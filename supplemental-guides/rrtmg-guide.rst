.. |br| raw:: html

   <br />

.. _rrtmg-guide:

##############################
RRTMG radiative transfer model
##############################

On this page we provide information about the coupling of GEOS-Chem
with the `RRTMG radiative transfer model
<http://rtweb.aer.com/rrtm_frame.html>`_ (by AER, Inc.).

.. _rrtmg-overview:

========
Overview
========

The GEOS-Chem model with online radiative transfer calculations
(referred to as GCRT) was developed to allow GEOS-Chem users to
produce gas and aerosol direct radiative effect (DRE) output for both
the longwave and shortwave. This alternative to offline coupling
allows better temporal resolution in the RT calculations and provides
a consistent platform for  GEOS-Chem users with the widely used
radiative transfer package `RRTMG
<http://rtweb.aer.com/rrtm_frame.html>`_.

Most of the added code is 'transparent', therefore this version of the
GEOS-Chem model can still be run with the radiation code switched
off. The optical properties are calculated at multiple wavelengths so
that the user is no longer restricted to 550 nm as default, so there
are associated changes regardless of whether the radiative code is
invoked. However, these cause negligible slowdown (the default model
Compiling with :command:`-DRRTMG=y` requires approximately double the
amount of RAM and takes between 40% and 100% longer depending on the
settings used.

.. _rrtmg-guide-authors:

=========================
Authors and collaborators
=========================

.. list-table::
   :header-rows: 1
   :align: center

   * - Author or Collaborator
     - Institution
   * - David Ridley
     - MIT (formerly)
   * - Colette Heald
     - ETH Zurich
   * - Steven Barrett
     - MIT (formerly)
   * - Karen Cady-Pereira
     - AER, Inc.
   * - Matthew Alvarado
     - AER, Inc.
   * - Sebastian Eastham
     - Imperial College London

.. _rrtmg-guide-notes:

===============
Important notes
===============

#. A menu for Radiation exists in :ref:`Settings in
   geoschem_config.yml <rrtmg-guide-running-gc-cfg>`.  This also includes a
   wavelength selection for optical depth output that is independent
   of whether RRTMG is switched on (up to three optical depths can be
   output in the HISTORY diagnostics). |br|
   |br|

#. A code folder, :code:`GeosRad/` is required for the RRTMG code. The
   module  :code:`rrtmg_rad_transfer_mod.F90` is the driver code
   (found in :code:`GeosCore/`) that interfaces with RRTMG. |br|
   |br|

#. The optics look-up tables are updated, containing multiple
   wavelengths and separated into files for each species
   (:file:`soot.dat`, :file:`so4.dat`, :file:`org.dat`,
   :file:`dust.dat`, :file:`ssa.dat`, :file:`ssc.dat`) that are stored
   in the Aerosol Optics folder (specified in
   :file:`geoschem_config.yml` here:

   .. code-block:: yaml

      aerosols:

        optics:
          input_dir: /path/to/Aerosol_Optics/folder

#. Surface albedo and emissivity climatologies have been generated
   and must be stored in the HEMCO data path(e.g.,
   :file:`ExtData/HEMCO/RRTMG/v2018-11`).

#. :ref:`Diagnostics <rrtmg-guide-running-history>` are available
   providing the change in radiative flux (DRE) for gases and aerosol,
   LW/SW, top of atmosphere (TOA) and surface, and clear-sky and
   all-sky conditions. These also include AOD, SSA, and asymmetry
   parameter for each aerosol species at the requested wavelengths.

.. _rrtmg-guide-running:

============================
Running GEOS-Chem with RRTMG
============================

_rrtmg-guide-running-species:

Available species
-----------------

The species available for output from the flux calculations are as follows:

.. list-table::
   :widths: 10 15 30
   :header-rows: 1

   * - #
     - Abbreviation
     - Species
   * - 0
     - BA
     - Baseline calculation
   * - 1
     - O₃
     - Ozone
   * - 2
     - ME
     - Methane
   * - 3
     - SU
     - Sulfate
   * - 4
     - NI
     - Nitrate
   * - 5
     - AM
     - Ammonium
   * - 6
     - BC
     - Black carbon
   * - 7
     - OA
     - Organic aerosol
   * - 8
     - SS
     - Sea salt
   * - 9
     - DU
     - Mineral dust
   * - 10
     - PM
     - All particulate matter
   * - 11
     - ST
     - Stratospheric aerosol

.. note::

   The radiative impacts of gases (ozone and methane) are relatively
   untested at this stage and should be interpreted with caution.

.. _rrtmg-guide-running-gc-cfg:

Settings in geoschem_config.yml
-------------------------------

For more information about these settings, please see the
`geoschem_config.yml chapter <https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/geoschem-config.html>`_
in our ReadTheDocs documentation.

.. code-block:: yaml

   #============================================================================
   # Timesteps settings
   #============================================================================
   timesteps:
     transport_timestep_in_s: 600
     chemistry_timestep_in_s: 1200
     radiation_timestep_in_s: 10800   # <=== timestep for RRTMG

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:
     .. etc ...

     rrtmg_rad_transfer_model:
       activate: true
       aod_wavelengths_in_nm:
         - 550
       longwave_fluxes: true
       shortwave_fluxes: true
       clear_sky_flux: true
       all_sky_flux: true
       fixed_dyn_heating: false
       seasonal_fdh: false
       read_dyn_heating: false
       co2_ppmv: 390.0

.. _rrtmg-guide-running-hco-cfg:

Settings in HEMCO_Config.rc
---------------------------

Several of the inputs to RRTMG are stored in netCDF format for input via
`HEMCO <https://hemco.readthedocs.io>`_. These include:

#. Diffuse surface albedos in visible and near-IR
#. Direct surface albedos in visible and near-IR
#. Surface emissivity in 16 different wavelength bands
#. Concentrations of CCl₄, CFC-11, CFC-12, CFC-22, CH₄, N₂O from TES in ppb

You will see lines similar to this in the :file:`HEMCO_Config.rc` file
that ships with the RRTMG simulation run directory:

.. code-block:: text

   #==============================================================================
   # --- Inputs for the RRTMG radiative transfer model ---
   #
   # NOTE: The 2 x 2.5 albedo fields and emissivity fields will produce
   # differences at the level of numerical noise when comparing output to
   # simulations from prior versions (esp. when running at 4 x 5 resolution).
   # You might see larger differences w/r/t prior versions for a few grid boxes
   # along the coastline of Antarctica, where the difference in resolution
   # and regridding will be more apparent in the sharp transition from ice to
   # ocean.  If this is a problem, you can use the data files at 4x5 resolution
   # for 4x5 RRTMG simulations.
   #
   # ALSO NOTE: The algorithm that HEMCO uses to select each time slice is
   # likely different than what was implemented when reading the old bpch
   # data from disk.  This can also cause differences when comparing to
   # prior versions.
   #==============================================================================
   (((RRTMG
   * MODIS_ALBDFNIR      $ROOT/RRTMG/v2018-11/modis_surf_albedo.2x25.nc     ALBDFNIR        2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_ALBDFVIS      $ROOT/RRTMG/v2018-11/modis_surf_albedo.2x25.nc     ALBDFVIS        2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_ALBDRNIR      $ROOT/RRTMG/v2018-11/modis_surf_albedo.2x25.nc     ALBDRNIR        2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_ALBDRVIS      $ROOT/RRTMG/v2018-11/modis_surf_albedo.2x25.nc     ALBDRVIS        2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_01 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band01  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_02 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band02  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_03 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band03  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_04 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band04  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_05 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band05  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_06 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band06  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_07 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band07  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_08 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band08  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_09 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band09  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_10 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band10  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_11 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band11  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_12 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band12  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_13 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band13  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_14 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band14  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_15 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band15  2002/1-12/1-31/0 C xy  1    * - 1 1
   * MODIS_EMISSIVITY_16 $ROOT/RRTMG/v2018-11/modis_emissivity.2x25.nc      RTEMISS_band16  2002/1-12/1-31/0 C xy  1    * - 1 1
   * TES_CLIM_CCL4  $ROOT/RRTMG/v2018-11/species_clim_profiles.2x25.nc      CCl4            2000/1/1/0       C xyz ppbv * - 1 1
   * TES_CLIM_CFC11 $ROOT/RRTMG/v2018-11/species_clim_profiles.2x25.nc      CFC11           2000/1/1/0       C xyz ppbv * - 1 1
   * TES_CLIM_CFC12 $ROOT/RRTMG/v2018-11/species_clim_profiles.2x25.nc      CFC12           2000/1/1/0       C xyz ppbv * - 1 1
   * TES_CLIM_CFC22 $ROOT/RRTMG/v2018-11/species_clim_profiles.2x25.nc      CFC22           2000/1/1/0       C xyz ppbv * - 1 1
   * TES_CLIM_CH4   $ROOT/RRTMG/v2018-11/species_clim_profiles.2x25.nc      CH4             2000/1/1/0       C xyz ppbv * - 1 1
   * TES_CLIM_N2O   $ROOT/RRTMG/v2018-11/species_clim_profiles.2x25.nc      N2O             2000/1/1/0       C xyz ppbv * - 1 1
   )))RRTMG

The data files are located in the :file:`HEMCO/RRTMG/v2018-11` folder on our
`GEOS-Chem Input data portal <https://geos-chem.readthedocs.io/en/stable/geos-chem-shared-docs/doc/gcid-data-on-aws.html>`_.


.. _rrtmg-guide-running-history:

Settings in HISTORY.rc
----------------------

.. code-block:: text

   #==============================================================================
   # %%%%% THE RRTMG COLLECTION %%%%%
   #
   # Outputs for different species from the RRTMG radiative transfer model:
   # (See http://wiki.geos-chem.org/Coupling_GEOS-Chem_with_RRTMG)
   #
   #    0=BA (Baseline    )  1=O3 (Ozone          )   2=ME (Methane   )
   #    3=SU (Sulfate     )  4=NI (Nitrate        )   5=AM (Ammonium  )
   #    6=BC (Black carbon)  7=OA (Organic aerosol)   8=SS (Sea Salt  )
   #    9=DU (Mineral dust) 10=PM (All part. matter) 12=ST (Strat aer., UCX only)
   #
   # NOTES:
   # (1) Only request diagnostics you need to reduce the overall run time.
   # (2) The ?RRTMG? wildcard includes all output except ST (strat aerosols).
   #     However, if ST is included explicitly for one diagnostic then it
   #     will be included for all others that use the wildcard.
   # (3) Only enable ST if running with UCX.
   # (4) Optics diagnostics have a reduced set of output species (no BASE, O3, ME)
   #==============================================================================
     RRTMG.template:             '%y4%m2%d2_%h2%n2z.nc4',
     RRTMG.frequency:            00000000 010000
     RRTMG.duration:             00000000 010000
     RRTMG.mode:                 'time-averaged'
     RRTMG.fields:               'RadClrSkyLWSurf_BASE   ',
                                 'RadClrSkyLWSurf_O3     ',
                                 'RadClrSkyLWSurf_ME     ',
                                 'RadClrSkyLWSurf_SU     ',
                                 'RadClrSkyLWSurf_NI     ',
                                 'RadClrSkyLWSurf_AM     ',
                                 'RadClrSkyLWSurf_BC     ',
                                 'RadClrSkyLWSurf_OA     ',
                                 'RadClrSkyLWSurf_SS     ',
                                 'RadClrSkyLWSurf_DU     ',
                                 'RadClrSkyLWSurf_PM     ',
                                 #'RadClrSkyLWSurf_ST     ',
                                 'RadAllSkyLWSurf_?RRTMG?',
                                 'RadClrSkySWSurf_?RRTMG?',
                                 'RadAllSkySWSurf_?RRTMG?',
                                 'RadClrSkyLWTOA_?RRTMG? ',
                                 'RadAllSkyLWTOA_?RRTMG? ',
                                 'RadClrSkySWTOA_?RRTMG? ',
                                 'RadAllSkySWTOA_?RRTMG? ',
                                 'RadAODWL1_SU          ',
                                 'RadAODWL1_NI          ',
                                 'RadAODWL1_AM          ',
                                 'RadAODWL1_BC          ',
                                 'RadAODWL1_OA          ',
                                 'RadAODWL1_SS          ',
                                 'RadAODWL1_DU          ',
                                 'RadAODWL1_PM          ',
                                 #'RadAODWL1_ST          ',
                                 'RadSSAWL1_SU          ',
                                 'RadSSAWL1_NI          ',
                                 'RadSSAWL1_AM          ',
                                 'RadSSAWL1_BC          ',
                                 'RadSSAWL1_OA          ',
                                 'RadSSAWL1_SS          ',
                                 'RadSSAWL1_DU          ',
                                 'RadSSAWL1_PM          ',
                                 #'RadSSAWL1_ST          ',
                                 'RadAsymWL1_SU         ',
                                 'RadAsymWL1_NI         ',
                                 'RadAsymWL1_AM         ',
                                 'RadAsymWL1_BC         ',
                                 'RadAsymWL1_OA         ',
                                 'RadAsymWL1_SS         ',
                                 'RadAsymWL1_DU         ',
                                 'RadAsymWL1_PM         ',
                                 #'RadAsymWL1_ST         ',


.. _rrtmg-guide-references:

==========
References
==========

#. Heald, C.L., D.A. Ridley, J.H. Kroll, S.R.H. Barrett,
   K.E. Cady-Pereira, M.J. Alvarado, C.D. Holmes, *Beyond Direct
   Radiative Forcing: The Case for Characterizing the Direct
   Radiative Effect of Aerosols*, Atmos. Chem. Phys., 14,
   5513-5527,  doi:10.5194/acp-14-5513-2014, 2014.
   (`Article
   <http://www.atmos-chem-phys.net/14/5513/2014/acp-14-5513-2014.html>`_)
