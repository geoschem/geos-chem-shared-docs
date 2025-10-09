.. |br| raw:: html

   <br />

.. _cfg-gc-yml:

###################
geoschem_config.yml
###################

Starting with GEOS-Chem 14.0.0, the :file:`input.geos` configuration
file (plain text) has been replaced with by the
:file:`geoschem_config.yml` file.  This file is in `YAML
<https://yaml.org>`_ format, which is a text-based markup syntax used
for representing dictionary-like data structures.

The :file:`geoschem_config.yml` file contains several sections.  Only
The sections relevant to a given type of simulation are present.  For
example, :option:`fullchem` simulation options (such as aerosol
settings and photolysis settings) are omitted from the
:file:`geoschem_config.yml` file for the :option:`CH4` simulation.

.. note::

   Settings that are not relevant to GCHP will be excluded from the
   :file:`geoschem_config.yml` file that ships with the GCHP run
   directory.  We will note these excluded settings below.  All other
   settings in :file:`geoschem_config.yml` will be treated in
   in the same way as in `GEOS-Chem Classic
   <https://geos-chem.readthedocs.io>`_.

.. _gc-yml-simulation:

===================
Simulation settings
===================

.. code-block:: yaml

   #============================================================================
   # Simulation settings
   #============================================================================
   simulation:
     name: fullchem
     start_date: [20190701, 000000]
     end_date: [20190801, 000000]
     root_data_dir: /path/to/ExtData
     met_field: MERRA2
     species_database_file: ./species_database.yml
     species_metadata_output_file: OutputDir/geoschem_species_metadata.yml
     verbose:
       activate: false
       on_cores: root       # Allowed values: root all
     use_gcclassic_timers: false
     read_restart_as_real8: true

The :command:`simulation` section contains general simulation options:

name
----

Specifies the type of GEOS-Chem simulation.  Accepted
values are

.. option:: fullchem

   :ref:`Full-chemistry simulation <fullchem-sim>` of Ox, NOx, VOCs,
   halogens, and aerosols.

.. option:: aerosol

   :ref:`aerosol-sim`.

.. option:: carbon

   :ref:`carbon-sim` (CH4-CO-CO2-OCS), implemented as a KPP mechanism
   (cf :cite:t:`Bukosa_et_al._2023`).

   You must configure your build with with
   :literal:`-DMECH=carbon` in order to use this simulation. For
   more information, please see:

   - `GEOS-Chem Classic configuration instructions
     <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/compile-cmake.html>`_,
     or
   - `GCHP configuration instructions
     <https://gchp.readthedocs.io/en/stable/user-guide/configuration-files.html>`_

.. option:: CH4

   `Methane simulation <http://wiki.geos-chem.org/CH4_simulation>`_.

   This simulation will eventually be superseded by the
   :ref:carbon-sim`.

.. option:: CO2

   `Carbon dioxide simulation <http://wiki.geos-chem.org/CO2_simulation>`_.

   This simulation will eventually be superseded by the
   :ref:`carbon-sim`.

.. option:: Hg

   :ref:`hg-sim`.

   You must configure your build with with
   :literal:`-DMECH=Hg` in order to use this simulation. For
   more information, please see:

   - `GEOS-Chem Classic configuration instructions
     <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/compile-cmake.html>`_,
     or
   - `GCHP configuration instructions
     <https://gchp.readthedocs.io/en/stable/user-guide/configuration-files.html>`_

.. option:: POPs

   `Persistent organic pollutants (aka POPs) simulation
   <http://wiki.geos-chem.org/POPs simulation>`_.

   .. attention::

	 The POPs simulation is currently stale.  We look to members
	 of the GEOS-Chem user community take the lead on updating
	 this simulation.

.. option:: tagCH4

   `Methane simulation
   <http://wiki.geos-chem.org/CH4_simulation>`_ with species
   tagged by geographic region or other criteria.

   This simulation will eventually be superseded by the
   :option:`carbon` simulation.

.. option:: tagCO

   Carbon dioxide simulation, with species
   tagged by geographic region and other criteria.

   This simulation will eventually be superseded by the
   :ref:`carbon-sim`.

.. option:: tagO3

   :ref:`tago3-sim` (using specified production and loss rates),
   with species tagged by geographical region.

.. option:: TransportTracers

   :ref:`transport-sim`, with both radionuclide and passive_species.
   Useful for evaluating model transport, convection, and/or wet
   deposition.

.. option:: metals

   :ref:`Trace metals simulation <metals-sim>`.

.. _gc-yml-simulation-start:

start_date
----------

.. note::

   This option is omitted for GCHP. The simulation start date
   is specified in the :file:`CAP.rc` and :file:`cap_restart`
   files.

Specifies the starting date and time of the simulation in list
notation :literal:`[YYYYMMDD, hhmmss]`.

.. _gc-yml-simulation-end:

end_date
--------

.. note::

   This option is omitted for GCHP. Duration is specified in the
   :file:`cap_restart` file.

Specifies the ending date and time of the simulation in list
notation :literal:`[YYYYMMDD, hhmmss]`.

.. _gc-yml-simulation-root:

root_data_dir
-------------

.. note::

   This option is omitted for GCHP. All data paths (with the
   exception of the aerosol optics and photolysis paths) are
   specified in the :file:`ExtData.rc` file.

Path to the root data directory.  All of the data that GEOS-Chem
Classic reads must be located in subfolders of this directory.

.. _gc-yml-simulation-met:

met_field
---------

.. note::

   This option is omitted for GCHP. Met field source is described
   in file paths of the in the :file:`ExtData.rc` file.

Name of the meteorology product that will be used to drive
GEOS-Chem.  Accepted values are:

.. option:: MERRA2

   The `MERRA-2 <https://wiki.geos-chem.org/MERRA-2>`_ meteorology
   product from NASA/GMAO.  MERRA-2 is a stable reanalysis product,
   and extends from approximately 1980 to present.
   **(Recommended option)**

.. option:: GEOS-FP

   The `GEOS-FP <https://wiki.geos-chem.org/MERRA-2>`_ meteorology
   product from NASA/GMAO.  GEOS-FP is an operational data product
   and, unlike MERRA-2, periodically receives science updates.

.. option:: GEOS-IT

   The `GEOS-IT <https://wiki.geos-chem.org/GEOS-IT>`_ meteorology
   product from NASA/GMAO.

.. option:: GCAP2

   The GCAP-2 meteorology product, archived from the GISS-2 GCM.
   GCAP-2 has hundreds of years of data available, making it useful
   for simulations of historical climate.

species_database_file
---------------------

Path to the :ref:`GEOS-Chem Species Database <spcguide>` file. This
is stored in the run directory file :file:`./species_database.yml`.
You should not have to edit this setting.

species_metadata_output_file
----------------------------

Path to the :file:`geoschem-species-metadata.yml` file.  This file
contains echoback of information from :ref:`species_database.yml
<spcguide>`, but only for species that are defined in this
simulation (instead of all possible species).  This facilitates
interfacing GEOS-Chem with external models such as CESM.

verbose
-------

Menu controlling verbose printout. Starting with GEOS-Chem 14.2.0
and HEMCO 3.7.0, most informational printouts are now deactivated
by default.  You may choose to activate them (e.g. for debugging
and/or testing) with the options below:

.. option:: activate

   .. option:: true

      Activates writing extra informational printout to the screen
      and/or log file.

   .. option:: false

      Deactivates writing extra informational printout.  This is the
      default setting.

.. option:: on_cores

   Specify on which computational cores informational printout
   should be done.

   .. option:: root

	 Print extra informational output only on the root core.  Use this
	 setting for GEOS-Chem Classic.

   .. option:: all

      Print extra informational output on all cores.  Consider
      using this when using GEOS-Chem as GCHP, or in MPI-based
      external models (NASA GEOS, CESM, etc.).

use_gcclassic_timers
--------------------

.. note::

   This setting is omitted for GCHP, as the MAPL library provides
   all timer functionality.

.. option:: false

   Deactivates the GEOS-Chem Classic timers.  This is the default
   setting.

.. option:: true

   Activates the GEOS-Chem Classic timers.  Information about how
   long each component of GEOS-Chem Classic took to execute will be
   printed to the screen and/or the `log file <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/log-files.html#geos-chem-and-hemco-log-file>`_
   The same information will also be written in JSON format to a
   file named `gcclassic_timers.json
   <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/log-files.html#timers-log-file>`_.

   You will only really need to activate the GEOS-Chem Classic
   timers if you are running a benchmark simulation or if you are
   doing performance testing.

read_restart_as_real8:
----------------------

.. note::

   This setting is omitted for GCHP, as the MAPL library provides
   all disk I/O functionality and has the ability to read restart data
   as :code:`REAL*8`.

Option controlling how the GEOS-Chem Classic restart file will be read.

.. option:: false

   The GEOS-Chem Classic  restart file will be read by HEMCO (which
   reads all data as :code:`REAL*4`).  This is the default option.  You
   must use this option if the resolution of the restart file does not
   match the simulation grid resolution.

.. option:: true

   The restart file will be read directly by GEOS-Chem Classic as
   :code:`REAL*8`.  Use this option when the resolution of your
   restart file matches the simulation grid resolution, and when mass
   conservation needs to be strictly enforced.

.. _cfg-gc-yml-grid:

=============
Grid settings
=============

.. note::

   Grid settings are omitted for GCHP.  Grid specifications are
   contained in the :file:`GCHP.rc` file instead.

.. code-block:: YAML

   #============================================================================
   # Grid settings
   #============================================================================
   grid:
     resolution: 4.0x5.0
     number_of_levels: 72
     longitude:
       range: [-180.0, 180.0]
       center_at_180: true
     latitude:
       range: [-90.0, 90.0]
       half_size_polar_boxes: true
     nested_grid_simulation:
       activate: true
       buffer_zone_NSEW: [0, 0, 0, 0]

The :command:`grid` section contains settings that define the grid used
by GEOS-Chem Classic:

resolution
----------

Specifies the horizontal resolution of the grid.  Accepted values are:

.. option:: 4.0x5.0

   The GEOS-Chem Classic :ref:`gcc-hgrids-global-4x5`.

.. option:: 2.0x2.5

   The GEOS-Chem Classic :ref:`gcc-hgrids-global-2x25`.

.. option:: 0.5x0.625

   The GEOS-Chem Classic :math:`0.5^{\circ}{\times}0.625^{\circ}`
   grid.  May be used for global or :ref:`nested-grid simulations
   <nestgrid-guide>` with :option:`MERRA2` or :option:`GEOS-IT`
   meteorology.

.. option:: 0.25x0.3125

   The GEOS-Chem Classic :math:`0.25^{\circ}{\times}0.3125^{\circ}`
   grid.  May be used for global or :ref:`nested-grid simulations
   <nestgrid-guide>` with :option:`GEOS-FP` meteorology.

.. option:: 0.125x0.15625

   The GEOS-Chem Classic global
   :math:`0.125^{\circ}{\times}0.15625^{\circ}` grid.  May be used for
   global or :ref:`nested-grid simulations <nestgrid-guide>` with
   :option:`GEOS-FP` meteorology.


number_of_levels
----------------

Number of vertical levels to use in the simulation.  Accepted
values are:

.. option:: 72

   Use 72 vertical levels.  This is the native vertical resolution
   of :option:`MERRA2`, :option:`GEOS-FP`, and :option:`GEOS-IT`.

.. option:: 47

   Use 47 vertical levels (for :option:`MERRA2`, :option:`GEOS-FP`,
   and :option:`GEOS-IT`).

.. option:: 40

   Use 40 vertical levels (for :option:`GCAP2`).

longitude
---------

.. option:: range

   The minimum and maximum longitude values (grid box edges),
   specified in list format.

.. option:: center_at_180

   .. option:: true

      Westernmost grid boxes are centered at :math:`-180^{\circ}`
      longitude (the International Date Line).  This is the default
      for :option:`MERRA2`, :option:`GEOS-FP`, and
      :option:`GEOS-IT` meteorology.

   .. option:: false

      Westernmost grid boxes have their western edges at
      :math:`-180^{\circ}` longitude.  This is the default setting for
      the :option:`GCAP2` grid.

latitude
--------

.. option:: range

   The minimum and maximum latitude values (grid box edges),
   specified in list format.

.. option:: use_halfpolar_boxes

   .. option:: true

      Northernmost and southernmost grid boxes will be
      :math:`\frac{1}{2}` the extent of other grid boxes.  This the
      default for :option:`MERRA2`, :option:`GEOS-FP`, and
      :option:`GEOS-IT` meteorology.

   .. option:: false

      All grid boxes will have the same extent in latitude. This is
      the default for :option:`GCAP2` meteorology.

nested_grid_simulation
----------------------

.. option:: activate

   .. option:: true

      Indicates this indicates that the simulation will use a
      sub-domain of the horizontal grid.

   .. option:: false

      Indicates that the simulation will use the entire global grid
      extent.

.. option:: buffer_zone_NSEW

   Specifies the nested grid latitude offsets (# of grid boxes) in list
   format :literal:`[N-offset, S-offset, E-offset, W-offset]`.  These
   offsets are used to define an inner window region in which
   transport is actually done (aka the "transport window").  This
   "transport window" is always smaller than the actual size of the
   nested grid region in order to properly account for the boundary
   conditions.

- For global simulations, use: :literal:`[0, 0, 0, 0]`.
- For nested-grid simulations, we recommend using: :literal:`[3, 3, 3, 3]`.

.. _cfg-gc-yml-timesteps:

==================
Timesteps settings
==================

.. note::

   Timesteps settings are omitted for GCHP.  Timesteps are specified
   in the :file:`CAP.rc` file.

.. code-block:: YAML

   #============================================================================
   # Timesteps settings
   #============================================================================
   timesteps:
     transport_timestep_in_s: 600
     chemistry_timestep_in_s: 1200
     radiation_timestep_in_s: 10800

The :command:`timesteps` section specifies the frequency at which
various GEOS-Chem operations occur.

The table below contains our recommended GEOS-Chem Classic timestep
settings.

.. list-table::
   :header-rows: 1

   * - GEOS-Chem Classic Resolution
     - Transport
     - Chemistry
   * - :math:`4^{\circ}{\times}5^{\circ}`
     - 600s (10m)
     - 1200s (20m)
   * -  :math:`2^{\circ}{\times}2.5^{\circ}`
     - 600s (10m)
     - 1200s (20m)
   * -  :math:`0.5^{\circ}{\times}0.625^{\circ}`
     - 300s (5m)
     - 600s (10m)
   * -  :math:`0.25^{\circ}{\times}0.3125^{\circ}`
     - 300s (5m)
     - 600s (10m)
   * - :math:`0.125^{\circ}{\times}0.15625^{\circ}`
     - 150s (2.5m)
     - 300s (5m)

The `Courant limit
<https://en.wikipedia.org/wiki/Courant%E2%80%93Friedrichs%E2%80%93Lewy_condition>`_
on the latitude-longitude grid constrains the choice of transport
timestep for a given horizontal resolution.  We choose a chemistry timestep that is
double the transport timestep (i.e.
`Strang operator splitting
<https://hplgit.github.io/fdm-book/doc/pub/book/sphinx/._book018.html#strang-splitting-for-odes>`_).

.. note::

   GCHP, which uses the FVdycore advection scheme on the cubed-sphere grid,
   does not have similar restrictions for timesteps.

See :cite:t:`Philip_et_al._2016` for a comprehensive study on
GEOS-Chem timesteps.  For some practical tips on speeding up your
simulations, see our `Speeding up GEOS-Chem Classic simulations
<https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/run-speedup.html>`_
guide.

transport_timestep_in_s
-----------------------

Specifies the "heartbeat" timestep of GEOS-Chem..  This is
the frequency at which transport, cloud convection, PBL mixing, and
wet deposition will be done.

chemistry_timestep_in_s
-----------------------

Specifies the frequency at which chemistry and emissions will be
done.

radiation_timestep_in_s
-----------------------

Specifies the frequency at which the `RRTMG
<http://wiki.geos-chem.org/Coupling_GEOS-Chem_with_RRTMG>`_ radiative
transfer model will be called (valid for :option:`fullchem`
simulations only).  We recommend using a timestep of 10800s (3h),
as the RRTMG calculations are computationally intensive.

.. _cfg-gc-yml-operations-chemistry:

=========
Chemistry
=========

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     chemistry:
       activate: true
       linear_chemistry_aloft:
         activate: true
         use_linoz_for_O3: true
       active_strat_H2O:
         activate: true
         use_static_bnd_cond: true
       gamma_HO2: 0.2
       autoreduce_solver:
         activate: false
         use_target_threshold:
           activate: true
           oh_tuning_factor: 0.00005
           no2_tuning_factor: 0.0001
         use_absolute_threshold:
           scale_by_pressure: true
           absolute_threshold: 100.0
         keep_halogens_active: false
         append_in_internal_timestep: false

         # ... following sub-sections omitted ...

The :command:`operations:chemistry` section contains settings for chemistry:

activate
--------

.. option:: true

   Activates chemistry in GEOS-Chem.  This is the default setting.

.. option:: false

   Deactivates chemistry in GEOS-Chem.

linear_chemistry_aloft
----------------------

Determines how linearized chemistry will be applied in the
stratosphere and/or mesosphere.  These apply only to
:option:`fullchem` simulations.

.. option:: activate

   .. option:: true

      Activates linearized stratospheric chemistry in the stratosphere
      and/or mesosphere.  This is the default setting.

   .. option:: false

      Deactivates linearized stratospheric chemistry in the
      stratosphere and/or mesosphere.

   .. option:: use_linoz_for_O3

      .. option:: true

         Activates `Linoz stratospheric ozone chemistry
         <http://wiki.geos-chem.org/Linoz_stratospheric_ozone_chemistry>`_
         will be used.  This is the default setting.

      .. option:: false

         Activates Synoz (i.e. a synthetic flux of ozone across the
	 tropopause).

active_strat_H2O
----------------

Determines if water vapor as modeled by GEOS-Chem will be
allowed to influence humidity fields.  These apply only to
:option:`fullchem` simulations.

.. option:: activate

   .. option:: true

      Allows the H2O species in GEOS-Chem to influence specific
      humidity and relative humidity.  This is the default setting.

   .. option:: false

      Prevents the H2O species in GEOS-Chem to influence specific
      humidity and relative humidity.

.. option:: use_static_bnd_cond

   .. option:: true

      Uses a static boundary condition.  This is the default setting.

   .. option:: false

      Does not use a static boundary condition.

gamma_HO2
---------

Specifies :math:`\gamma`, the uptake coefficient for :math:`HO_2`
heterogeneous chemistry.  Recommended value: :literal:`0.2`.

autoreduce_solver
-----------------

Menu for controlling the adaptive mechanism auto-reduction feature,
which is available in `KPP
3.0.0. <https://kpp.readthedocs.io/en/3.0.0/>`_ and later
versions. See :cite:t:`Lin_et_al._2023` for details.

.. option:: activate

   .. option:: true

      Integrates the chemistry mechanism using the Rosenbrock method
      with the adaptive auto-reduction feature.

   .. option:: false

      Integrates the chemistry mechanism using the traditional
      Rosenbrock method.  This is the default setting.

.. option:: use_target_threshold

   Contains options for defining :math:`\partial` (the partitioning
   threshold between "fast" and "slow" species") by considering the
   production and loss of key species (OH for daytime, NO2 for
   nighttime).

   .. option:: activate

      .. option:: true

          Uses OH and NO2 to determine :math:`\partial`.  This is
          the default setting.

      .. option:: false

         Skips computation of :math:`\partial`.

   .. option:: oh_tuning_factor

      Specifies :math:`{\alpha}_{OH}`, which is used to compute
      :math:`\partial`.

   .. option:: no2 tuning factor

      Specifies :math:`{\alpha}_{NO2}`, which is used to compute
      :math:`\partial`.

use_absolute_threshold
----------------------

Contains options for setting an absolute threshold
:math:`\partial` that may be weighted by pressure.

.. option:: scale_by_pressure

   .. option:: true

      Activates using a pressure-dependent method to determine
      :math:`\partial`.

   .. option:: false

      Deactivates using a pressure-dependent method to determine
      :math:`\partial`.

.. option:: absolute_threshold

   The absolute partitioning threshold :math:`\partial`.

   If :option:`scale_by_pressure` is :literal:`true,` and
   :envvar:`use_target_threshold:activate` is :literal:`false`, the
   value for :math:`\partial` specified here will be scaled by the
   ratio :math:`P / P_{sfc}`. where :math:`P` is the grid box pressure
   and :math:`P_{sfc}` is the surface pressure for the column.

keep_halogens_active
--------------------

.. option:: true

   All halogen species will be considered "fast". This may be
   necessary in order to obtain realistic results for ozone and
   other important species.  This is the default setting.

.. option:: false

   Halogen species will be determined as "slow" or "fast" depending
   on the partitioning threshold :math:`\partial`.

append_in_internal_timestep
---------------------------

.. option:: true

   Any "slow" species that later become "fast" will be appended to
   the list of "fast" species.

.. option:: false

   Any "slow" species that later become  "fast" will NOT be
   appended to the list of "fast" species.

.. _cfg-gc-yml-operations-convection:

==========
Convection
==========

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     convection:
       activate: true

     # ... following sub-sections omitted ...

The :command:`operations:convection` section contains settings for
`cloud convection <http://wiki.geos-chem.org/Cloud_convection>`_:

activate
--------

.. option:: true

   Activates cloud convection in GEOS-Chem

.. option:: false

   Deactivates cloud convection in GEOS-Chem

.. _cfg-gc-yml-operations-drydep:

==============
Dry deposition
==============

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     dry_deposition:
       activate: true
       CO2_effect:
         activate: false
         CO2_level: 600.0
         reference_CO2_level: 380.0
       diag_alt_above_sfc_in_m: 10

     # ... following sub-sections omitted ...

The :literal:`operations:dry_deposition` section contains settings that
for `dry deposition <http://wiki.geos-chem.org/Dry_deposition>`_:

activate
--------

.. option:: true

   Activates dry deposition in GEOS-Chem.

.. option:: false

   Deactivates dry deposition in GEOS-Chem.

CO2_effect
----------

This sub-section contains options for applying the
`simple parameterization for the CO2 effect on stomatal resistance
<http://wiki.geos-chem.org/Dry_deposition#Simple_parameterization_for_CO2_dependence_of_stomatal_resistance>`_.

.. option:: activate

   .. option:: true

      Activates the CO2 effect on stomatal resistance in dry deposition.

   .. option:: false

      DeActivates the CO2 effect on stomatal resistance in dry
      deposition.  This is the default setting.

.. option:: CO2_level

   Specifies the CO2 level (in ppb).

.. option:: reference_CO2_level

   Specifies the reference CO2 level (in ppb).

diag_alt_above_sfc_in_m
-----------------------

Specifies the altitude above the surface (in m) to used with the
`ConcAboveSfc diagnostic collection <http://wiki.seas.harvard.edu/geos-chem/index.php/History_collections_for_dry_deposition#The_ConcAboveSfc_collection>`_.

.. _cfg-gc-yml-operations-pblmix:

==========
PBL mixing
==========

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     pbl_mixing:
       activate: true
       use_non_local_pbl: true

     # ... following sub-sections omitted ...

The :command:`operations:pbl_mixing` section contains settings that
for `planetary boundary layer (PBL) mixing
<http://wiki.geos-chem.org/Boundary_layer_mixing>`_:

activate
--------

.. option:: true

   Activates planetary boundary layer mixing in GEOS-Chem.

.. option:: false

   Deactivates planetary boundary layer mixing in GEOS-Chem.

use_non_local_pbl
-----------------

.. option:: true

   Uses the `non-local PBL mixing scheme (VDIFF)
   <http://wiki.geos-chem.org/Boundary_layer_mixing#VDIFF>`_.  This is
   the default setting.

.. option:: false

   Uses the `full PBL mixing scheme (TURBDAY)
   <http://wiki.geos-chem.org/Boundary_layer_mixing#VDIFF>`_.

.. _cfg-gc-yml-operations-photolysis:

==========
Photolysis
==========

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     photolysis:
       activate: true
       cloud-j:
         cloudj_input_dir: ${RUNDIR_DATA_ROOT}/CHEM_INPUTS/CLOUD_J/v2025-01/
         num_levs_with_cloud: 34
         cloud_scheme_flag: 3
         opt_depth_increase_factor: 1.050
         min_top_inserted_cloud_OD: 0.005
         cloud_overlap_correlation: 0.33
         num_cloud_overlap_blocks: 6
         sphere_correction: 1
         num_wavelength_bins: 18
         use_H2O_UV_absorption: true
       fast-jx:
         fastjx_input_dir: /path/to/ExtData/CHEM_INPUTS/FAST_JX/v2024-05/
       overhead_O3:
         use_online_O3_from_model: true
         use_column_O3_from_met: true
         use_TOMS_SBUV_O3: false
       photolyze_nitrate_aerosol:
         activate: true
         NITs_Jscale: 100.0
         NIT_Jscale: 100.0
         percent_channel_A_HONO: 66.667
         percent_channel_B_NO2: 33.333

     # ... following sub-sections omitted ...

The :command:`operations:photolysis` section contains settings for photolysis.
This section only applies to :option:`fullchem` and :option:`Hg` simulations.

activate
--------

.. option:: true

   Activates photolysis in GEOS-Chem.  This is the default setting.

.. option:: false

   Deactivates photolysis in GEOS-Chem.

   .. attention::

      You should always keep photolysis turned on in your
      simulations.  Disabling photolysis should only be done when
      debugging.

cloud-j
-------

Specifies various options for the Cloud-J photolysis package.

.. note::

   The Cloud-J settings have been preset to the recommended values.
   You should not need to modify these settings (unless you are
   investigating how aerosol and cloud interactions impact photolysis).

.. option:: cloudj_input_dir

   Specifies the path to the Cloud-J configuration files containing
   information about species cross sections and quantum yields.

.. option:: num_levs_with_cloud

   Specifies the number of levels that can contain clouds, which is a
   required input for the Cloud-J photolysis module.  This value is
   pre-set to the proper value for the vertical grid that your
   simulation will use.

   .. list-table::
      :header-rows: 1

      * - GEOS-Chem variable
        - Cloud-J variable
      * - :code:`Input_Opt%NLevs_Phot_Cloud`
        - :code:`LWEPAR`

.. option:: cloud_scheme_flag

   Specifies the `cloud option
   <https://github.com/geoschem/Cloud-J/blob/f8a2b7f964bde1582fbc38c41d8872bc23a21735/src/Core/cldj_cmn_mod.F90#L71-L79>`_
   used in the computation of photolyis rates.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%CLDFLAG`
        - :code:`LWEPAR`

.. option:: opt_depth_increase_factor

   Specifies the factor increase in cloud optical depth from a
   given layer to the layer below.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%OD_Increase_Factor`
	- :code:`ATAU`

.. option:: min_top_inserted_cloud_OD

   Specifies the minimum cloud OD in the uppermost inserted layer.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%Min_Cloud_OD`
        - :code:`ATAU0`

.. option:: cloud_overlap_correlation

   Specifies the cloud de-corellation between max-overlap blocks,
   where 0.00 is random overlap.  This option is only used when
   :option:`cloud_scheme_flag` is set to 5 or higher.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%Cloud_Corr`
	- :code:`CLDCOR`

.. option:: num_cloud_overlap_blocks

   Specifies the number of `maximum-overlap blocks
   <https://github.com/geoschem/Cloud-J/blob/f8a2b7f964bde1582fbc38c41d8872bc23a21735/src/Core/cldj_cmn_mod.F90#L97-L99>`_.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%Num_Max_Overlap`
        - :code:`LNRG`

.. option:: sphere_correction

   Specifies the type of `spherical correction <https://github.com/geoschem/Cloud-J/blob/f8a2b7f964bde1582fbc38c41d8872bc23a21735/src/Core/cldj_cmn_mod.F90#L56-L60>`_ to be applied.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%OD_Increase_Factor`
	- :code:`ATM0`

.. option:: num_wavelength_bins

   Specifies the `number of wavelength bins
   <https://github.com/geoschem/Cloud-J/blob/f8a2b7f964bde1582fbc38c41d8872bc23a21735/src/Core/cldj_cmn_mod.F90#L101-L104>`_
   to use in the computation of photolysis reaction rates.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%Num_WV_Bins`
        - :code:`ATM0`

.. option:: use_H2O_UV_absorption

   Specifies whether to enable (:literal:`true`) or disable
   (:literal:`false`) UV absorption of water vapor in the
   computations for photolysis rates.  Default value:
   :literal:`true`.

   .. list-table::
      :header-rows: 1
      :widths: 50 50

      * - GEOS-Chem variable
	- Cloud-J variable
      * - :code:`Input_Opt%Use_H2O_UV_Abs`
        - :code:`USEH2OUV`

fast-jx
-------

Specifies various options for the FAST-JX photolysis package.

.. attention::

   FAST-JX is currently used only by the Hg (mercury) simulation,
   In the near future, the Hg simulation will be updated to use
   Cloud-J, and FAST_JX will be retired from GEOS-Chem.

.. option:: fastjx_input_dir

   Specifies the path to the legacy FAST_JX configuration files containing
   information about species cross sections and quantum yields.
   These are used to define several aerosol optical properties
   even when FAST-JX is not used.

   Note that FAST-JX is off by default and Cloud-J is used
   instead. You can use legacy FAST-JX instead of Cloud-J by
   configuring with  :literal:`-DFASTJX=y` during build.

overhead_O3
-----------

This section contains settings that control which overhead ozone
sources are used for photolysis

.. option:: use_online_O3_from_model

   .. option:: true

      Uses the advected O3 species from GEOS-Chem in the extinction
      calculations for photolysis.  This is the recommended setting.

   .. option:: false

      Does not use the advected O3 species from GEOS-Chem in the
      extinction calculations for photolysis.

.. option:: use_column_O3_from_met

   .. option:: true

      Uses ozone columns (e.g. TO3) from the meteorology fields.
      This is the recommended setting.

   .. option:: false

      Does not not use ozone columns from the meteorology fields.

.. option:: use_TOMS_SBUV_O3

   .. option:: true

      Uses ozone columms from the TOMS-SBUV archive.

   .. option:: false

      Does not use ozone columsn from the TOMS-SBUV archive.  This is
      the recommended setting.

photolyze_nitrate_aerosol
-------------------------

This section contains settings that control options for nitrate
aerosol photolysis.

.. option:: activate

   .. option:: true

      Activates nitrate aerosol photolysis.  This is the recommended setting.

   .. option:: false

      Deactivates nitrate aerosol photolysis.

.. option:: NITs_Jscale

   Scale factor (percent) for JNO3 that photolyzes NITs aerosol.

.. option:: NIT_Jscale

   Scale factor (percent) for JHNO2 that photolyzes NIT aerosol.

.. option:: percent_channel_A_HONO

   Fraction of JNITs/JNIT in channel A (HNO2) for NITs photolysis.

.. option:: percent_channel_B_HO2

   Fraction of JNITs/JNIT in channel B (NO2) for NITs photolysis.

.. _cfg-gc-yml-rrtmg:

==============================
RRTMG radiative transfer model
==============================

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     rrtmg_rad_transfer_model:
       activate: false
       aod_wavelengths_in_nm:
         - 550
       longwave_fluxes: false
       shortwave_fluxes: false
       clear_sky_flux: false
       all_sky_flux: false
       fixed_dyn_heating: false
       seasonal_fdh: false
       read_dyn_heating: false
       co2_ppmv: 390.0

     # .. following sub-sections omitted ...

The :command:`operations:rrtmg_rad_transfer_model` section contains
settings for the `RRTMG radiative transfer model
<http://wiki.geos-chem.org/Coupling_RRTMG_to_GEOS-Chem>`_:

This section only applies to :option:`fullchem` simultions.

activate
--------

.. option:: true

   Activates the RRTMG radiative transfer model.

.. option:: false

   Deactivates the RRTMG radiative transfer model.  This is the
   default setting.

aod_wavelengths_in_nm
---------------------

   Specify wavelength(s) for the aerosol optical properties in nm
   (in `YAML sequence format
   <https://www.tutorialspoint.com/yaml/yaml_sequence_styles.htm>`_)
   Up to three wavelengths can be selected.  The specified wavelengths
   are used for the photolysis mechanism (either legacy FAST-JX or
   Cloud-J) regardless of whether the RRTMG radiative transfer model is used.

longwave_fluxes
---------------

.. option:: true

   Activates RRTMG longwave flux calculations.

.. option:: false

   Dectivates RRTMG longwave flux calculations.  This is the
   default setting.

shortwave_fluxes
----------------

.. option:: true

   Activates RRTMG shortwave flux calculations.

.. option:: false

   Dectivates RRTMG shortwave flux calculations.  This is the
   default setting.

clear_sky_flux
--------------

.. option:: true

   Activates RRTMG clear-sky flux calculations.

.. option:: false

   Dectivates RRTMG clear-sky flux calculations.  This is the
   default setting.

all_sky_flux
------------

.. option:: true

   Activates RRTMG all-sky flux calculations.

.. option:: false

   Dectivates RRTMG clear-sky flux calculations.  This is the
   default setting.

fixed_dyn_heating
-----------------

.. option:: true

   Activates fixed dynamic heating (FDH) approximation as described
   by Forster *et al.* [`1997
   <https://agupubs.onlinelibrary.wiley.com/doi/10.1029/96JD03510>`_].

.. option:: false

   Deactivates fixed dynamic heating (FDH) approximation.  This is
   the default setting.

seasonal_fdh
------------

.. option:: true

   Activates seasonally-evolving fixed dynamic heating (SEFDH)
   approximation as described by Kiehl *et al.* [`1999
   <https://agupubs.onlinelibrary.wiley.com/doi/pdf/10.1029/1999JD900991>`_].

   .. attention::

      This option has not been extensively tested, and is considered
      experimental.

.. option:: false

   Deactivates seasonally-evolving fixed dynamic heating (SEFDH)
   approximation.  This is the default setting.

read_dyn_heating
----------------

.. option:: true

   Activates reading previously-archived dynamical heating outputs
   from disk.

.. option:: false

   Dectivates reading previously-archived dynamical heating outputs
   from disk.  This is the default setting.

co2_ppmv
--------

Specify the value of CO2 [in parts per million by volume] to be
used in radiative forcing calculations.  Default value:
:literal:`390.0`.

.. _cfg-gc-yml-transport:

=========
Transport
=========

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     transport:
       gcclassic_tpcore:                 # GEOS-Chem Classic only
         activate: true                  # GEOS-Chem Classic only
         fill_negative_values: true      # GEOS-Chem Classic only
         iord_jord_kord: [3, 3, 7]       # GEOS-Chem Classic only
       transported_species:
         - ACET
         - ACTA
         - AERI
	 # ... etc more transported species ...

   # .. following sub-sections omitted ...

The :command:`operations:transport` section contains
settings for `species transport
<http://wiki.geos-chem.org/Advection_scheme_TPCORE>`_:

gcclassic_tpcore
----------------

.. note::

   These settings are omitted for GCHP, which uses the FVdycore
   advection package instead.

Options that control species transport in GEOS-Chem
Classic with the `TPCORE advection scheme
<http://wiki.geos-chem.org/Advection_scheme_TPCORE>`_:

.. option:: activate

   .. option:: true

      Activates species transport in GEOS-Chem Classic.  This is the
      default setting.

   .. option:: false

      Deactivates species transport in GEOS-Chem Classic.

.. option:: fill_negative_values

   .. option:: true

      Will replace negative species concentrations with zeros.  This
      is the default setting.

   .. option:: false

      Will not replace negative species concentrations with zeros.

iord_jord_kord
--------------

Specifies advection options (in list format) for TPCORE in the
longitude, latitude, and vertical dimensions.  The options are
listed below:

#. 1st order upstream scheme (use for debugging only)
#. 2nd order van Leer (full monotonicity constraint)
#. Monotonic PPM
#. Semi-monotonic PPM (same as 3, but overshoots are allowed)
#. Positive-definite PPM
#. Un-constrained PPM (use when fields & winds are very smooth)
   this option only when the fields and winds are very smooth.
#. Huynh/Van Leer/Lin full monotonicity constraint (KORD only)

Default (and recommended) value: :literal:`[3, 3, 7]`

transported_species
-------------------

A list of species names (in `YAML sequence format
<https://www.tutorialspoint.com/yaml/yaml_sequence_styles.htm>`_)
that will be transported by the TPCORE advection scheme.

.. _cfg-gc-yml-wetdep:

==============
Wet deposition
==============

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem operations
   #============================================================================
   operations:

     # .. preceding sub-sections omitted ...

     wet_deposition:
       activate: true

The :command:`operations:wet_deposition` section contains settings
for `wet deposition <http://wiki.geos-chem.org/Wet_deposition>`_.

activate
--------

.. option:: true

   Activates wet deposition of soluble species in GEOS-Chem.  This is
   the default setting for simulations containing soluble species.

.. option:: false

   Deactivates wet deposition of soluble species in GEOS-Chem.  This
   is the default setting for simulations that do not have soluble species.

.. _gc-yml-aerosols:



There are several sub-sections under :literal:`aerosols`:

.. _cfg-gc-yml-aerosol-optics:

==============
Aerosol optics
==============

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     optics:
       input_dir: /path/to/ExtData/CHEM_INPUTS/Aerosol_Optics/v2025-03/

     # .. following sub-sections omitted ...

The :command:`aerosols:optics` section contains settings for aerosol
optics data.  This section only applies to :option:`fullchem` and
:option:`aerosol` simulations.

optics
------

.. option:: input_dir

   Specifies the path to files used containing aerosol optical
   properties for computing aerosol optical depth.

.. _cfg-gc-yml-aerosol-carbon:

===============
Carbon aerosols
===============

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     # ... preceding sub-sections omitted ...

     carbon:
       activate: true
       brown_carbon: false
       enhance_black_carbon_absorption:
         activate: true
         hydrophilic: 1.5
         hydrophobic: 1.0

     # .. following sub-sections omitted ...

The :command:`aerosols:carbon` section contains settings for
`carbon aerosols
<http://wiki.geos-chem.org/Carbonaceous_aerosols>`_.  This section
only applies to :option:`fullchem` and :option:`aerosol`
simulations.

activate
--------

.. option:: true

   Activates carbon aerosols in GEOS-Chem.  This is the default setting.

.. option:: true

   Deactivates carbon aerosols in GEOS-Chem

brown_carbon
------------

.. option:: true

   Activates brown carbon aerosols in GEOS-Chem.

.. option:: true

   Deactivates brown carbon aerosols in GEOS-Chem.  This is the
   default setting.

enhance_black_carbon_absorption
-------------------------------

Options for enhancing the absorption of black carbon aerosols
due to external coating.

.. option:: activate

   .. option:: true

      Activates black carbon absorption enhancement.  This is the
      default setting.

   .. option:: false

      Deactivates black carbon absorption enhancement.

.. option:: hydrophilic

   Absorption enhancement factor for hydrophilic black carbon
   aerosol (species name **BCPI**).  Default value: :literal:`1.5`

.. option:: hydrophobic

   Absorption enhancement factor for hydrophilic black carbon
   aerosol (species name **BCPO**).  Default value: :literal:`1.0`

.. _cfg-gc-yml-aerosols-soa:

===========
Complex SOA
===========

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     # ... preceding sub-sections omitted ...

     complex_SOA:
       activate:  true
       semivolatile_POA: false

     # ... following sub-sections omitted ...

The :command:`aerosols:complex_SOA` section contains settings for
`the complex SOA scheme used in GEOS-Chem
<http://wiki.seas.harvard.edu/geos-chem/index.php/Secondary_organic_aerosols#Complex_SOA_scheme>`_.
This section only applies to :option:`fullchem` and
:option:`aerosol` simulations.

activate
--------

.. option:: true

   Activates the complex SOA scheme.  This is the default setting for
   the for the :option:`fullchem` benchmark simulation.

.. option:: false

   Deactivates the complex SOA scheme.  This is the default setting
   for all other :option:`fullchem` simulations.

semivolatile_POA
----------------

.. option:: true

   Activates the semi-volatile primary organic aerosol (POA) option.

.. option:: false

   Deactivates the semi-volatile primary organic aerosol (POA)
   option.  This is the default setting.

.. _gc-yml-aerosols-dust:

=====================
Mineral dust aerosols
=====================

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     # ... preceding sub-sections omitted ...

     dust:
       activate: true
       acid_uptake_on_dust: false

     # ... following sub-sections omitted ...

The :command:`aerosols:dust` section contains settings for
`mineral dust aerosols
<http://wiki.seas.harvard.edu/geos-chem/index.php/Mineral_dust_aerosols>`_.
This section only applies to :option:`fullchem` and :option:`aerosol`
simulations.

activate
--------

.. option:: true

   Activates the mineral dust aerosols in GEOS-Chem.  This is the
   default setting.

.. option:: false

    Deactivates the mineral dust aerosols in GEOS-Chem.

acid_uptake_on_dust
-------------------

.. option:: true

   Activates `acid uptake on dust option
   <http://wiki.seas.harvard.edu/geos-chem/index.php/Mineral_dust_aerosols#Surface_chemistry_on_dust>`_,
   which includes 12 additional species.

.. option:: false

    Deactivates the acid uptake on dust option.  This is the default setting.

.. _cfg-gc-yml-aerosols-seasalt:

=================
Sea salt aerosols
=================

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     # ... preceding sub-sections omitted ...

     sea_salt:
       activate: true
       SALA_radius_bin_in_um: [0.01, 0.5]
       SALC_radius_bin_in_um: [0.5,  8.0]
       marine_organic_aerosols: false

     # ... following sub-sections omitted ...

The :command:`aerosols:sea_salt` section contains settings for `sea salt
aerosols
<http://wiki.seas.harvard.edu/geos-chem/index.php/Sea_salt_aerosols>`_.
This section only applies to :option:`fullchem` and
:option:`aerosol` simulations.

activate
--------

.. option:: true

   Activates sea salt aerosols in GEOS-Chem.  This is the
   default setting.

.. option:: false

    Deactivates sea salt aerosols.

SALA_radius_bin_in_um
---------------------

   Specifies the upper and lower boundaries (in nm) for
   accumulation-mode sea salt aerosol (aka **SALA**).   Default value:
   :literal:`[0.01, 0.5]`

SALC_radius_bin_in_um
---------------------

   Specifies the upper and lower boundaries (in nm) for
   coarse-mode sea salt aerosol (aka **SALC**).  Default value:
   :literal:`[0.5, 8.0]`

marine_organic_aerosols
-----------------------

.. option:: true

   Activates `emission of marine primary organic aerosols
   <http://wiki.seas.harvard.edu/geos-chem/index.php/Aerosol_emissions#Online_emission_of_marine_primary_organic_aerosol_.28POA.29>`_.
   This option includes two extra species (**MOPO** and **MOPI**).

.. option:: false

   Deactivates emission of marine primary organic aerosols.  This is
   the default setting.

.. _cfg-gc-yml-aerosols-strat:

======================
Stratospheric aerosols
======================

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     # ... preceding sub-sections omitted ...

     stratosphere:
       settle_strat_aerosol: true
       polar_strat_clouds:
         activate: true
         het_chem: true
       allow_homogeneous_NAT: false
       NAT_supercooling_req_in_K: 3.0
       supersat_factor_req_for_ice_nucl: 1.2
       calc_strat_aod: true

     # ... following sub-sections omitted ...

The :command:`aerosols:sulfate` section contains settings for
stratopsheric aerosols.  This section only applies to
:option:`fullchem` simulations.

settle_strat_aerosol
--------------------

.. option:: true

   Activates gravitational settling of stratospheric solid particulate
   aerosols (SPA, trapezoidal scheme) and stratospheric liquid
   aerosols (SLA, corrected Stokes' Law).  This is the default
   setting.

.. option:: false

   Dectivates gravitational settling of stratospheric solid
   particulate aerosols and stratopsheric liquid aerosols.

polar_strat_clouds
------------------

Contains settings for how aerosols are handled in polar
stratospheric clouds (PSC):

.. option:: activate

   .. option:: true

      Activates formation of polar stratospheric clouds.  This is the
      default setting.

   .. option:: false

      Dectivates formation of polar stratospheric clouds.

.. option:: het_chem

   .. option:: true

      Activates heterogeneous chemistry within polar stratospheric
      clouds. This is the default setting.

   .. option:: false

      Dectivates heterogeneous chemistry within polar stratospheric
      clouds.

.. option:: allow_homogeneous_NAT

   .. option:: true

      Activates heterogeneous formation of NAT from freezing of HNO3.

   .. option:: false

      Deactivates heterogeneous formation of NAT from freezing of
      HNO3.  This is the default setting.

..option:: NAT_supercooling_req_in_K

   Specifies the cooling (in K) required for homogeneous NAT
   nucleation.  Default value: :literal:`3.0`

.. option:: supersat_factor_req_for_ice_nucl

   Specifies the supersaturation factor required for ice nucleation.

   Recommended values: :literal:`1.2` for coarse grids; :literal:`1.5` for
   fine grids.

.. option:: calc_strat_aod

   .. option:: true

      Includes online stratospheric aerosols in extinction
      calculations for photolysis.  This is the default setting.

   .. option:: false

      Excludes online stratospheric aerosols in extinction
      calculations for photolysis.

.. _cfg-gc-yml-aerosols-sulfate:

================
Sulfate aerosols
================

.. code-block:: YAML

   #============================================================================
   # Settings for GEOS-Chem aerosols
   #============================================================================
   aerosols:

     # ... preceding sub-sections omitted ...

     sulfate:
       activate: true
       metal_cat_SO2_oxidation: true

The :command:`aerosols:sulfate` section contains settings for `sulfate
aerosols <http://wiki.geos-chem.org/Sulfate_aerosols>`_.  This section
only applies to the :option:`fullchem` and :option:`aerosol` simulations.

activate
--------

.. option:: true

   Activates sulfate aerosols in GEOS-Chem.  This is the default setting.

.. option:: false

   Deactivates sulfate aerosols in GEOS-Chem.

metal_cat_SO2_oxidation
-----------------------

.. option:: true

   Activates  `metal catalyzed oxidation of SO2
   <http://wiki.geos-chem.org/Sulfate_aerosols#Metal_catalyzed_oxidation_of_SO2>`_.
   This is the default setting.

.. option:: false

   Deactivates metal-catalyzed oxidation of SO2.

.. _gc-yml-xdiag-obspack:

==================
Obspack diagnostic
==================

.. note::

   These settings are omitted for GCHP, as ObsPack diagnostics can
   only be used with GEOS-Chem Classic.

.. code-block:: YAML

   #============================================================================
   # Settings for diagnostics (other than HISTORY and HEMCO)
   #============================================================================
   extra_diagnostics:

     obspack:
       activate: false
       quiet_logfile_output: false
       input_file: ./obspack_co2_1_OCO2MIP_2018-11-28.YYYYMMDD.nc
       output_file: ./OutputDir/GEOSChem.ObsPack.YYYYMMDD_hhmmz.nc4
       output_species:
         - CO
         - 'NO'
         - O3

     # ... following sub-sections omitted ...

The :command:`extra_diagnostics:obspack` section contains settings for
the `Obspack diagnostic <https://wiki.geos-chem.org/Obspack_diagnostic>`_:

activate
--------

.. option:: true

   Activates ObsPack diagnostic output in GEOS-Chem Classic.

.. option:: false

   Activates ObsPack diagnostic output in GEOS-Chem Classic.  This is
   the default setting.

quiet_logfile_output
--------------------

.. option:: true

   Suppresses printing extra informational output from ObsPack to
   :literal:`stdout` (i.e. the screen or log file).

.. option:: false

   Activates printing extra informational output from ObsPack to
   :literal:`stdout` (i.e. the screen or log file).  This is the
   default setting.

input_file
----------

Specifies the path to an ObsPack data file (in netCDF format).

output_file
-----------

Specifies the path to the ObsPack diagnostic output file.  This
will be a file that contains data at the same locations as
specified in :option:`input_file`.

output_species
--------------

A list of GEOS-Chem species (as a YAML sequence) to archive to the
output file.

.. _gc-yml-xdiag-plane:

======================
Planeflight diagnostic
======================

.. note::

   These settings are omitted for GCHP, as the Planeflight diagnostics can
   only be used with GEOS-Chem Classic.

.. code-block:: YAML

   #============================================================================
   # Settings for diagnostics (other than HISTORY and HEMCO)
   #============================================================================
   extra_diagnostics:

     # ... preceding sub-sections omitted ...

     planeflight:
       activate: false
       flight_track_file: Planeflight.dat.YYYYMMDD
       output_file: plane.log.YYYYMMDD

     # ... following sub-sections omitted ...

The :command:`extra_diagnostics:planeflight` section contains settings for
the `GEOS-Chem planeflight diagnostic
<https://wiki.geos-chem.org/Planeflight_diagnostic>`_.

activate
--------

.. option:: true

   Activates the Planeflight diagnostic output in GEOS-Chem Classic.

.. option:: false

   Deactivates (:literal:`false`) the Planeflight diagnostic output in
   GEOS-Chem Classic.  This is the default setting.

flight_track_file
-----------------

Specifies the path to a flight track file.  This file contains
the coordinates of the plane as a function of time, as well as the
requested quantities to archive.

output_file
-----------

Specifies the path to the Planeflight output file.  Requested
quantities will be archived from GEOS-Chem along the flight track
specified in :option:`flight_track_file`.

.. _cfg-gc-yml-hg-src:

==========
Hg sources
==========

.. code-block:: YAML

   #============================================================================
   # Settings specific to the Hg simulation
   #============================================================================
   Hg_simulation_options:

     sources:
       use_dynamic_ocean_Hg: false
       use_preindustrial_Hg: false
       use_arctic_river_Hg: true

     # ... following sub-sections omitted ...

The :command:`Hg_simulation_options:sources` section contains settings
for various mercury sources.  This section only applies to the
:option:`Hg` simulation.


use_dynamic_ocean_Hg
--------------------

.. option:: true

   Activates the online slab ocean mercury model.

.. option:: false

   Deactivates the online slab ocean mercury model.  This is the
   default setting.

use_preindustrial_Hg
--------------------

.. option:: true

   Activates the preindustrial mercury simulation.  This will turn off all
   anthropogenic emissions.

.. option:: false

   Deactivates the preindustrial mercury simulation.  This is the
   default setting.

use_arctic_river_Hg
-------------------

.. option:: true

   Activates the source of mercury from arctic rivers.  This is the
   default setting.

.. option:: false

   Deactivates the source of mercury from arctic rivers.

.. _cfg-gc-yml-hg-chem:

============
Hg chemistry
============

.. code-block:: YAML

   #============================================================================
   # Settings specific to the Hg simulation
   #============================================================================
   Hg_simulation_options:

     # ... preceding sub-sections omitted ...

     chemistry:
       tie_HgIIaq_reduction_to_UVB: true

     # ... following sub-sections omitted ...

The :command:`Hg_simulation_options:chemistry` section contains settings
for mercury chemistry.   This section only applies to the :option:`Hg`
simulation.

tie_HgIIaq_reduction_to_UVB
---------------------------

.. option:: true

   Activates linking the reduction of aqueous oxidized mercury to UVB
   radiation. (A lifetime of -1 seconds indicates the species has an
   infinite lifetime.)  This is the default setting.

.. option:: false

   Deactivates linking the reduction of aqueous oxidized mercury to
   UVB radiation.

.. _gc-yml-ch4_obsopt:

===========================
CH4 observational operators
===========================

.. code-block:: YAML

   #============================================================================
   # Settings specific to the CH4 simulation / Integrated Methane Inversion
   #============================================================================
   CH4_simulation_options:

     use_observational_operators:
       AIRS: false
       GOSAT: false
       TCCON: false

     # ... following sub-sections omitted ...

The :command:`CH4_simulation_options:use_observational_operators` section
contains options for using satellite observational operators for CH4.
This section only applies to simulations with carbon gases
(:option:`carbon`, :option:`CH4`, :option:`CO2`, :option:`tagCO`,
:option:`tagCH4`).

AIRS
----

.. option:: true

   Activates the AIRS observational operator.

.. option:: false

   Deactivates the AIRS observational operator.  This is the default
   setting.


GOSAT
-----

.. option:: true

   Activates the GOSAT observational operator.

.. option:: false

   Deactivates the GOSAT observational operator.  This is the default
   setting.

TCCON
-----

.. option:: true

   Activates the TCCON observational operator.

.. option:: false

   Deactivates the TCCON observational operator.  This is the default
   setting.

.. _gc-yml-ch4_anopt:

================================
CH4 analytical inversion options
================================

.. code-block:: YAML

   #============================================================================
   # Settings specific to the CH4 simulation / Integrated Methane Inversion
   #============================================================================
   CH4_simulation_options:

     # ... preceding sub-sections omitted ...

     analytical_inversion:
       perturb_OH_boundary_conditions: false
       CH4_boundary_condition_ppb_increase_NSEW: [0.0, 0.0, 0.0, 0.0]

The :literal:`ch4_simulation_options:analytical_inversion` section
contains options for analytical inversions with the `Integrated
Methane Inversion workflow (aka IMI) <https://imi.readthedocs.io>`_.
The IMI will automatically modify several of these options based on
the inversion parameters that you specify.

This section only applies to simulations with methane
(:option:`carbon` and :option:`CH4`).

perturb_CH4_boundary_conditions
-------------------------------

.. option:: true

   Activates perturbation of CH4 nested-grid boundary conditions in
   analytical inversions.

.. option:: false

   Deactivates perturbation of CH4 nested-grid boundary conditions in
   analytical inversions. This is the default setting.

CH4_boundary_condition_ppb_increase_NSEW
----------------------------------------

Specifies the perturbation amount (in ppbv) to apply to the north,
south, east and west CH4 nested-grid boundary conditions.  Used in
conjunction with the :option:`perturb_CH4_boundary_conditions`
option.

Default value: :literal:`[0.0, 0.0, 0.0, 0.0]` (no perturbation)

.. _cfg-gc-yml-co2:

===========
CO2 Sources
===========

.. code-block:: YAML

   #============================================================================
   # Settings specific to the CO2 simulation
   #============================================================================
   CO2_simulation_options:

     sources:
       3D_chemical_oxidation_source: true

     # ... following sub-sections omitted ...

The :command:`CO2_simulation_options:sources` section contains toggles
for activating sources of :math:`CO_2`.  This section only applies to
simulations with CO2 (:option:`carbon` and :option:`CO2`).

3D_chemical_oxidation_source
----------------------------

.. option:: true

   Activates :math:`CO_2` production by archived chemical oxidation,
   as read by HEMCO.  This is the default setting.

.. option:: false

   Deactivates :math:`CO_2` production by archived chemical oxidation.

.. _cfg-gc-yml-co2-tagspc:

==================
CO2 tagged species
==================

.. code-block:: YAML

   #============================================================================
   # Settings specific to the CO2 simulation
   #============================================================================
   CO2_simulation_options:

     # ... preceding sub-sections omitted ...

     tagged_species:
       tag_bio_and_ocean_CO2: false
       tag_land_fossil_fuel_CO2: false

     # .. following sub-sections omitted ...

The :literal:`CO2_simulation_options:tagged_species` section contains toggles
for activating tagged :math:`CO_2` species.  This section only applies to
simulations with CO2 (:option:`carbon` and :option:`CO2`).

.. attention::

   Tagged :math:`CO_2` tracers should be customized by each user and
   the present configuration will not work for resolutions other than
   :math:`2.0^{\circ} {\times} 2.5^{\circ}`.

tag_bio_and_ocean_CO2
---------------------

.. option:: true

   Activates tagging of biosphere regions (28), ocean regions (11),
   and the rest of the world (ROW) as specified in
   :file:`Regions_land.dat` and :file:`Regions_ocean.dat` files.

.. option:: false

   DeActivates tagging of regions. This is the default setting.

tag_land_fossil_fuel_CO2
------------------------

.. option:: true

   Activates tagging of land and ocean fossil fuel regions.

.. option:: false

   Deactivates tagging of land and ocean fossil fuel regions.  This is
   the default setting.

.. _cfg-gc-yml-co:

===================
CO chemical sources
===================

.. code-block:: YAML

   #============================================================================
   # Settings specific to the tagged CO simulation
   #============================================================================

   tagged_CO_simulation_options:
     use_fullchem_PCO_from_CH4: true
     use_fullchem_PCO_from_NMVOC: true

The :literal:`tagged_CO_simulation_options` section contains settings
for the :option:`carbon` simulation and `tagged CO simulation
<https://wiki.geos-chem.org/Tagged_CO_simulation>`_.

use_fullchem_PCO_from_CH4
-------------------------

.. option:: true

    Activates applying the production of :math:`CO` from :math:`CH_4`.
    This field is archived from a 1-year or 10-year :option:`fullchem`
    benchmark simulation and is read from disk via HEMCO.  This is the
    default setting.

.. option:: false

    DeActivates applying the production of :math:`CO` from :math:`CH_4`.

use_fullchem_PCO_from_NMVOC
---------------------------

.. option:: true

    Activates applying the production of :math:`CO` from non-methane
    volatile organic compounds (NMVOCs). This field is archived from a
    1-year or 10-year :option:`fullchem` benchmark simulation and is
    read from disk via  HEMCO.  This is the default setting.

.. option:: false

   Deactivates applying the production of :math:`CO` from NMVOCs.
