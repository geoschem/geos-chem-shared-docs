.. _customguide:

###########################################
Customize simulations with research options
###########################################

Most of the time you will want to use the "out-of-the-box" options
settings in your GEOS-Chem simulations, as these are the settings that
are used in GEOS-Chem benchmark simulations.  But depending on your
research needs, you may wish to use options that are not turned on by
default.  In this Guide we will show you how you can select these
**research options** by editing the various GEOS-Chem and HEMCO
configuration files.

.. _customguide-aer:

========
Aerosols
========

.. _customguide-aer-mp:

Aerosol microphysics
--------------------

GEOS-Chem incorporates two different aerosol microphysics schemes: APM
(:cite:t:`Yu_and_Luo_2009`) and TOMAS
(:cite:t:`Trivitayanurak_et_al._2008`) as compile-time options for the
full-chemistry simulation.  Both APM and TOMAS are deactivated by
default due to the extra computational overhead that these
microphysics schemes require.

Follow the steps below to activate either APM or TOMAS microphysics in
your full-chemistry simulation.

APM
~~~

#. Create a run directory for the Full Chemistry simulation with APM
   as the extra simulation option.
#. Navigate to the :file:`build` folder within the run directory.
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DAPM=y
      $ make -j
      $ make install

TOMAS
~~~~~

#. Create a run directory for the Full Chemistry simulation with TOMAS
   as the extra simulation option.
#. Navigate to the :file:`build` folder within the run directory.
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DTOMAS=y -DTOMAS_BINS=15 -DBPCH_DIAG=y
      $ make -j
      $ make install

This will create a GEOS-Chem executable for the TOMAS15 (15 size bins)
simulation.  To generate an executable for the TOMAS40 (40 size-bins)
simulation, replace :code:`-DTOMAS_BINS=15` with
:code:`-DTOMAS_BINS=40` in the :literal:`cmake` step above.

.. _customguide-chem:

=========
Chemistry
=========

.. _customguide-chem-kpp:

Adaptive Rosenbrock solver with mechanism auto-reduction
--------------------------------------------------------

In :cite:t:`Lin_et_al._2023`, the authors introduce an `adaptive
Rosenbrock solver with on-the-fly mechanism reduction
<https://kpp.readthedocs.io/en/stable/tech_info/07_numerical_methods.html#rosenbrock-with-mechanism-auto-reduction>`_
in `The Kinetic PreProcessor (KPP) <https://kpp.readthedocs.io>`_
version 3.0.0 and later.  While this adaptive solver is available for all
GEOS-Chem simulations that use the :literal:`fullchem` simulation, it
is disabled by default.

To activate the adaptive Rosenbrock solver with mechanism
auto-reduction, edit the line of :file:`geoschem_config.yml` indicated
below:

.. code-block:: yaml

   chemistry:
     activate: true
     # ... Previous sub-sections omitted
     autoreduce_solver:
       activate: false   # <== true activates the adaptive Rosenbrock solver
       use_target_threshold:
         activate: true
         oh_tuning_factor: 0.00005
         no2_tuning_factor: 0.0001
       use_absolute_threshold:
         scale_by_pressure: true
         absolute_threshold: 100.0
       keep_halogens_active: false
       append_in_internal_timestep: false

Please see the :cite:t:`Lin_et_al._2023` reference for a detailed
explanation of the other adaptive Rosenbrock solver options.

.. _customguide-chem-mech:

Alternate chemistry mechanisms
------------------------------

GEOS-Chem is compiled "out-of-the-box" with KPP-generated solver code
for the :literal:`fullchem` mechanism.  But you must manually specify
the mechanism name at configuration time for the following instances:

Carbon mechanism
~~~~~~~~~~~~~~~~

Follow these steps to build an executable with the :literal:`carbon`
mechanism:

#. Create a run directory for the Carbon simulation
#. Navigate to the :file:`build` folder within the run directory.
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DMECH=carbon
      $ make -j
      $ make install

Custom full-chemistry mechanism
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

We recommend that you use the :literal:`custom` mechanism instead of
directly modifying the :literal:`fullchem` mechanism.  The
:literal:`custom` mechanism is a copy of :literal:`fullchem`, but the
KPP solver code will be generated in the :file:`KPP/custom`
folder instead of in :file:`KPP/fullchem`.  This lets you keep the
:literal:`fullchem` folder untouched.

Follow these steps:

#. Create a run directory for the full-chemistry simulation (whichever
   configuration you need)
#. Navigate to the :file:`build` folder within the run directory.
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DMECH=custom
      $ make -j
      $ make install

Hg mechanism
~~~~~~~~~~~~
Follow these steps to build an executable with the :literal:`Hg` (mercury)
mechanism:

#. Create a run directory for the Hg simulation.
#. Navigate to the :file:`build` folder within the run directory.\
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DMECH=Hg
      $ make -j
      $ make install

.. _customguide-chem-ho2:

HO2 heterogeneous chemistry reaction probability
------------------------------------------------

You may update the value of :math:`\gamma_{HO2}` (reaction probability for
uptake of HO2 in heterogeneous chemistry) used in your simulations.
Edit the line of :file:`geoschem_config.yml` indicated below:

.. code-block:: yaml

   chemistry:
     activate: true
     # ... Preceding sections omitted ...
     gamma_HO2: 0.2   # <=== add new value here

.. _customguide-chem-pasv:

Passive species
---------------

TBD

.. _customguide-diag:

===========
Diagnostics
===========

GEOS-Chem and HEMCO diagnostics
-------------------------------

Please see our `Diagnostics reference
<https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/diagnostics.html>`_
chapter for an overview of how to archive diagnostics from GEOS-Chem
and HEMCO.

RRTMG radiative transfer diagnostics
------------------------------------
You can use the RRTMG radiative transfer model to archive radiative
forcing fluxes to the :literal:`GeosRad` History diagnostic
collection.  RRTMG is implemented as a compile-time option due to the
extra computational overhead that it incurs.

To activate RRTMG, follow these steps:

#. Create a run directory for the Full Chemistry simulation, with
   extra option RRTMG.
#. Navigate to the :file:`build` folder within the run directory.
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DRRTMG=y
      $ make -j
      $ make install

Then also make sure to request the radiative forcing flux diagnostics
that you wish to archive in the :literal:`HISTORY.rc` file.

.. _customguide-emis:

=========
Emissions
=========

.. _customguide-emis-offline:

Offline vs. online emissions
----------------------------

Emission inventories sometimes include dynamic source types and
nonlinear scale factors that have functional dependencies on local
environmental variables such as wind speed or temperature, which are
best calculated online during execution of the model. HEMCO includes a
suite of additional modules (aka `HEMCO extensions
<https://hemco.readthedocs.io/en/stable/hco-ref-guide/extensions.html>`_)
that perform **online emissions** ccalculations for a variety of
sources.

Some types of emissions, such as especially mineral dust and sea salt
aerosols, biogenic emissions, and lightning, are highly sensitive to 
meteorological variables such as wind speed and temperature.  Because
the meteorological inputs are regridded from their native resolution
to the model grid, this can cause emissions computed with
fine-resolution meteorology to significantly differ from emissions 
computed with coarse-resolution meteorology.

In order to provide more consistency in the computed emissions, we now
make available for download **offline emissions**; that is, emissions
that have been pre-computed with HEMCO standalone simulations using
fine-resolution meteorological inputs.  These offline emissions then
are regridded conservatively within GEOS-Chem simulations such that
the total mass of species emitted is constant regardless of the model
grid resolution.

All GEOS-Chem full-chemistry simulations (except benchmark
simulations) use online emissions.

Instructions TBD

.. _customguide-chem-ssdb:

Sea salt debromination
----------------------

In `L. Zhu et al [2018]
<https://acp.copernicus.org/articles/19/6497/2019/>`_, the authors
present a mechanistic description of sea salt aerosol debromination.
This option was originally enabled by in GEOS-Chem 13.4.0, but
was then implemented as a user option (disabled by default) due to the
impact it had on ozone concentrations, especially at southern latitudes.

Further chemistry updates to GEOS-Chem have allowed us to re-activate
sea-salt debromination as the default option in GEOS-Chem 14.2.0 and
later versions.  If you wish to disable sea salt debromination in your
simulations, edit the line in :file:`HEMCO_Config.rc` indicated below.

.. code-block:: kconfig

   107     SeaSalt                : on  SALA/SALC/SALACL/SALCCL/SALAAL/SALCAL/BrSALA/BrSALC/MOPO/MOPI
       # ... Preceding options omitted ...
       --> Model sea salt Br-     :       true    # <== false deactivates sea salt debromination
       --> Br- mass ratio         :       2.11e-3

.. _chemguide-phot:

==========
Photolysis
==========

.. _customguide-phot-np:

Particulate nitrate photolysis
------------------------------
A study by `V. Shah et al [2023]
<https://doi.org/10.5194/acp-23-1227-2023>`_ showed that particulate
nitrate photolysis increases GEOS-Chem modeled ozone concentrations by
up to 5 ppbv in the free troposphere in northern extratropical
regions.  This helps to correct a low bias with respect to
observations.

Particulate nitrate photolysis is turned on by default in GEOS-Chem
14.2.0 and later versions.  You may disable this option by editing
the line in :file:`geoschem_config.yml` indicated below:

.. code-block:: yaml

   photolysis:
     activate: true
     # .. preceding sub-sections omitted ...
     photolyze_nitrate_aerosol:
       activate: true   # <=== false deactivates nitrate photolysis
       NITs_Jscale_JHNO3: 100.0
       NIT_Jscale_JHNO2: 100.0
       percent_channel_A_HONO: 66.667
       percent_channel_B_NO2: 33.333

You can also edit the other nitrate photolysis parameters by changing
the appropriate lines above.  See the Shah et al [2023] reference for
more information.

.. _customguide-wetd:

==============
Wet deposition
==============

.. _customguide-wetd-luo:

Luo et al 2020 wet deposition scheme
------------------------------------

In :cite:t:`Luo_et_al._2020`, the authors introduced an updated wet
deposition parameterization, which is now incorporated into GEOS-Chem as a
compile-time option.  Follow these steps to activate the Luo et al
2020 wetdep scheme in your GEOS-Chem simulations.

#. Create a run directory for the type of simulation that you wish to
   use.

   - CAVEAT: Make sure your simulation uses at least one species that
     can be wet-scavenged.

#. Navigate to the :file:`build` folder within the run directory.
#. Then type the following:

   .. code-block:: console

      $ cmake .. -DLUO_WETDEP=y
      $ make -j
      $ make install
