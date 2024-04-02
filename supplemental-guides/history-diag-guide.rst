.. |br| raw:: html

   <br />

.. _histguide:

###########################################
Archive output with the History diagnostics
###########################################

.. _histguide-example:

============
Introduction
============

GEOS-Chem Classic and GCHP allow you to save various diagnostic fields
to netCDF files via the **History** component.  In the following
sections you will learn how to schedule diagnostics for output using
History.

.. note::

   HEMCO has its own diagnostic archiving capability.  Please see the
   `HEMCO diagnostics
   <https://hemco.readthedocs.io/en/latesthco-ref-guide/diagnostics.html>`_
   chapter of `The HEMCO User's Guide <https://hemco.readthedocs.io>`_
   more information.

The HISTORY.rc configuration file
---------------------------------

You can request GEOS-Chem diagnostics by editing the
:file:`HISTORY.rc` configuration file.  This file lists the groups of
diagnostic outputs (called **collections**), and the parameters for
each collection (frequency of archival, mode of archiving, fields per
collection, etc.). GEOS-Chem will write netCDF files for each
collection at the output intervals that you specify in
:file:`HISTORY.rc`.

The following is a stripped-down :file:`HISTORY.rc` file example for
illustration purposes.  The :file:`HISTORY.rc` file that you will find
in your GEOS-Chem run directory will contain more collections than
what is shown below.

.. code-block:: kconfig

   #============================================================================
   # EXPID allows you to specify the beginning of the file path corresponding
   # to each diagnostic collection.  For example:
   #
   #   EXPID: ./GEOSChem
   #      Will create netCDF files whose names begin "GEOSChem",
   #      in this run directory.
   #
   #   EXPID: ./OutputDir/GEOSChem
   #      Will create netCDF files whose names begin with "GEOSChem"
   #      in the OutputDir sub-folder of this run directory.
   #
   #============================================================================
   EXPID:  ./OutputDir/GEOSChem

   #==============================================================================
   # %%%%% COLLECTION NAME DECLARATIONS %%%%%
   #
   # To disable a collection, place a "#" character in front of its name.
   #==============================================================================
   COLLECTIONS: 'SpeciesConc',
                'SpeciesConcSubset',
                'ConcAfterChem',
   ::
   #==============================================================================
   # %%%%% THE SpeciesConc COLLECTION %%%%%
   #
   # GEOS-Chem species concentrations (default = advected species)
   #
   # Available for all simulations
   #==============================================================================
   SpeciesConc.template:           '%y4%m2%d2_%h2%n2z.nc4',
   SpeciesConc.frequency:          00000000 060000
   SpeciesConc.frequency:          00000001 000000
   SpeciesConc.format:             'CFIO',
   SpeciesConc.mode:               'instantaneous',
   SpeciesConc.fields:             'SpeciesConcVV_?ADV?',
                                   'SpeciesConcMND_?ALL?',
   ::
   #==============================================================================
   # %%%%% THE SpeciesConcSubset COLLECTION %%%%%
   #
   # Same as the SpeciesConc collection, but will subset data in the horizontal
   # and vertical dimensions so that the netCDF diagnostic files will cover
   # a smaller region of the globe.  This can save disk space and memory.
   #
   # NOTE: This capability will be available in GEOS-Chem "Classic" 12.5.0
   # and later versions.
   #
   # Available for all simulations
   #==============================================================================
   SpeciesConcSubset.template:     '%y4%m2%d2_%h2%n2z.nc4',
   SpeciesConcSubset.frequency:    00000000 060000
   SpeciesConcSubset.duration:     00000001 000000
   SpeciesConcSubset.format:       'CFIO',
   SpeciesConcSubset.mode:         'instantaneous',
   SpeciesConcSubset.LON_RANGE:    -40.0 60.0,
   SpeciesConcSubset.LAT_RANGE:    -10.0 50.0,
   SpeciesConcSubset.levels:       1 2 3 4 5,
   SpeciesConcSubset.fields:       'SpeciesConcVV_?ADV?',
   ::
   #==============================================================================
   # %%%%% THE ConcAfterChem COLLECTION %%%%%
   #
   # Concentrations of OH, HO2, O1D, O3P immediately after exiting the KPP solver
   # or OH after the CH4 specialty-simulation chemistry routine.
   #
   # OH:       Available for all full-chemistry simulations and CH4 specialty sim
   # HO2:      Available for all full-chemistry simulations
   # O1D, O3P: Availalbe for full-chemistry simulations using UCX mechanism
   #==============================================================================
   ConcAfterChem.template:         '%y4%m2%d2_%h2%n2z.nc4',
   ConcAfterChem.format:           'CFIO',
   ConcAfterChem.frequency:        00000100 000000,
   ConcAfterChem.duration:         00000100 000000,
   ConcAfterChem.mode:             'time-averaged',
   ConcAfterChem.fields:           'OHconcAfterChem',
                                   'HO2concAfterChem',
                                   'O1DconcAfterChem',
                                   'O3PconcAfterChem',
   ::

In this :file:`HISTORY.rc` file, we are requesting three collections, or
types of netCDF file output. The table below explains in more detail
parameters shown in the HISTORY.rc file above.

:option:`EXPID`

   This parameter controls the filename prefix. :ref:`In this example,
   <histguide-sample>`, :literal:`EXPID` is set to
   :file:`./OutputDir/GEOSChem` by default.  This means that all
   diagnostic files will be written to the :file:`./OutputDir`
   subdirectory of the GEOS-Chem run directory, and will start with
   the prefix :file:`GEOSChem`.

   .. note::

      Restart files are placed in the :file:`./Restarts` subdirectory
      of the run directory instead of :file:`./OutputDir`, which only
      contains diagnostic files.

:option:`COLLECTIONS:`

   The :literal:`COLLECTIONS:` tag specifies all of the diagnostic
   collections that you wish to activate during a GEOS-Chem
   simulation. Each collection represents a group of diagnostic
   quantities that will be written to disk in netCDF file format. The
   collection name will be automatically added to the netCDF file name
   along with the date/or time.

   Each GEOS-Chem run directory will ship with its own customized
   :file:`HISTORY.rc` configuration file.  Only the diagnostic
   collections pertaining to a particular GEOS-Chem simulation will be
   included in the corresponding HISTORY.rc file.

   Each collection name must be bracketed by single quotes, and be
   followed by a comma.

   To disable an entire diagnostic collection, simply put a
   :literal:`#` comment character in front of the collection name in
   the :literal:`COLLECTIONS:`  section.

   GEOS-Chem will expect to find a collection definition section for each
   of the activated collections listed under the COLLECTIONS: section.
   In other words, if you have SpeciesConc listed under
   COLLECTIONS:, but there is no further information provided about the
   SpeciesConc collection, then GEOS-Chem will halt with an error
   message.

   .. note::

      You are not limited to the collections that are pre-defined in
      the :file:`HISTORY.rc` configuration file.  You may create
      additional diagnostic collections to suit your research
      purposes.

:option:`SpeciesConc`

   Name of the first collection in this :file:`HISTORY.rc` file. A
   **collection** is a series of files containing similar GEOS-Chem
   diagnostic quantities.

:option:`SpeciesConc.template`

   Determines the date and time format for the :option:`SpeciesConc`
   collection filename suffix, as described below:

   - :literal:`%y4%m2%d2_%h2%n2z.nc4` prints
     :literal:`YYYYMMDD_hhmmz.nc4` to the end of each netCDF filename.
   - :literal:`YYYYMMDD` is the date in year/month/day format;
   - :literal:`hhmm` is the time in :literal:`hour:minutes` format.
   - :literal:`z` denotes "Zulu", which is an abbreviation for UTC time.
   - :literal:`.nc4` denotes that the data file is in the netCDF-4 format.

   .. note::

      For example, the complete file path for the SpeciesConc
      collection at 00:00 UTC on 2020/01/01 will be
      :file:`./OutputDir/GEOSChem.SpeciesConc.20200101_0000z.nc4`, where:

      - :option:`EXPID` specifies the filename prefix
        (:file:`./OutputDir/GEOSChem`)

      - :option:`SpeciesConc.template` specifies the filename suffix
        (:file:`.20200101_0000z.nc4`).

:option:`SpeciesConc.frequency`

   Determines how often the diagnostic quantities belonging to
   :option:`SpeciesConc` collection will be saved to a netCDF file.
   This can be specified as either :literal:`hhmmss` or
   :literal:`YYYYMMDD hhmmss`.

   In the above example, data belonging to the collection will be
   written to  the file every 6 hours.  Because :option:`SpeciesConc`
   is an instantaneous collection, no time-averaging will be
   performed.

:option:`SpeciesConc.format`

   For GCHP simulations only indicates the I/O library that will be
   used. This can be omitted for GEOS-Chem "Classic"  simulations.

:option:`SpeciesConc.duration`

   Determines how often a new :option:`SpeciesConc` netCDF file will
   be created.  Uses the same format as :option:`SpeciesConc.frequency`.

:option:`SpeciesConc.mode`

   Determines the averaging method for the :option:`SpeciesConc`
   collection.  Allowable values are:

   - :literal:`instantaneous`: Archives instantaneous values at the
     interval specified by :option:`SpeciesConc.frequency`.
   - :literal:`time-averaged`: Archives values averaged over the
     interval specified by :option:`SpeciesConc.frequency`.

:option:`SpeciesConc.fields`

   Lists the diagnostic quantities to be archived in the
   :option:`SpeciesConc` collection.  Some diagnostic quantities
   (e.g. concentrations, fluxes, masses) may also have an extra
   dimension, which represents species, size bins, reaction numbers, etc.

   For example, to request the ozone species concentration (in mixing
   ratio units) you may use the field name
   :literal:`SpeciesConcVV_O3`.  The species name is separated from
   the quantity name by a single underscore.

   .. note::

      For GCHP, each diagnostic field must be followed by
      :literal:`'GCHPchem',`.  This indicates the ESMF Gridded
      Component to which the diagnostics belong.

   If you are using GEOS-Chem Classic,you may also use a
   :ref:`wildcard <histguide-wildcards>` to specify a given category
   of species. In the above example :literal:`SpeciesConcVV_?ADV?`
   refers to all advected species and :literal:`SpeciesConcVV_?ALL?`
   refers to all species (both advected and non-advected).

   .. note::

      GCHP does not allow the use of wildcards.  Each diagnostic
      quantity must be listed individually.

:option:`::`

   Signifies the end of the `SpeciesConc` definition section.
   :option:`::` may be placed at any column.

:option:`SpeciesConc.subset`

   Name of the second diagnostic collection specified in this sample
   :file:`HISTORY.rc` configuration file.  In this collection we
   will request output to be restricted to a subset of the horizontal
   grid.

   The :literal:`.template`, :literal:`.frequency`,
   :literal:`.duration`,  :literal:`:mode`, and :literal:`.fields`
   are described for the :option:`SpeciesConc` collection above, so we
   will not repeat them here.

:option:`SpeciesConcSubset.LON_RANGE`

   Defines the longitude range (:literal:`min max`) where diagnostic
   data will be archived.  Data outside of this range will be
   ignored.  If this option is omitted, values at all longitudes
   (:literal:`-180 180`) will be included.

:option:`SpeciesConcSubset.LAT_RANGE`

   Defines the latitude range (:literal:`min max`) where diagnostic
   data will be archived.  Data outside of this range will be ignored.
   If this option is omitted, values at all latitudes
   (:literal:`-90 90`) will be included.

:option:`SpeciesConcSubset.levels`

   Specifies the levels that you wish to be included in the diagnostic
   archiving.  If omitted, data at all levels will be included.

   .. note::

      In GEOS-Chem Classic, all levels between the minimum and
      maximum level specified will be included in the diagnostic
      archival.  This differs from the behavior in GCHP, which
      archives only the specified levels.

:option:`ConcAfterChem`

   Name of the third collection specified in this sample
   :file:`HISTORY.rc` configuration file.

   The :literal:`.template`, :literal:`.frequency`,
   :literal:`.duration`,  :literal:`:mode`, and :literal:`.fields`
   are described for the :option:`SpeciesConc` collection above,
   so we will not repeat them here.

:option:`ConcAftercChem.mode`

   In this example, the :literal:`ConcAfterChem.mode` setting
   indicates that the :literal:`ConcAfterChem` collection will contain
   time-averaged data.  The averaging interval is set in the


.. _histguide-wildcards:

Wildcards (GEOS-Chem Classic only)
----------------------------------

For GEOS-Chem Classic diagnostic output, you can use the following
wildcards with diagnostic quantities that have a species/bin/reaction
dimension:

+------------------+-----------------------------+-------------------------------+
| Wildcard         | Description                 | Example                       |
+==================+=============================+===============================+
| ``?ADV?``        | Advected species            | ``SpeciesConcVV_?ADV?``       |
+------------------+-----------------------------+-------------------------------+
| ``?AER?``        | Aerosol species             | ``SpeciesConcVV_?AER?``       |
+------------------+-----------------------------+-------------------------------+
| ``?ALL?``        | All species                 | ``SpeciesConcVV_?ALL?``       |
+------------------+-----------------------------+-------------------------------+
| ``?DRY?``        | Dry-deposited species       | ``SpeciesConcVV_?DRY?``       |
+------------------+-----------------------------+-------------------------------+
| ``?DRYALT?``     | Species for the             | ``SpeciesConcVV_?DRYALT``     |
|                  | ``ConcAboveChem``           |                               |
|                  | collection                  |                               |
+------------------+-----------------------------+-------------------------------+
| ``?DUSTBIN?``    | Dust bin number             | ``AODdust550nm_?DUSTBIN?``    |
+------------------+-----------------------------+-------------------------------+
| ``?FIX?``        | Fixed species in the        | ``SpeciesConcVV_?FIX?``       |
|                  | KPP chemistry mechanism     |                               |
+------------------+-----------------------------+-------------------------------+
| ``?GAS?``        | Gas-phase species           | ``SpeciesConcVV_?GAS?``       |
+------------------+-----------------------------+-------------------------------+
| ``?HYG?``        | Aerosol species that        | ``AODhyg550nm_?HYG?``         |
|                  | undergo hygroscopic growth  |                               |
|                  | (e.g. black carbon)         |                               |
+------------------+-----------------------------+-------------------------------+
| ``?KPP?``        | All species (fixed +        | ``SpeciesConcVV_?KPP?``       |
|                  | variable) in the KPP        |                               |
|                  | chemistry mechanism         |                               |
+------------------+-----------------------------+-------------------------------+
| ``?LOS?``        | Chemical loss species       | ``SpeciesConcVV_?LOS?``       |
|                  | or families                 |                               |
+------------------+-----------------------------+-------------------------------+
| ``?PHO?``        | Photolyzed species          | ``SpeciesConcVV_?PHO?``       |
+------------------+-----------------------------+-------------------------------+
| ``?PRD?``        | Chemical production         | ``SpeciesConcVV_?PRD?``       |
|                  | species or families         |                               |
+------------------+-----------------------------+-------------------------------+
| ``?RRTMG?``      | RRTMG-computed fluxes       | ``RadAllSkywSurf_?RRTMG?``    |
+------------------+-----------------------------+-------------------------------+
| ``?RXN?``        | KPP reaction rates          | ``RxnRate_?RXN?``             |
+------------------+-----------------------------+-------------------------------+
| ``?TOMASBIN?``   | TOMAS size bins             | ``TomasH2SO4Mass_?TOMASBIN?`` |
+------------------+-----------------------------+-------------------------------+
| ``?UVFLX?``      | UV flux bins                | ``UVFluxDiffuse_?UVFLX?``     |
+------------------+-----------------------------+-------------------------------+
| ``?VAR?``        | Variable species in the     | ``SpeciesConcVV_?VAR?``       |
|                  | KPP mechanism               |                               |
+------------------+-----------------------------+-------------------------------+
| ``?WET?``        | Wet-deposited species       | ``SpeciesConcVV_?WET``        |
+------------------+-----------------------------+-------------------------------+


.. _histguide-prefixes:

Prefixes
--------

You may add any field from the :code:`State_Met` and :code:`State_Chm`
objects to any diagnostic collection as well.  These fields must be
prefixed as described below:

+------------------+-----------------------------+-------------------------------+
| Wildcard         | Description                 | Example                       |
+==================+=============================+===============================+
| ``Chem_``        | Request diagnostic output   | ``Chem_pHCloud``              |
|                  | from the ``State_Chm``      |                               |
|                  | object                      |                               |
+------------------+-----------------------------+-------------------------------+
| ``Met_``         | Request diagnostic output   | ``Met_SPHU``                  |
|                  | from the ``State_Met``      |                               |
|                  | object                      |                               |
+------------------+-----------------------------+-------------------------------+

.. _histguide-filename:

File naming convention
----------------------

As mentioned above, :option:`SpeciesConc.template`, GEOS-Chem History
files adhere to the following naming convention:

.. code-block:: none

   EXPID.collection-name.collection-template

e.g.

.. code-block:: none

   ../OutputDir/GEOSChem.SpeciesConc.20200101_0000z.nc4

The duration tag of each collection in :file:`HISTORY.rc` controls how
often a new file will be written to disk, as we saw :ref:`above
<histtguide-example>`:

.. code-block:: none

   SpeciesConc.duration:           00000001 000000     # Write a new file each day
   SpeciesConcSubset.duration:     00000001 000000     # Write a new file each day
   ConcAfterChem.duration:         00000100 000000     # Write a new file each month

Therefore, based on all of these settings in our example HISTORY.rc
file, GEOS-Chem will write the following netCDF files to disk in the
current run directory:

.. code-block:: console

   GEOSChem.SpeciesConc.20160101_0000z.nc4    GEOSChem.SpeciesConcSubset.20160101_0000z.nc4
   GEOSChem.SpeciesConc.20160102_0000z.nc4    GEOSChem.SpeciesConcSubset.20160102_0000z.nc4
   GEOSChem.SpeciesConc.20160103_0000z.nc4    GEOSChem.SpeciesConcSubset.20160102_0000z.nc4
   GEOSChem.SpeciesConc.20160104_0000z.nc4    GEOSChem.SpeciesConcSubset.20160104_0000z.nc4
   ... etc ...

   GEOSChem.ConcAfterChem.20160101_0000z.nc4
   GEOSChem.ConcAfterChem.20160201_0000z.nc4
   GEOSChem.ConcAfterChem.20160301_0000z.nc4
   GEOSChem.ConcAfterChem.20160401_0000z.nc4
   ... etc ...

.. _histgude_vert_coords:

Vertical coordinates in netCDF files
------------------------------------

All netCDF files produced by GEOS-Chem (i.e. diagnostic files and
restart files) adhere to the :ref:`the COARDS netCDF convention
<coards-guide>`. for the lon, lat, and time dimensions.

For the vertical dimension, we have chosen to use the following
coordinate variables, emulating the file format of the NCAR Community
Earth System Model (CESM):

.. code-block:: console

   variables:
        double lev(lev) ;
            lev:long_name = "hybrid level at midpoints (1000*(A+B))" ;
            lev:units = "level" ;
            lev:positive = "down" ;\
            lev:standard_name = "atmosphere_hybrid_sigma_pressure_coordinate" ;
            lev:formula_terms = "a: hyam b: hybm p0: P0 ps: PS" ;
        double hyam(lev) ;
            hyam:long_name = "hybrid A coefficient at layer midpoints" ;
        double hybm(lev) ;
            hybm:long_name = "hybrid B coefficient at layer midpoints" ;
        double ilev(ilev) ;
            ilev:long_name = "hybrid level at interfaces (1000*(A+B))" ;
            ilev:units = "level" ;
            ilev:positive = "down" ;
            ilev:standard_name = "atmosphere_hybrid_sigma_pressure_coordinate" ;
            ilev:formula_terms = "a: hyai b: hybi p0: P0 ps: PS" ;
        double hyai(ilev) ;
            hyai:long_name = "hybrid A coefficient at layer interfaces" ;
        double hybi(ilev) ;
            hybi:long_name = "hybrid B coefficient at layer interfaces" ;
        double P0 ;
            P0:long_name = "reference pressure" ;

The lev variable is used for data that is placed on the midpoints
between vertical levels. This is an "approximate" eta coordinate, which
is close to 1 at the surface and close to zero at the atmosphere top.

.. code-block:: console

   lev = 0.99250002413, 0.97749990013, 0.962499776, 0.947499955, 0.93250006,
      0.91749991, 0.90249991, 0.88749996, 0.87249996, 0.85750006, 0.842500125,
      0.82750016, 0.8100002, 0.78750002, 0.762499965, 0.737500105, 0.7125001,
      0.6875001, 0.65625015, 0.6187502, 0.58125015, 0.5437501, 0.5062501,
      0.4687501, 0.4312501, 0.3937501, 0.3562501, 0.31279158, 0.26647905,
      0.2265135325, 0.192541016587707, 0.163661504087706, 0.139115, 0.11825,
      0.10051436, 0.085439015, 0.07255786, 0.06149566, 0.05201591, 0.04390966,
      0.03699271, 0.03108891, 0.02604911, 0.021761005, 0.01812435, 0.01505025,
      0.01246015, 0.010284921, 0.008456392, 0.0069183215, 0.005631801,
      0.004561686, 0.003676501, 0.002948321, 0.0023525905, 0.00186788,
      0.00147565, 0.001159975, 0.00090728705, 0.0007059566, 0.0005462926,
      0.0004204236, 0.0003217836, 0.00024493755, 0.000185422, 0.000139599,
      0.00010452401, 7.7672515e-05, 5.679251e-05, 4.0142505e-05, 2.635e-05,
      1.5e-05 ;

The lev variable may be used for quick plotting. To compute the
actual pressure at the midpoint of the grid box (I,J,L), you will need
to supply your own 2-D surface pressure field (e.g. saved from another
diagnostic file):

.. code-block:: console

   Pmid = ( hyam(L) * PS(I,J) ) + hybm(L)

The ilev variable is used for data that is placed on vertical level
edges or "interfaces" (hence the "i" in ilev). This is also an
"approximate" eta coordinate.

.. code-block:: console

   ilev = 1, 0.98500004826, 0.969999752, 0.9549998, 0.94000011, 0.92500001,
      0.90999981, 0.89500001, 0.87999991, 0.86500001, 0.85000011, 0.83500014,
      0.82000018, 0.80000022, 0.77499982, 0.75000011, 0.7250001, 0.7000001,
      0.6750001, 0.6375002, 0.6000002, 0.5625001, 0.5250001, 0.4875001,
      0.4500001, 0.4125001, 0.3750001, 0.3375001, 0.28808306, 0.24487504,
      0.208152025, 0.176930008175413, 0.150393, 0.127837, 0.108663, 0.09236572,
      0.07851231, 0.06660341, 0.05638791, 0.04764391, 0.04017541, 0.03381001,
      0.02836781, 0.02373041, 0.0197916, 0.0164571, 0.0136434, 0.0112769,
      0.009292942, 0.007619842, 0.006216801, 0.005046801, 0.004076571,
      0.003276431, 0.002620211, 0.00208497, 0.00165079, 0.00130051, 0.00101944,
      0.0007951341, 0.0006167791, 0.0004758061, 0.0003650411, 0.0002785261,
      0.000211349, 0.000159495, 0.000119703, 8.934502e-05, 6.600001e-05,
      4.758501e-05, 3.27e-05, 2e-05, 1e-05 ;

To compute the actual pressure at the bottom and top edges of the grid
box (I,J,L), you will need to supply your own 2-D surface pressure field
(e.g. saved from another diagnostic file):

.. code-block:: console

   Pbot = ( hyai(L  ) * PS(I,J) ) + hybi(L  )
   Ptop = ( hyai(L+1) * PS(I,J) ) + hybi(L+1)

.. _histguide-collections:

======================
Diagnostic collections
======================

The diagnostic collections described in the sections below are used by
default in GEOS-Chem simulations. You can create your own customized
collections by modifying the HISTORY.rc file.

The only restriction is that you cannot mix data that is placed on grid
box layer edges in the same collection as data placed on grid box layer
centers. This violates the netCDF convention that all data variables
have to be defined with the same vertical dimension.

In this section we give an overview of the History diagnostics used in GEOS-Chem.

.. _histguide-legend:

General information about each collection
-----------------------------------------

The tables below list the following parameters for each diagnostic that
is archived to bpch format:

+-----------------+---------------------------------------------------+
| Item            | Description                                       |
+=================+===================================================+
| Diagnostic name | The name of the given diagnostic quantity that    |
|                 | will be archived to netCDF file format.           |
|                 |                                                   |
+-----------------+---------------------------------------------------+
| Description     | A short overview of the given diagnostic.         |
+-----------------+---------------------------------------------------+
| Units           | The physical units of the given diagnostic        |
|                 | quantity.                                         |
+-----------------+---------------------------------------------------+
| Wildcards       | :ref:`A list of wildcards <histguide-wildcards>`  |
|                 | that can be used with the given diagnostic        |
|                 | quantity.                                         |
+-----------------+---------------------------------------------------+

.. note::

   For diagnostic quantities that have a species/bin/reaction
   dimension, we will use the abbreviation :literal:`<name|wc>` to
   indicate a species name or wildcard.

   For example, :literal:`SpeciesConcVV_<name|wc>` means that the
   diagnostic quantity can be a single species
   (:literal:`SpeciesConcVV_O3`) or a wildcarded subset
   of species (:literal:`SpeciesConcVV_?ADV?`).

.. _histguide-advfluxvert:

The AdvFluxVert collection
--------------------------

**Sample definition section for HISTORY.rc**

.. code-block:: none

     AdvFluxVert.template:       '%y4%m2%d2_%h2%n2z.nc4',
     AdvFluxVert.format:         'CFIO',
     AdvFluxVert.frequency:      00000100 000000
     AdvFluxVert.duration:       00000100 000000
     AdvFluxVert.mode:           'time-averaged'
     AdvFluxVert.fields:         'AdvFluxVert_O3',
   ::

**List of diagnostic quantities**

+-----------------------------+---------------------------+-------+-----------+
| Diagnostic field            | Description               | Units | Wildcards |
+=============================+===========================+=======+===========+
| AdvFluxVert_name|wc [#f1]_  | Vertical flux of species  | kg/s  | ``?ADV?`` |
|                             | in advection              |       |           |
+-----------------------------+---------------------------+-------+-----------+

.. rubric:: Footnotes

.. [#f1] Only defined for GEOS-Chem Classic simulations.

.. _histguide-aerosols:

The Aerosols collection
-----------------------

The **Aerosols** collection contains diagnostics for aerosol optical
depth and related quantities from full-chemistry simulations.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     Aerosols.template:          '%y4%m2%d2_%h2%n2z.nc4',
     Aerosols.format:            'CFIO',
     Aerosols.frequency:         00000100 000000
     Aerosols.duration:          00000100 000000
     Aerosols.mode:              'time-averaged'
     Aerosols.fields:            'AODDust                       ',
                                 'AODDustWL1_?DUSTBIN?          ',
                                 'AODHygWL1_?HYG?               ',
                                 'AODSOAfromAqIsopreneWL1       ',
                                 'AODStratLiquidAerWL1          ',
                                 'AODPolarStratCloudWL1         ',
                                 'AerHygroscopicGrowth_?HYG?    ',
                                 'AerNumDensityStratLiquid      ',
                                 'AerNumDensityStratParticulate ',
                                 'AerAqueousVolume              ',
                                 'AerSurfAreaDust               ',
                                 'AerSurfAreaHyg_?HYG?          ',
                                 'AerSurfAreaStratLiquid        ',
                                 'AerSurfAreaPolarStratCloud    ',
                                 'Chem_AeroAreaMDUST1           ',
                                 'Chem_AeroAreaMDUST2           ',
                                 'Chem_AeroAreaMDUST3'          ',
                                 'Chem_AeroAreaMDUST4           ',
                                 'Chem_AeroAreaMDUST5           ',
                                 'Chem_AeroAreaMDUST6           ',
                                 'Chem_AeroAreaMDUST7           ',
                                 'Chem_AeroAreaSULF             ',
                                 'Chem_AeroAreaBC               ',
                                 'Chem_AeroAreaOC               ',
                                 'Chem_AeroAreaSSA              ',
                                 'Chem_AeroAreaSSC              ',
                                 'Chem_AeroAreaBGSULF           ',
                                 'Chem_AeroAreaICEI             ',
                                 'Chem_AeroRadiMDUST1           ',
                                 'Chem_AeroRadiMDUST2           ',
                                 'Chem_AeroRadiMDUST3           ',
                                 'Chem_AeroRadiMDUST4           ',
                                 'Chem_AeroRadiMDUST5           ',
                                 'Chem_AeroRadiMDUST6           ',
                                 'Chem_AeroRadiMDUST7           ',
                                 'Chem_AeroRadiSULF             ',
                                 'Chem_AeroRadiBC               ',
                                 'Chem_AeroRadiOC               ',
                                 'Chem_AeroRadiSSA              ',
                                 'Chem_AeroRadiSSC              ',
                                 'Chem_AeroRadiBGSULF           ',
                                 'Chem_AeroRadiICEI             ',
                                 'Chem_WetAeroAreaMDUST1        ',
                                 'Chem_WetAeroAreaMDUST2        ',
                                 'Chem_WetAeroAreaMDUST3        ',
                                 'Chem_WetAeroAreaMDUST4        ',
                                 'Chem_WetAeroAreaMDUST5        ',
                                 'Chem_WetAeroAreaMDUST6        ',
                                 'Chem_WetAeroAreaMDUST7        ',
                                 'Chem_WetAeroAreaSULF          ',
                                 'Chem_WetAeroAreaBC            ',
                                 'Chem_WetAeroAreaOC            ',
                                 'Chem_WetAeroAreaSSA           ',
                                 'Chem_WetAeroAreaSSC           ',
                                 'Chem_WetAeroAreaBGSULF        ',
                                 'Chem_WetAeroAreaICEI          ',
                                 'Chem_WetAeroRadiMDUST1        ',
                                 'Chem_WetAeroRadiMDUST2        ',
                                 'Chem_WetAeroRadiMDUST3        ',
                                 'Chem_WetAeroRadiMDUST4        ',
                                 'Chem_WetAeroRadiMDUST5        ',
                                 'Chem_WetAeroRadiMDUST6        ',
                                 'Chem_WetAeroRadiMDUST7        ',
                                 'Chem_WetAeroRadiSULF          ',
                                 'Chem_WetAeroRadiBC            ',
                                 'Chem_WetAeroRadiOC            ',
                                 'Chem_WetAeroRadiSSA           ',
                                 'Chem_WetAeroRadiSSC           ',
                                 'Chem_WetAeroRadiBGSULF        ',
                                 'Chem_WetAeroRadiICEI          ',
                                 'Chem_StatePSC                 ',
                                 'Chem_KhetiSLAN2O5H2O          ',
                                 'Chem_KhetiSLAN2O5HCl          ',
                                 'Chem_KhetiSLAClNO3H2O         ',
                                 'Chem_KhetiSLAClNO3HCl         ',
                                 'Chem_KhetiSLAClNO3HBr         ',
                                 'Chem_KhetiSLABrNO3H2O         ',
                                 'Chem_KhetiSLABrNO3HCl         ',
                                 'Chem_KhetiSLAHOClHCl          ',
                                 'Chem_KhetiSLAHOClHBr          ',
                                 'Chem_KhetiSLAHOBrHCl          ',
                                 'Chem_KhetiSLAHOBrHBr          ',
   ::

**List of diagnostic quantities**

+---------+---------+---------+---------+
| Dia     | Desc    | Units   | Wi      |
| gnostic | ription |         | ldcards |
| name    |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
+=========+=========+=========+=========+
| AODDust | Mineral | 1       |         |
|         | dust    |         |         |
|         | optical |         |         |
|         | depth   |         |         |
|         | for 1st |         |         |
|         | wav     |         |         |
|         | elength |         |         |
|         | sp      |         |         |
|         | ecified |         |         |
|         | in      |         |         |
|         | Ra      |         |         |
|         | diation |         |         |
|         | Menu    |         |         |
|         |         |         |         |
+---------+---------+---------+---------+
| AODDust | AOD for | 1       | ?D      |
| _WL1_?D | 1st     |         | USTBIN? |
| USTBIN? | wav     |         |         |
|         | elength |         |         |
|         | sp      |         |         |
|         | ecified |         |         |
|         | in      |         |         |
|         | Ra      |         |         |
|         | diation |         |         |
|         | Menu,   |         |         |
|         | for     |         |         |
|         | each    |         |         |
|         | dust    |         |         |
|         | bin     |         |         |
+---------+---------+---------+---------+
| AODH    | Optical | 1       | ?HYG?   |
| ygWL1\_ | depth   |         |         |
|         | for     |         |         |
|         | s       |         |         |
|         | elected |         |         |
|         | species |         |         |
|         | (SO4,   |         |         |
|         | BC, OC, |         |         |
|         | SALA,   |         |         |
|         | SALC)   |         |         |
|         | at the  |         |         |
|         | 1st     |         |         |
|         | wav     |         |         |
|         | elength |         |         |
|         | sp      |         |         |
|         | ecified |         |         |
|         | in the  |         |         |
|         | Ra      |         |         |
|         | diation |         |         |
|         | Menu    |         |         |
+---------+---------+---------+---------+
| AO      | Optical | 1       |         |
| DSOAfro | depth   |         |         |
| mAqIsop | of SOA  |         |         |
| reneWL1 | from    |         |         |
|         | aqueous |         |         |
|         | i       |         |         |
|         | soprene |         |         |
|         | optical |         |         |
|         | depth   |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
+---------+---------+---------+---------+
| AODStr  | Strato  |         |         |
| atLiqui | spheric |         |         |
| dAerWL1 | liquid  |         |         |
|         | aerosol |         |         |
|         | optical |         |         |
|         | depth   |         |         |
|         | (600    |         |         |
|         | nm)     |         |         |
+---------+---------+---------+---------+
| AODPola | Polar   | 1       |         |
| rStratC | strato  |         |         |
| loudWL1 | spheric |         |         |
|         | cloud   |         |         |
|         | type    |         |         |
|         | 1a/2    |         |         |
|         | optical |         |         |
|         | depth   |         |         |
|         | (600    |         |         |
|         | nm)     |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
|         |         |         |         |
+---------+---------+---------+---------+
| A       | Hygr    | 1       | ?HYG?   |
| erHygro | oscopic |         |         |
| scopicG | growth  |         |         |
| rowth\_ | of      |         |         |
|         | s       |         |         |
|         | elected |         |         |
|         | species |         |         |
|         | (SO4,   |         |         |
|         | BC, OC, |         |         |
|         | SALA,   |         |         |
|         | SALC)   |         |         |
+---------+---------+---------+---------+
| Aer     | Strato  | 1/cm3   |         |
| NumDens | spheric |         |         |
| ityStra | liquid  |         |         |
| tLiquid | aerosol |         |         |
|         | number  |         |         |
|         | density |         |         |
|         | (UCX    |         |         |
|         | sim     |         |         |
|         | ulation |         |         |
|         | only)   |         |         |
+---------+---------+---------+---------+
| A       | Strato  | cm2/cm3 |         |
| erSurfA | spheric |         |         |
| reaStra | liquid  |         |         |
| tLiquid | aerosol |         |         |
|         | surface |         |         |
|         | area    |         |         |
|         | (UCX    |         |         |
|         | sim     |         |         |
|         | ulation |         |         |
|         | only)   |         |         |
+---------+---------+---------+---------+
| AerSu   | Polar   | cm2/cm3 |         |
| rfAreaP | strato  |         |         |
| olarStr | spheric |         |         |
| atCloud | cloud   |         |         |
|         | type    |         |         |
|         | 1a/2    |         |         |
|         | surface |         |         |
|         | area    |         |         |
|         | (UCX    |         |         |
|         | sim     |         |         |
|         | ulation |         |         |
|         | only)   |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST1 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.15  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST2 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.25  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST3 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.4   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST4 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.8   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST5 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 1.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST6 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 2.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aMDUST7 | area    |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 4.0   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Che     | Dry     | cm2/cm3 |         |
| m_AeroA | aerosol |         |         |
| reaSULF | area    |         |         |
|         | for     |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| C       | Dry     | cm2/cm3 |         |
| hem_Aer | aerosol |         |         |
| oAreaBC | area    |         |         |
|         | for     |         |         |
|         | black   |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| C       | Dry     | cm2/cm3 |         |
| hem_Aer | aerosol |         |         |
| oAreaOC | area    |         |         |
|         | for     |         |         |
|         | organic |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| Ch      | Dry     | cm2/cm3 |         |
| em_Aero | aerosol |         |         |
| AreaSSA | area    |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | accum   |         |         |
|         | ulation |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| Ch      | Dry     | cm2/cm3 |         |
| em_Aero | aerosol |         |         |
| AreaSSA | area    |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | coarse  |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm2/cm3 |         |
| AeroAre | aerosol |         |         |
| aBGSULF | area    |         |         |
|         | for     |         |         |
|         | bac     |         |         |
|         | kground |         |         |
|         | strato  |         |         |
|         | spheric |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| Che     | Dry     | cm2/cm3 |         |
| m_AeroA | aerosol |         |         |
| reaICEI | area    |         |         |
|         | for     |         |         |
|         | ir      |         |         |
|         | regular |         |         |
|         | ice     |         |         |
|         | cloud   |         |         |
|         | (Mis    |         |         |
|         | chenko) |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST1 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.15  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST2 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.25  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST3 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.4   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST4 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.8   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST5 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 1.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST6 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 2.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iMDUST7 | radius  |         |         |
|         | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 4.0   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Che     | Dry     | cm      |         |
| m_AeroR | aerosol |         |         |
| adiSULF | radius  |         |         |
|         | for     |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| C       | Dry     | cm      |         |
| hem_Aer | aerosol |         |         |
| oRadiBC | radius  |         |         |
|         | for     |         |         |
|         | black   |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| C       | Dry     | cm      |         |
| hem_Aer | aerosol |         |         |
| oRadiOC | radius  |         |         |
|         | for     |         |         |
|         | organic |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| Ch      | Dry     | cm      |         |
| em_Aero | aerosol |         |         |
| RadiSSA | radius  |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | accum   |         |         |
|         | ulation |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| Chem    | Dry     | cm      |         |
| _AeroRa | aerosol |         |         |
| diusSSC | radius  |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | coarse  |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iBGSULF | radius  |         |         |
|         | for     |         |         |
|         | bac     |         |         |
|         | kground |         |         |
|         | strato  |         |         |
|         | spheric |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| Chem_   | Dry     | cm      |         |
| AeroRad | aerosol |         |         |
| iusICEI | radius  |         |         |
|         | for     |         |         |
|         | ir      |         |         |
|         | regular |         |         |
|         | ice     |         |         |
|         | cloud   |         |         |
|         | (Mis    |         |         |
|         | chenko) |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST1 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.15  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST2 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.25  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST3 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.4   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST4 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.8   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST5 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 1.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST6 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 2.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aMDUST7 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 4.0   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_W  | Wet     | cm2/cm3 |         |
| etAeroA | aerosol |         |         |
| reaSULF | area    |         |         |
|         | for     |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| Chem    | Wet     | cm2/cm3 |         |
| _WetAer | aerosol |         |         |
| oAreaBC | area    |         |         |
|         | for     |         |         |
|         | black   |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| Chem    | Wet     | cm2/cm3 |         |
| _WetAer | aerosol |         |         |
| oAreaOC | area    |         |         |
|         | for     |         |         |
|         | organic |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| Chem_   | Wet     | cm2/cm3 |         |
| WetAero | aerosol |         |         |
| AreaSSA | area    |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | accum   |         |         |
|         | ulation |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| Chem_   | Wet     | cm2/cm3 |         |
| WetAero | aerosol |         |         |
| AreaSSA | area    |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | coarse  |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm2/cm3 |         |
| hem_Wet | aerosol |         |         |
| AeroAre | area    |         |         |
| aBGSULF | for     |         |         |
|         | bac     |         |         |
|         | kground |         |         |
|         | strato  |         |         |
|         | spheric |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| Chem_W  | Wet     | cm2/cm3 |         |
| etAeroA | aerosol |         |         |
| reaICEI | area    |         |         |
|         | for     |         |         |
|         | ir      |         |         |
|         | regular |         |         |
|         | ice     |         |         |
|         | cloud   |         |         |
|         | (Mis    |         |         |
|         | chenko) |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST1 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.15  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST2 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.25  |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST3 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.4   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST4 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 0.8   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST5 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 1.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST6 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 2.5   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iMDUST7 | for     |         |         |
|         | mineral |         |         |
|         | dust    |         |         |
|         | (bin    |         |         |
|         | R\ :su  |         |         |
|         | b:`eff` |         |         |
|         | = 4.0   |         |         |
|         | μm)     |         |         |
+---------+---------+---------+---------+
| Chem_W  | Wet     | cm      |         |
| etAeroR | aerosol |         |         |
| adiSULF | radius  |         |         |
|         | for     |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| Chem    | Wet     | cm      |         |
| _WetAer | aerosol |         |         |
| oRadiBC | radius  |         |         |
|         | for     |         |         |
|         | black   |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| Chem    | Wet     | cm      |         |
| _WetAer | aerosol |         |         |
| oRadiOC | radius  |         |         |
|         | for     |         |         |
|         | organic |         |         |
|         | carbon  |         |         |
+---------+---------+---------+---------+
| WetCh   | Wet     | cm      |         |
| em_Aero | aerosol |         |         |
| RadiSSA | radius  |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | accum   |         |         |
|         | ulation |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| Chem_We | Wet     | cm      |         |
| tAeroRa | aerosol |         |         |
| diusSSC | radius  |         |         |
|         | for sea |         |         |
|         | salt,   |         |         |
|         | coarse  |         |         |
|         | mode    |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iBGSULF | for     |         |         |
|         | bac     |         |         |
|         | kground |         |         |
|         | strato  |         |         |
|         | spheric |         |         |
|         | sulfate |         |         |
+---------+---------+---------+---------+
| C       | Wet     | cm      |         |
| hem_Wet | aerosol |         |         |
| AeroRad | radius  |         |         |
| iusICEI | for     |         |         |
|         | ir      |         |         |
|         | regular |         |         |
|         | ice     |         |         |
|         | cloud   |         |         |
|         | (Mis    |         |         |
|         | chenko) |         |         |
+---------+---------+---------+---------+
| Chem_S  | Polar   | count   |         |
| tatePSC | strato  |         |         |
|         | spheric |         |         |
|         | cloud   |         |         |
|         | type    |         |         |
|         | (cf     |         |         |
|         | Kirner  |         |         |
|         | et al,  |         |         |
|         | 2011,   |         |         |
|         | GMD)    |         |         |
+---------+---------+---------+---------+
| Chem_K  | S       | 1       |         |
| hetiSLA | ticking |         |         |
| N2O5H2O | coeff.  |         |         |
|         | for     |         |         |
|         | N2O5 +  |         |         |
|         | H2O rxn |         |         |
+---------+---------+---------+---------+
| Chem_K  | S       | 1       |         |
| hetiSLA | ticking |         |         |
| N2O5HCl | coeff.  |         |         |
|         | for     |         |         |
|         | N2O5 +  |         |         |
|         | HCl rxn |         |         |
+---------+---------+---------+---------+
| Chem_Kh | S       | 1       |         |
| etiSLAC | ticking |         |         |
| lNO3H2O | coeff.  |         |         |
|         | for     |         |         |
|         | ClNO3 + |         |         |
|         | H2O rxn |         |         |
+---------+---------+---------+---------+
| Chem_Kh | S       | 1       |         |
| etiSLAC | ticking |         |         |
| lNO3HCl | coeff.  |         |         |
|         | for     |         |         |
|         | ClNO3 + |         |         |
|         | HCl rxn |         |         |
+---------+---------+---------+---------+
| Chem_Kh | S       | 1       |         |
| etiSLAC | ticking |         |         |
| lNO3HBr | coeff.  |         |         |
|         | for     |         |         |
|         | ClNO3 + |         |         |
|         | HBr rxn |         |         |
+---------+---------+---------+---------+
| Chem_Kh | S       | 1       |         |
| etiSLAB | ticking |         |         |
| rNO3H2O | coeff.  |         |         |
|         | for     |         |         |
|         | BrNO3 + |         |         |
|         | H2O rxn |         |         |
+---------+---------+---------+---------+
| Chem_Kh | S       | 1       |         |
| etiSLAB | ticking |         |         |
| rNO3HCl | coeff.  |         |         |
|         | for     |         |         |
|         | BrNO3 + |         |         |
|         | HCl rxn |         |         |
+---------+---------+---------+---------+
| Chem_K  | S       | 1       |         |
| hetiSLA | ticking |         |         |
| HOClHCl | coeff.  |         |         |
|         | for     |         |         |
|         | HOCl +  |         |         |
|         | HCl rxn |         |         |
+---------+---------+---------+---------+
| Chem_   | S       | 1       |         |
| KhetiSL | ticking |         |         |
| AHOClBr | coeff.  |         |         |
|         | for     |         |         |
|         | HOCl +  |         |         |
|         | HBr rxn |         |         |
+---------+---------+---------+---------+
| Chem_K  | S       | 1       |         |
| hetiSLA | ticking |         |         |
| HOBrHCl | coeff.  |         |         |
|         | for     |         |         |
|         | HOBr +  |         |         |
|         | HCl rxn |         |         |
+---------+---------+---------+---------+
| Chem_K  | S       | 1       |         |
| hetiSLA | ticking |         |         |
| HOBrHCl | coeff.  |         |         |
|         | for     |         |         |
|         | HOBr +  |         |         |
|         | HBr rxn |         |         |
+---------+---------+---------+---------+


.. _histguide-aerosolmass

The AerosolMass collection
--------------------------

The **AerosolMass** collection contains diagnostics for aerosol mass and
particulate matter from full-chemistry simulations.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     AerosolMass.template:       '%y4%m2%d2_%h2%n2z.nc4',
     AerosolMass.format:         'CFIO',
     AerosolMass.frequency:      00000100 000000
     AerosolMass.duration:       00000100 000000
     AerosolMass.mode:           'time-averaged'
     AerosolMass.fields:         'AerMassASOA                  ', 'GIGCchem',
                                 'AerMassBC                    ', 'GIGCchem',
                                 'AerMassINDIOL                ', 'GIGCchem',
                                 'AerMassISN1OA                ', 'GIGCchem',
                                 'AerMassISOA                  ', 'GIGCchem',
                                 'AerMassLVOCOA                ', 'GIGCchem',
                                 'AerMassNH4                   ', 'GIGCchem',
                                 'AerMassNIT                   ', 'GIGCchem',
                                 'AerMassOPOA                  ', 'GIGCchem',
                                 'AerMassPOA                   ', 'GIGCchem',
                                 'AerMassSAL                   ', 'GIGCchem',
                                 'AerMassSO4                   ', 'GIGCchem',
                                 'AerMassSOAGX                 ', 'GIGCchem',
                                 'AerMassSOAIE                 ', 'GIGCchem',
                                 'AerMassSOAME                 ', 'GIGCchem',
                                 'AerMassSOAMG                 ', 'GIGCchem',
                                 'AerMassTSOA                  ', 'GIGCchem',
                                 'BetaNO                       ', 'GIGCchem',
                                 'PM25                         ', 'GIGCchem',
                                 'TotalBiogenicOA              ', 'GIGCchem',
                                 'TotalOA                      ', 'GIGCchem',
                                 'TotalOC                      ', 'GIGCchem',
   ::

**List of diagnostic quantities**

+-----------------------+-------------------------------+--------------------+
| Diagnostic name       | Description                   | Units              |
+=======================+===============================+====================+
| AerMassASOA\ [#f2]_   | Aerosol products of light     | :math:`{\mu}g/m^3` |
|                       | aromatics + IVOC oxidation    |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassBC             | Aerosol products of light     | :math:`{\mu}g/m^3` |
|                       | aromatics + IVOC oxidation    |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassINDIOL\ [#f2]_ | Generic aerosol-phase         | :math:`{\mu}g/m^3` |
|                       | organonitrate hydrolysis      |                    |
|                       | product                       |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassISN10A\ [#f2]_ | Aerosol phase 2nd generation  | :math:`{\mu}g/m^3` |
|                       | hydroxynitrates formed from   |                    |
|                       | ISOP + NO3 rxn pathway        |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassISOA\ [#f2]_   | Aerosol products of isoprene  | :math:`{\mu}g/m^3` |
|                       | oxidation                     |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassLVOCOA\ [#f2]_ | Aerosol-phase low-volatility  | :math:`{\mu}g/m^3` |
|                       | non-IEPOX product of ISOPOOH  |                    |
|                       | (RIP) oxidation               |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassNH4            | Ammonium                      | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassNIT            | Inorganic nitrate aerosol     | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassPOA\ [#f2]_    | Aerosols from SVOCs           | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassOPOA\ [#f2]_   | Aerosols products of POG      | :math:`{\mu}g/m^3` |
|                       | oxidation                     |                    |
+-----------------------+-------------------------------+--------------------+
| AerMassSAL            | Sea salt aerosol (SALA+SALC)  | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassSO4            | Sulfate                       | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassSOAGX\ [#f2]_  | Aerosol phase glyoxal         | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassSOAIE\ [#f2]_  | Aerosol phase IEPOX           | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassSOAME\ [#f2]_  | Aerosol phase IMAE            | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassSOAMG\ [#f2]_  | Aerosol phase methylglyoxal   | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| AerMassTSOA\ [#f2]_   | Aerosol products of terpene   | :math:`{\mu}g/m^3` |
|                       | oxidation                     |                    |
+-----------------------+-------------------------------+--------------------+
| BetaNO\ [#f2]_        | NO branching ratio            | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| PM25                  | Particulate matter (d < 2.5   | :math:`{\mu}g/m^3` |
|                       | :math:{\mu}m`)                |                    |
+-----------------------+-------------------------------+--------------------+
| TotalBiogenicOA\      | Sum of all organic aerosol    | :math:`{\mu}g/m^3` |
| [#f3]_                |                               |                    |
+-----------------------+-------------------------------+--------------------+
| TotalOA\ [#f2]_       | Sum of all organic aerosol    | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+
| TotalOC               | Sum of all organic carbon     | :math:`{\mu}g/m^3` |
+-----------------------+-------------------------------+--------------------+

.. rubric:: Footnotes

.. [#f2] Only defined for fullchem simulations with complex SOA
 	 species.

.. [#f3] Defined for aerosol-only simulations or fullchem simulations.

.. _histguide-boundaryconditions:

The BoundaryConditions collection
---------------------------------

The **BoundaryConditions** diagnostic collection contains advected
species concentrations (archived from a global simulation) that will
be used by GEOS-Chen Classic nested-grid simulations as transport
boundary conditions.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     BoundaryConditions.template:   '%y4%m2%d2_%h2%n2z.nc4',
     BoundaryConditions.format:     'CFIO',
     BoundaryConditions.frequency:  00000000 030000
     BoundaryConditions.duration:   00000100 000000
     BoundaryConditions.mode:       'instantaneous'
     BoundaryConditions.fields:     'SpeciesBC_?ADV?',
   ::

**List of diagnostic quantities**

+-------------------------------+-------------------------+-------------+-----------+
| Diagnostic field              | Description             | Units       | Wildcard  |
+===============================+=========================+=============+===========+
| SpeciesBC_<name|wc>\ [#f4]_   | Advected species        | mol/mol dry | ``?ADV?`` |
|                               | concentrations used     | air         |           |
|                               | as boundary conditions  |             |           |
|                               | GEOS-Chem Classic       |             |           |
|                               | nested-grid simulations |             |           |
+-------------------------------+-------------------------+-------------+-----------+

.. rubric:: Footnotes

.. [#f4] This diagnostic is only for use with GEOS-Chem Classic.

.. _histguide-budget:

The Budget collection
---------------------

The **Budget** diagnostic collection is a 2D diagnostic containing the
mass tendencies per grid cell, in kg/s, for each species within a region
of the column and across each GEOS-Chem component. The diagnostic is
calculated by taking the difference in vertically summed column mass
before and after major GEOS-Chem components.

There are three column regions defined for this diagnostic:
troposphere-only, PBL-only, and full column. By post-processing this
diagnostic you can calculate global mass change or mass change across
regions by summing the diagnostic values for the relevant grid cells.
You can also retrieve the mass change across a longer chunk of time by
multiplying the time-averaged output by the number of seconds in the
averaging period.

While there are seven major components in GEOS-Chem, there are only six
implemented for the budget diagnostics. Emissions and dry deposition
components are combined together for this diagnostic because of the way
they are applied at the same time. Furthermore, if using non-local PBL
mixing then the emissions and dry deposition budget diagnostic will not
capture all fluxes from these sources and sinks. This is because
emissions and dry deposition tendencies below the PBL are applied within
mixing instead. When using full mixing, however, mixing and
emissions/dry deposition budget diagnostics are fully separated.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     Budget.template:     '%y4%m2%d2_%h2%n2z.nc4',
     Budget.format:       'CFIO',
     Budget.frequency:    00000100 000000
     Budget.duration:     00000100 000000
     Budget.mode:         'time-averaged'
     Budget.fields:       'BudgetChemistryFull_?ADV? ',
                          'BudgetChemistryPBL_?ADV?  ',
                          'BudgetChemistryTrop_?ADV? ',
                          'BudgetEmisDepFull_?ADV?   ',
                          'BudgetEmisDepTrop_?ADV?   ',
                          'BudgetEmisDepPBL_?ADV?    ',
                          'BudgetTransportFull_?ADV? ',
                          'BudgetTransportTrop_?ADV? ',
                          'BudgetTransportPBL_?ADV?  ',
                          'BudgetMixingFull_?ADV?    ',
                          'BudgetMixingTrop_?ADV?    ',
                          'BudgetMixingPBL_?ADV?     ',
                          'BudgetConvectionFull_?ADV?',
                          'BudgetConvectionTrop_?ADV?',
                          'BudgetConvectionPBL_?ADV? ',
                          'BudgetWetDepFull_?WET?    ',
                          'BudgetWetDepTrop_?WET?    ',
                          'BudgetWetDepPBL_?WET?     ',
   ::

**List of diagnostic quantities**

+---------------------------------------+---------------------------------+----------+
| Diagnostic field                      | Mass tendency (kg/s) across ... | Wildcard |
+=======================================+=================================+==========+
| BudgetChemistryFull_<name|wc>         | Chemistry (full atmosphere)     | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetChemistryLevs1to35_<name|wc>\   | Chemistry (fixed level range)   | ?ADV?    |
| [#f5]_                                |                                 |          |
+---------------------------------------+---------------------------------+----------+
| BudgetChemistryPBL_<name|wc>          | Chemistry (PBL only)            | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetChemistryTrop_<name|wc>         | Chemistry (troposphere only)    | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetEmisDepFull_<name|wc>\ [#f6]_   | Emissions & dry deposition      | ?ADV?    |
|                                       | (full atmosphere)               |          |
+---------------------------------------+---------------------------------+----------+
| BudgetEmisDepLevs1to35_<name|wc>\     | Emissions & dry deposition      | ?ADV?    |
| [#f5]_ [#f6]_                         | (fixed level range)             |          |
+---------------------------------------+---------------------------------+----------+
| BudgetEmisPBL_<name|wc>\ [#f6]_       | Emissions & dry deposition      | ?ADV?    |
|                                       | (PBL only)                      |          |
+---------------------------------------+---------------------------------+----------+
| BudgetEmisTrop_<name|wc>\ [#f6]_      | Emissions & dry deposition      | ?ADV?    |
|                                       | (troposphere only)              |          |
+---------------------------------------+---------------------------------+----------+
| BudgetTransportFull_<name|wc>         | Transport (full attmosphere)    | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetTransportLevs1to35_<name|wc>\   | Transport (fixed level range)   | ?ADV?    |
| [#f5]_                                |                                 |          |
+---------------------------------------+---------------------------------+----------+
| BudgetTransportPBL_<name|wc>          | Transport (PBL only)            | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetTransportTrop_<name|wc>         | Transport (troposphere only)    | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetMixingFull_<name|wc> [#f7]_     | PBL mixing (full atmosphere)    | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetMixingLevs1to35_<name|wc>\      | PBL mixing (full atmosphere)    | ?ADV?    |
| [#f5]_  [#f7]_                        | (fixed level range)             |          |
+---------------------------------------+---------------------------------+----------+
| BudgetMixingPBL_<name|wc>\ [#f7]_     | PBL mixing (PBL only)           | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetMixingTrop_<name|wc>\ [#f7]_    | PBL mixing (troposphere only)   | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetConvectionFull_<name|wc>        | Convection (full atmosphere)    | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetConvectionLevs1to35_<name|wc>   | Convection (fixed level range)  | ?ADV?    |
| [#f5]_                                |                                 |          |
+---------------------------------------+---------------------------------+----------+
| BudgetConvectionPBL_<name|wc>         | Convection (PBL only)           | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetConvectionTrop_<name|wc>        | Convection (troposphere only)   | ?ADV?    |
+---------------------------------------+---------------------------------+----------+
| BudgetWetDepFull_<name|wc>            | Wet deposition                  | ?WET?    |
|                                       | (full atmosphere)               |          |
+---------------------------------------+---------------------------------+----------+
| BudgetWetDepLevs1to35_<name|wc>\      | Wet deposition (fixed level     | ?WET?    |
| [#f5]_                                | range)                          |          |
+---------------------------------------+---------------------------------+----------+
| BudgetWetDepPBL_<name|wc>             | Wet deposition (PBL only)       | ?WET     |
+---------------------------------------+---------------------------------+----------+
| BudgetConvectionTrop_<name|wc>        | Wet deposition                  | ?WET     |
|                                       | (troposphere only)              |          |
+---------------------------------------+---------------------------------+----------+

.. rubric:: Footnotes

.. [#f5] These diagnostic quantities allow you to compute mass
	 tendencies in a fixed level range.  The lower level and upper level of
	 the range is specified in the diagnostic name
	 (:literal:`LevsXtoY`).  Levels 1 to 35 (surface to
	 approximately the tropopause) are the default settings.

.. [#f6] The emissions and dry deposition budget diagnostics will not
	 capture all fluxes if using the non-local PBL mixing scheme since
	 these tendencies are applied within mixing in :file:`vdiff_mod.F90`
	 below the  PBL. When using full mixing, however, mixing and emissions/dry
	 deposition are fully separated.

.. [#f7] The mixing budget diagnostics includes the application of
	 emissions and dry deposition below the PBL if using the non-local PBL
	 mixing scheme (vdiff).

.. _histguide-ch4:

The CH4 collection
------------------

The **CH4** collection contains diagnostics for loss of CH4 and OH
concentration for the CH4 and carbon simulations.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     CH4.template:          '%y4%m2%d2_%h2%n2z.nc4',
     CH4.format:            'CFIO',
     CH4.frequency:         00000100 000000
     CH4.duration:          00000100 000000
     CH4.mode:              'time-averaged'
     CH4.fields:            'OHconcAfterChem   ',
                            'LossCH4byClinTrop ',
                            'LossCH4byOHinTrop ',
                            'LossCH4inStrat    ',
   ::

**List of diagnostic quantities**

+--------------------+------------------------------------------+--------+
| Diagnostic field   | Description                              | Units  |
+====================+==========================================+========+
| LossCH4byClinTrop  | Loss of CH4 by raction with Cl in the    | kg/s   |
|                    | troposphere                              |        |
+--------------------+------------------------------------------+--------+
| LossCH4byOHinTrop  | Loss of CH4 by raction with OH in the    | kg/s   |
|                    | troposphere                              |        |
+--------------------+------------------------------------------+--------+
| LossCH4inStrat     | Loss of CH4 in the stratosphere          | kg/s   |
+--------------------+------------------------------------------+--------+
| OHconcAfterChem    | OH concentration after chemistry         | kg/s   |
+--------------------+------------------------------------------+--------+

.. _histguide-cloudconvflux:

The CloudConvFlux collection
----------------------------

The **CloudConvFlux** collection contains diagnostics for mass fluxes in
cloud convection.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     CloudConvFlux.template:     '%y4%m2%d2_%h2%n2z.nc4',
     CloudConvFlux.format:       'CFIO',
     CloudConvFlux.frequency:    00000100 000000
     CloudConvFlux.duration:     00000100 000000
     CloudConvFlux.mode:         'time-averaged'
     CloudConvFlux.fields:       'CloudConvFlux_?ADV?',
   ::

**List of diagnostic quantities**

+-------------------------+--------------------+----------+------------+
| Diagnostic field        | Description        | Units    | Wildcards  |
+=========================+====================+==========+============+
| CloudConvFlux_<name|wc> | Mass change due to | kg/s     | ?ADV? |br| |
|                         | cloud convection   |          | ?GAS? |br| |
|                         |                    |          | ?WET?      |
+-------------------------+--------------------+----------+------------+

.. _histguide-concafterchem:

The ConcAfterChem collection
----------------------------

The **ConcAfterChem** collection contains diagnostics for OH, HO2, etc.
species immediately upon exiting the chemical solver.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     ConcAfterChem.template:     '%y4%m2%d2_%h2%n2z.nc4',
     ConcAfterChem.format:       'CFIO',
     ConcAfterChem.frequency:    00000100 000000
     ConcAfterChem.duration:     00000100 000000
     ConcAfterChem.mode:         'time-averaged'
     ConcAfterChem.fields:       'OHconcAfterChem                ', 'GIGCchem',
                                 'HO2concAfterChem               ', 'GIGCchem',
                                 'O1DconcAfterChem               ', 'GIGCchem',
                                 'O3PconcAfterChem               ', 'GIGCchem',
                                 'O3concAfterChem                ', 'GIGCchem',
                                 'RO2concAfterChem               ', 'GIGCchem',
   ::

**List of diagnostic quantities**

+-------------------+-------------------------------+---------------+
| Diagnostic field  | Description                   | Units         |
+===================+===============================+===============+
| OHconcAfterChem   | OH immediately after exiting  | molec/cm3     |
|                   | the chemical solver           |               |
+-------------------+-------------------------------+---------------+
| HO2concAfterChem  | HO2 immediately after exiting | mol/mol       |
|                   | the chemical solver           |               |
+-------------------+-------------------------------+---------------+
| O1DconcAfterChem  | O1D immediately after exiting | molec/cm3     |
|                   | the chemical solver           |               |
+-------------------+-------------------------------+---------------+
| O3PconcAfterChem  | O3P immediately after exiting | molec/cm3     |
|                   | the chemical solver           |               |
+-------------------+-------------------------------+---------------+
| O3concAfterChem   | O3 immediately after exiting  | molec/cm3     |
|                   | the chemical solver           |               |
+-------------------+-------------------------------+---------------+
| RO2concAfterChem  | RO2 immediately after exiting | molec/cm3     |
|                   | the chemical solver           |               |
+-------------------+-------------------------------+---------------+

.. _the_jvalues_collection:

The JValues collection
----------------------

The **JValues** collection contains diagnostics for photolysis rates for
various chemical species, obtained from the photolysis mechanism.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     JValues.template:           '%y4%m2%d2_%h2%n2z.nc4',
     JValues.format:             'CFIO',
     JValues.frequency:          00000000 010000
     JValues.duration:           00000000 010000
     JValues.mode:               'instantaneous'
     JValues.fields:             'Jval_?PHO?',
                                 'JvalO3O1D ',
				 'JvalO3O3P ',
   ::

**List of diagnostic quantities**

+--------------------+-------------------------------+--------+----------+
| Diagnostic field   | Description                   | Units  | Wildcard |
+====================+===============================+========+==========+
| Jval_<name|wc>     | Photolysis rates              | 1/s    | ?PHO?    |
+--------------------+-------------------------------+--------+----------+
| JvalO3O1D          | Photolysis rate of O3         | 1/s    |          |
|                    | :math:`\rightarrow` O1D       |        |          |
+--------------------+-------------------------------+--------+----------+
| JvalO3O3P          | Photolysis rate of O3         | 1/s    |          |
|                    | :math:`\rightarrow` O3P       |        |          |
+--------------------+-------------------------------+--------+----------+

.. _histguide-kppardiags:

The KppARDiags collection
-------------------------

The **KppARDiags** collection contains diagnostics for the KPP
Rosenbrock solver with mechanism  autoreduction. You may leave this
collection disabled unless you are interested in assessing the
performance of the mechanism auto-reduction in KPP.

**Sample definition section for HISTORY.rc**

.. code-block:: none

     KppARDiags.template:        '%y4%m2%d2_%h2%n2z.nc4',
     KppARDiags.frequency:       00000100 000000
     KppARDiags.duration:        00000100 000000
     KppARDiags.mode:            'time-averaged'
     KppARDiags.fields:          'KppAutoReducerNVAR',
                                 'KppAutoReduceThres',
                                 'KppcNONZERO       ',
   ::

**List of diagnostic quantities**

+--------------------+-------------------------------------+-------------+
| Diagnostic field   | Description                         | Units       |
+====================+=====================================+=============+
| KppAutoReducerNVAR | Number of species (``rNVAR``) in    | count       |
|                    | the auto-reduced mechanism          |             |
+--------------------+-------------------------------------+-------------+
| KppAutoReduceThres | Auto-reduction threshold            | molec/cm3/s |
+--------------------+-------------------------------------+-------------+
| KppcNONZERO        | Number of nonzero elements          | count       |
|                    | (``cNONZERO``) in LU decomposition  |             |
|                    | in the auto-reduced mechanism       |             |
+--------------------+-------------------------------------+-------------+

.. _histguide-kppdiags:

The KppDiags collection
-----------------------

The **KppDiags** collection contains KPP solver diagnostics. You may
leave this collection disabled unless you are interested in assessing
the solver's performance.

**Sample definition section for HISTORY.rc**

.. code-block:: none

    KppDiags.template:          '%y4%m2%d2_%h2%n2z.nc4',
    KppDiags.format:            'CFIO',
    KppDiags.frequency:         00000100 000000
    KppDiags.duration:          00000100 000000
    KppDiags.mode:              'time-averaged'
    KppDiags.fields:            'KppIntCounts',
                                'KppJacCounts',
                                'KppTotSteps ',
                                'KppAccSteps ',
                                'KppRejSteps ',
                                'KppLuDecomps',
                                'KppSubsts   ',
                                'KppSmDecomps',
   ::

**List of diagnostic quantities**

+-------------------+-------------------------------------------+---------+
| Diagnostic field  | Description                               | Units   |
+===================+===========================================+=========+
| KppIntCounts      | Number of times the KPP integrator        | count   |
|                   | was called                                |         |
+-------------------+-------------------------------------------+---------+
| KppJacCounts      | Number of times the KPP Jacobian matrix   | count   |
|                   | was constructed                           |         |
+-------------------+-------------------------------------------+---------+
| KppTotSteps       | Total number of integration timesteps     | count   |
+-------------------+-------------------------------------------+---------+
| KppAccSteps       | Number of accepted integration timesteps  | count   |
+-------------------+-------------------------------------------+---------+
| KppRejSteps       | Number of rejected integration timesteps  | count   |
+-------------------+-------------------------------------------+---------+
| KppLuDecomps      | Number of LU decompositions performed     | count   |
+-------------------+-------------------------------------------+---------+
| KppSubsts         | Number of matrix substitutions performed  | count   |
|                   | (both forward & backward substitutions)   |         |
+-------------------+-------------------------------------------+---------+
| KppSmDecomps\     | Number of singular matrix decompositions  | count   |
| [#f8]_            | performed                                 |         |
+-------------------+-------------------------------------------+---------+

.. rubric:: Footnotes

.. [#f8] For Rosenbrock solvers, KppSmDecomps will be zero everywhere,
	 because the Rosenbrock method utilizes LU decomposition
	 instead of singular matrix decomposition.

.. _the_metrics_collection:

The Metrics collection
----------------------

The **Metrics** collection contains diagnostics for computing OH metrics
from a GEOS-Chem full chemistry simulation. To compute the OH metrics,
you must run the Python script metrics.py that ships with each
fullchem or CH4 run directory.

  Metrics.template:    '%y4%m2%d2_%h2%n2z.nc4',
  Metrics.format:      'CFIO',
  Metrics.frequency:   'End',
  Metrics.duration:    'End',
  Metrics.mode:        'time-averaged'
  Metrics.fields:      'AirMassColumnFull             ', 'GIGCchem',
                       'CH4emission                   ', 'GIGCchem',
                       'CH4massColumnFull             ', 'GIGCchem'
                       'CH4massColumnTrop             ', 'GIGCchem',
                       'LossOHbyCH4columnTrop         ', 'GIGCchem',
                       'LossOHbyMCFcolumnTrop         ', 'GIGCchem',
                       'OHwgtByAirMassColumnFull      ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
ProdLoss collection.

+---------+---------+---------+---------+---------+-------+---------+
| Dia     | Desc    | Units   | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |         | ldcards | lations |       | equiv.  |
| name    |         |         |         |         |       |  <List_ |
|         |         |         |         |         |       | of_diag |
|         |         |         |         |         |       | nostics |
|         |         |         |         |         |       | _archiv |
|         |         |         |         |         |       | ed_to_b |
|         |         |         |         |         |       | pch_for |
|         |         |         |         |         |       | mat>`__ |
+=========+=========+=========+=========+=========+=======+=========+
| Air     | Air     | kg      |         | -  f    |       |         |
| MassCol | mass,   |         |         | ullchem |       |         |
| umnFull | full    |         |         |         |       |         |
|         | atm     |         |         |         |       |         |
|         | osphere |         |         |         |       |         |
|         | column  |         |         |         |       |         |
|         | sums    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| LossOHb | Loss    | molec   |         | -  f    |       |         |
| yCH4col | rate of | cm-3    |         | ullchem |       |         |
| umnTrop | CH4 by  |         |         |         |       |         |
|         | OH,     |         |         |         |       |         |
|         | tropo   |         |         |         |       |         |
|         | spheric |         |         |         |       |         |
|         | column  |         |         |         |       |         |
|         | sums    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| LossOHb | Loss    | molec   |         | -  f    |       |         |
| yMCFcol | rate of | cm-3    |         | ullchem |       |         |
| umnTrop | CH3CCl3 |         |         |         |       |         |
|         | (aka    |         |         |         |       |         |
|         | MCF) by |         |         |         |       |         |
|         | OH,     |         |         |         |       |         |
|         | tropo   |         |         |         |       |         |
|         | spheric |         |         |         |       |         |
|         | column  |         |         |         |       |         |
|         | sums    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| OHw     | Ai      | kg air  |         | -  f    |       |         |
| gtByAir | rmass-w | kg OH   |         | ullchem |       |         |
| MassCol | eighted | m-3     |         |         |       |         |
| umnFull | mean OH |         |         |         |       |         |
|         | concent |         |         |         |       |         |
|         | ration, |         |         |         |       |         |
|         | f       |         |         |         |       |         |
|         | ull-atm |         |         |         |       |         |
|         | osphere |         |         |         |       |         |
|         | column  |         |         |         |       |         |
|         | sums    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| app     |         |         |         |         |       |         |
| licable |         |         |         |         |       |         |
| only to |         |         |         |         |       |         |
| the CH4 |         |         |         |         |       |         |
| or      |         |         |         |         |       |         |
| tagCH4  |         |         |         |         |       |         |
| simu    |         |         |         |         |       |         |
| lations |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| CH4e    | CH4     | kg s-1  |         | -  CH4  |       |         |
| mission | em      |         |         |         |       |         |
|         | ission, |         |         |         |       |         |
|         | used    |         |         |         |       |         |
|         | for     |         |         |         |       |         |
|         | co      |         |         |         |       |         |
|         | mputing |         |         |         |       |         |
|         | OH      |         |         |         |       |         |
|         | metrics |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| CH4     | Ai      | kg air  |         | -  CH4  |       |         |
| massCol | rmass-w | kg CH4  |         |         |       |         |
| umnFull | eighted | m-3     |         |         |       |         |
|         | CH4     |         |         |         |       |         |
|         | concent |         |         |         |       |         |
|         | ration, |         |         |         |       |         |
|         | f       |         |         |         |       |         |
|         | ull-atm |         |         |         |       |         |
|         | osphere |         |         |         |       |         |
|         | column  |         |         |         |       |         |
|         | sums    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| CH4     | Ai      | kg air  |         | -  CH4  |       |         |
| massCol | rmass-w | kg CH4  |         |         |       |         |
| umnTrop | eighted | m-3     |         |         |       |         |
|         | CH4     |         |         |         |       |         |
|         | concent |         |         |         |       |         |
|         | ration, |         |         |         |       |         |
|         | tr      |         |         |         |       |         |
|         | oposphe |         |         |         |       |         |
|         | re-only |         |         |         |       |         |
|         | column  |         |         |         |       |         |
|         | sums    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+

.. _the_prodloss_collection:

The ProdLoss Collection
-----------------------

The **ProdLoss** collection contains chemical production and loss rates.

Here is a sample definition section for the ProdLoss collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.
-  *NOTE: This example is for the*\ **benchmark**\ *simulation. Some
   quantities in this collection are not applicable to certain
   simulations.*

  ProdLoss.template:          '%y4%m2%d2_%h2%n2z.nc4',
  ProdLoss.format:            'CFIO',
  ProdLoss.frequency:         00000100 000000
  ProdLoss.duration:          00000100 000000
  ProdLoss.mode:              'time-averaged'
  ProdLoss.fields:            'Prod_?PRD?                    ', 'GIGCchem',
                              'ProdBCPIfromBCPO              ', 'GIGCchem',
                              'ProdOCPIfromOCPO              ', 'GIGCchem',
                              'ProdSO4fromH2O2inCloud        ', 'GIGCchem',
                              'ProdSO4fromO2inCloudMetal     ', 'GIGCchem',
                              'ProdSO4fromO3inCloud          ', 'GIGCchem',
                              'ProdSO4fromO3inSeaSalt        ', 'GIGCchem',
                              'ProdSO4fromHOBrInCloud        ', 'GIGCchem',
                              'ProdSO4fromSRO3               ', 'GIGCchem',
                              'ProdSO4fromSRHObr             ', 'GIGCchem',
                              'ProdSO4fromO3s                ', 'GIGCchem',
                              'Loss_?LOS?                    ', 'GIGCchem',
                              'LossHNO3onSeaSalt             ', 'GIGCchem',
                              'ProdCOfromCH4                 ', 'GIGCchem',
                              'ProdCOfromNMVOC               ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
ProdLoss collection.

-  *NOTE:*\ **fullchem**\ *refers to all simulations that use*\ `a
   full-chemistry
   mechanism <GEOS-Chem_chemistry_mechanisms#Mechanisms_for_GEOS-Chem_v11-02>`__\ *(i.e.
   benchmark, complexSOA*, standard, tropchem. aciduptake, marinePOA,
   RRTMG, TOMAS).*

+---------+---------+---------+---------+---------+-------+---------+
| Dia     | Desc    | Units   | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |         | ldcards | lations |       | equiv.  |
| name    |         |         |         |         |       |  <List_ |
|         |         |         |         |         |       | of_diag |
|         |         |         |         |         |       | nostics |
|         |         |         |         |         |       | _archiv |
|         |         |         |         |         |       | ed_to_b |
|         |         |         |         |         |       | pch_for |
|         |         |         |         |         |       | mat>`__ |
+=========+=========+=========+=========+=========+=======+=========+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| app     |         |         |         |         |       |         |
| licable |         |         |         |         |       |         |
| only to |         |         |         |         |       |         |
| the     |         |         |         |         |       |         |
| f       |         |         |         |         |       |         |
| ullchem |         |         |         |         |       |         |
| sim     |         |         |         |         |       |         |
| ulation |         |         |         |         |       |         |
| with    |         |         |         |         |       |         |
| the     |         |         |         |         |       |         |
| aci     |         |         |         |         |       |         |
| duptake |         |         |         |         |       |         |
| option  |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdS   | Pro     | kg S/s  |         | -  f    |       |         |
| O4fromO | duction |         |         | ullchem |       |         |
| xidatio | of SO4  |         |         |    w/   |       |         |
| nOnDust | from    |         |         |    aci  |       |         |
|         | ox      |         |         | duptake |       |         |
|         | idation |         |         |         |       |         |
|         | on dust |         |         |         |       |         |
|         | a       |         |         |         |       |         |
|         | erosols |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdNI  | Pro     | kg N/s  |         |         |       |         |
| TfromHN | duction |         |         |         |       |         |
| O3uptak | of NIT  |         |         |         |       |         |
| eOnDust | from    |         |         |         |       |         |
|         | HNO3    |         |         |         |       |         |
|         | uptake  |         |         |         |       |         |
|         | on dust |         |         |         |       |         |
|         | a       |         |         |         |       |         |
|         | erosols |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Prod    | Pro     | kg S/s  |         | -  f    |       |         |
| SO4from | duction |         |         | ullchem |       |         |
| UptakeO | of SO4  |         |         |    w/   |       |         |
| fH2SO4g | from    |         |         |    aci  |       |         |
|         | uptake  |         |         | duptake |       |         |
|         | of      |         |         |         |       |         |
|         | H       |         |         |         |       |         |
|         | 2SO4(g) |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| app     |         |         |         |         |       |         |
| licable |         |         |         |         |       |         |
| only to |         |         |         |         |       |         |
| the     |         |         |         |         |       |         |
| aeros   |         |         |         |         |       |         |
| ol-only |         |         |         |         |       |         |
| sim     |         |         |         |         |       |         |
| ulation |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdS   | Pro     | kg S/s  |         | -       |       |         |
| O2fromD | duction |         |         |   aeros |       |         |
| MSandOH | of SO2  |         |         | ol-only |       |         |
|         | from    |         |         |         |       |         |
|         | DMS +   |         |         |         |       |         |
|         | OH (in  |         |         |         |       |         |
|         | sulfate |         |         |         |       |         |
|         | _mod.F) |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdSO  | Pro     | kg S/s  |         |         |       |         |
| 2fromDM | duction |         |         |         |       |         |
| SandNO3 | of SO2  |         |         |         |       |         |
|         | from    |         |         |         |       |         |
|         | DMS +   |         |         |         |       |         |
|         | NO3R    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdSO2 | Total   | kg S/s  |         | -       |       |         |
| fromDMS | P(SO2)  |         |         |   aeros |       |         |
|         | from    |         |         | ol-only |       |         |
|         | DMS     |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdMSA | Pro     | kg S/s  |         | -       |       | -       |
| fromDMS | duction |         |         |   aeros |       |   `ND05 |
|         | of MSA  |         |         | ol-only |       |         |
|         | from    |         |         |         |       | #4 <Lis |
|         | DMS     |         |         |         |       | t_of_di |
|         |         |         |         |         |       | agnosti |
|         |         |         |         |         |       | cs_arch |
|         |         |         |         |         |       | ived_to |
|         |         |         |         |         |       | _bpch_f |
|         |         |         |         |         |       | ormat#N |
|         |         |         |         |         |       | D05:_P. |
|         |         |         |         |         |       | 2FL_for |
|         |         |         |         |         |       | _sulfat |
|         |         |         |         |         |       | e_aeros |
|         |         |         |         |         |       | ols>`__ |
+---------+---------+---------+---------+---------+-------+---------+
| ProdS   | Pro     | kg S/s  |         | -       |       |         |
| O4fromG | duction |         |         |   aeros |       |         |
| asPhase | of SO4  |         |         | ol-only |       |         |
|         | in the  |         |         |         |       |         |
|         | gas     |         |         |         |       |         |
|         | phase   |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| app     |         |         |         |         |       |         |
| licable |         |         |         |         |       |         |
| only to |         |         |         |         |       |         |
| the     |         |         |         |         |       |         |
| aeros   |         |         |         |         |       |         |
| ol-only |         |         |         |         |       |         |
| and     |         |         |         |         |       |         |
| f       |         |         |         |         |       |         |
| ullchem |         |         |         |         |       |         |
| simu    |         |         |         |         |       |         |
| lations |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Pr      | Pro     | kg      |         | -       |       | -       |
| odBCPIf | duction |         |         |   aeros |       |   `ND07 |
| romBCPO | of      |         |         | ol-only |       |         |
|         | hydr    |         |         | -  f    |       |   #4 <L |
|         | ophilic |         |         | ullchem |       | ist_of_ |
|         | BC from |         |         |         |       | diagnos |
|         | hydr    |         |         |         |       | tics_ar |
|         | ophobic |         |         |         |       | chived_ |
|         | BCs     |         |         |         |       | to_bpch |
|         |         |         |         |         |       | _format |
|         |         |         |         |         |       | #ND07:_ |
|         |         |         |         |         |       | BC_and_ |
|         |         |         |         |         |       | OC_sour |
|         |         |         |         |         |       | ces>`__ |
+---------+---------+---------+---------+---------+-------+---------+
| Pr      | Pro     | kg      |         | -       |       |         |
| odOCPIf | duction |         |         |   aeros |       |         |
| romOCPO | of      |         |         | ol-only |       |         |
|         | hydr    |         |         | -  f    |       |         |
|         | ophilic |         |         | ullchem |       |         |
|         | BC from |         |         |         |       |         |
|         | hydr    |         |         |         |       |         |
|         | ophobic |         |         |         |       |         |
|         | BCs     |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| P       | Pro     | kg S/s  |         | -       |       |         |
| rodSO4f | duction |         |         |   aeros |       |         |
| romH2O2 | of SO4  |         |         | ol-only |       |         |
| inCloud | from    |         |         | -  f    |       |         |
|         | aqueous |         |         | ullchem |       |         |
|         | ox      |         |         |         |       |         |
|         | idation |         |         |         |       |         |
|         | of H2O2 |         |         |         |       |         |
|         | in      |         |         |         |       |         |
|         | clouds  |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Prod    | Pro     | kg S/s  |         | -       |       |         |
| SO4from | duction |         |         |   aeros |       |         |
| O2inClo | of SO4  |         |         | ol-only |       |         |
| udMetal | from    |         |         | -  f    |       |         |
|         | aqueous |         |         | ullchem |       |         |
|         | ox      |         |         |         |       |         |
|         | idation |         |         |         |       |         |
|         | of O2   |         |         |         |       |         |
|         | from    |         |         |         |       |         |
|         | metals  |         |         |         |       |         |
|         | in      |         |         |         |       |         |
|         | cloud   |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdSO  | Pro     | kg S/s  |         | -       |       |         |
| 4fromO3 | duction |         |         |   aeros |       |         |
| inCloud | of SO4  |         |         | ol-only |       |         |
|         | from    |         |         | -  f    |       |         |
|         | aqueous |         |         | ullchem |       |         |
|         | ox      |         |         |         |       |         |
|         | idation |         |         |         |       |         |
|         | of O3   |         |         |         |       |         |
|         | in      |         |         |         |       |         |
|         | clouds  |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| P       | Pro     | kg S/s  |         | -       |       |         |
| rodSO4f | duction |         |         |   aeros |       |         |
| romO3in | of SO4  |         |         | ol-only |       |         |
| SeaSalt | from O3 |         |         | -  f    |       |         |
|         | in sea  |         |         | ullchem |       |         |
|         | salt    |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdSO4 | Pro     | kg S/s  |         | -       |       |         |
| fromO3s | duction |         |         |   aeros |       |         |
|         | of SO4  |         |         | ol-only |       |         |
|         | from    |         |         | -  f    |       |         |
|         | aqueous |         |         | ullchem |       |         |
|         | phase   |         |         |         |       |         |
|         | SO3--   |         |         |         |       |         |
|         | loss by |         |         |         |       |         |
|         | OH      |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| P       | Pro     | kg S/s  |         | -       |       |         |
| rodSO4f | duction |         |         |   aeros |       |         |
| romSRO3 | of SO4  |         |         | ol-only |       |         |
|         | from    |         |         | -  f    |       |         |
|         | sulfur  |         |         | ullchem |       |         |
|         | pro     |         |         |         |       |         |
|         | duction |         |         |         |       |         |
|         | rate of |         |         |         |       |         |
|         | O3      |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Los     | Loss of | kg S/s  |         | -       |       |         |
| sHNO3on | HNO3 on |         |         |   aeros |       |         |
| SeaSalt | sea     |         |         | ol-only |       |         |
|         | salt    |         |         | -  f    |       |         |
|         | a       |         |         | ullchem |       |         |
|         | erosols |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| app     |         |         |         |         |       |         |
| licable |         |         |         |         |       |         |
| only to |         |         |         |         |       |         |
| full-ch |         |         |         |         |       |         |
| emistry |         |         |         |         |       |         |
| simu    |         |         |         |         |       |         |
| lations |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| P       | Pro     | kg S/s  |         | -  f    |       |         |
| rodSO4f | duction |         |         | ullchem |       |         |
| romHOBr | of SO4  |         |         |         |       |         |
| inCloud | from    |         |         |         |       |         |
|         | aqueous |         |         |         |       |         |
|         | ox      |         |         |         |       |         |
|         | idation |         |         |         |       |         |
|         | of HOBr |         |         |         |       |         |
|         | in      |         |         |         |       |         |
|         | clouds  |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Pro     | Pro     | kg S/s  |         | -  f    |       |         |
| dSO4fro | duction |         |         | ullchem |       |         |
| mSRHOBr | of SO4  |         |         |         |       |         |
|         | from    |         |         |         |       |         |
|         | sulfur  |         |         |         |       |         |
|         | pro     |         |         |         |       |         |
|         | duction |         |         |         |       |         |
|         | rate of |         |         |         |       |         |
|         | HOBr+O3 |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| ProdCO  | Pro     | molec   |         | -  f    |       |         |
| fromCH4 | duction | cm-3    |         | ullchem |       |         |
|         | of CO   | s-1     |         |         |       |         |
|         | from    |         |         |         |       |         |
|         | CH4     |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| P       | Pro     | molec   |         | -  f    |       |         |
| rodCOfr | duction | cm-3    |         | ullchem |       |         |
| omNMVOC | of CO   | s-1     |         |         |       |         |
|         | from    |         |         |         |       |         |
|         | NMVOCs  |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| for     |         |         |         |         |       |         |
| pro     |         |         |         |         |       |         |
| duction |         |         |         |         |       |         |
| and     |         |         |         |         |       |         |
| loss of |         |         |         |         |       |         |
| species |         |         |         |         |       |         |
| or      |         |         |         |         |       |         |
| c       |         |         |         |         |       |         |
| hemical |         |         |         |         |       |         |
| f       |         |         |         |         |       |         |
| amilies |         |         |         |         |       |         |
| (e.g.   |         |         |         |         |       |         |
| Ox)     |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Prod\_  | C       | mole    | -       | -  f    |       |         |
|         | hemical | c/cm3/s |   ?PRD? | ullchem |       |         |
|         | pro     |         |         | -       |       |         |
|         | duction |         |         |   tagCO |       |         |
|         | for a   |         |         | -       |       |         |
|         | given   |         |         |   tagO3 |       |         |
|         | species |         |         |         |       |         |
|         | or      |         |         |         |       |         |
|         | family  |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Loss\_  | C       | mole    | -       | -  f    |       |         |
|         | hemical | c/cm3/s |   ?LOS? | ullchem |       |         |
|         | loss    |         |         | -       |       |         |
|         | for a   |         |         |   tagCO |       |         |
|         | given   |         |         | -       |       |         |
|         | species |         |         |   tagO3 |       |         |
|         | or      |         |         |         |       |         |
|         | family  |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+

.. _the_rxnrates_collection:

The RxnRates Collection
-----------------------

The **RxnRates** collection contains reaction rates from the chemical
mechanism (as computed by the KPP-generated solver code). For example,
in the case of the NO + O\ :sub:`3` → NO\ :sub:`2` + O\ :sub:`2`
reaction the returned quantity is k[NO][O\ :sub:`3`] in
molec/cm\ :sup:`3`/s.

Here is a sample definition section for the RxnRates collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

 #
 # It is best to list individual reactions to avoid using too much memory.
 # Reactions should be listed as "RxnRate_EQnnn", where nnn is the reaction
 # index as listed in KPP/fullchem/gckpp_Monitor.F90 or KPP/Hg/gckpp_monitor.F90
 # (pad zeroes as needed)
 #
 RxnRates.template:   '%y4%m2%d2_%h2%n2z.nc4',
 RxnRates.frequency:  00000000 010000
 RxnRates.duration:   00000000 010000
 RxnRates.mode:       'time-averaged'
 RxnRates.fields:     'RxnRate_EQ001                           ',
                      'RxnRate_EQ002                           ',

The table below describes diagnostic quantities belonging to the
ProdLoss collection.

-  *NOTE:*\ **fullchem**\ *refers to all simulations that use*\ `a
   full-chemistry
   mechanism <GEOS-Chem_chemistry_mechanisms#Mechanisms_for_GEOS-Chem_v11-02>`__\ *(i.e.
   benchmark, complexSOA*, standard, tropchem. aciduptake, marinePOA,
   RRTMG, TOMAS).*

+---------+---------+---------+---------+---------+-------+---------+
| Dia     | Desc    | Units   | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |         | ldcards | lations |       | equiv.  |
| name    |         |         |         |         |       |  <List_ |
|         |         |         |         |         |       | of_diag |
|         |         |         |         |         |       | nostics |
|         |         |         |         |         |       | _archiv |
|         |         |         |         |         |       | ed_to_b |
|         |         |         |         |         |       | pch_for |
|         |         |         |         |         |       | mat>`__ |
+=========+=========+=========+=========+=========+=======+=========+
| Diag    |         |         |         |         |       |         |
| nostics |         |         |         |         |       |         |
| app     |         |         |         |         |       |         |
| licable |         |         |         |         |       |         |
| only to |         |         |         |         |       |         |
| the     |         |         |         |         |       |         |
| f       |         |         |         |         |       |         |
| ullchem |         |         |         |         |       |         |
| sim     |         |         |         |         |       |         |
| ulation |         |         |         |         |       |         |
| with    |         |         |         |         |       |         |
| the     |         |         |         |         |       |         |
| aci     |         |         |         |         |       |         |
| duptake |         |         |         |         |       |         |
| option  |         |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| RxnRat  | Rate    | s\ :s   | -       | -  f    |       |         |
| e_EQnnn | for     | up:`-1` |   ?RXN? | ullchem |       |         |
|         | r       |         |         | -  Hg   |       |         |
|         | eaction |         |         | -       |       |         |
|         | nnn     |         |         |  carbon |       |         |
|         | (see    |         |         |         |       |         |
|         gckpp |         |         |         |       |         |
|         | _Monito |         |         |         |       |         |
|         | r.F90 |         |         |         |       |         |
|         | for the |         |         |         |       |         |
|         | r       |         |         |         |       |         |
|         | eaction |         |         |         |       |         |
|         | number. |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+

.. _the_statechm_collection:

The StateChm Collection
-----------------------

The '''StateChm ''' collection contains quantities from State_Chm,
the Chemistry state object (other than the species concentrations, which
are stored in the SpeciesConc collection).

Here is a sample definition section for the StateChm collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Other fields of the State_Chm object can be added to this
   collection by prefixing the field name with Chem_.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  StateChm.template:          '%y4%m2%d2_%h2%n2z.nc4',
  StateChm.format:            'CFIO',
  StateChm.frequency:         00000100 000000
  StateChm.duration:          00000100 000000
  StateChm.mode:              'time-averaged'
  StateChm.fields:            'Chem_phSav                    ', 'GIGCchem',
                              'Chem_HplusSav                 ', 'GIGCchem',
                              'Chem_WaterSav                 ', 'GIGCchem',
                              'Chem_SulRatSav                ', 'GIGCchem',
                              'Chem_NaRatSav                 ', 'GIGCchem',
                              'Chem_AcidPurSav               ', 'GIGCchem',
                              'Chem_BiSulSav                 ', 'GIGCchem',
                              'Chem_pHCloud                  ', 'GIGCchem',
                              'Chem_SSAlk',                  ', 'GIGCchem',
                              'Chem_HSO3AQ                   ', 'GIGCchem',
                              'Chem_SO3AQ                    ', 'GIGCchem',
                              'Chem_fupdateHOBr              ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
StateChm collection.

-  *NOTE:*\ **fullchem**\ *refers to all simulations that use*\ `a
   full-chemistry
   mechanism <GEOS-Chem_chemistry_mechanisms#Mechanisms_for_GEOS-Chem_v11-02>`__\ *(i.e.
   benchmark, complexSOA*, standard, tropchem. aciduptake, marinePOA,
   RRTMG, TOMAS).*

+---------+---------+-------+---------+---------+-------+---------+
| Dia     | Desc    | Units | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |       | ldcards | lations |       | equiv.  |
| name    |         |       |         |         |       |  <List_ |
|         |         |       |         |         |       | of_diag |
|         |         |       |         |         |       | nostics |
|         |         |       |         |         |       | _archiv |
|         |         |       |         |         |       | ed_to_b |
|         |         |       |         |         |       | pch_for |
|         |         |       |         |         |       | mat>`__ |
+=========+=========+=======+=========+=========+=======+=========+
| Che     | IS      | 1     |         |         |       |         |
| m_phSav | ORROPIA |       |         |         |       |         |
|         | aerosol |       |         |         |       |         |
|         | pH      |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Chem_H  | IS      | M     |         | -  f    |       | -       |
| plusSav | ORROPIA |       |         | ullchem |       |   `ND73 |
|         | H+      |       |         |         |       |    #2   |
|         | concen  |       |         |         |       |  <List_ |
|         | tration |       |         |         |       | of_diag |
|         |         |       |         |         |       | nostics |
|         |         |       |         |         |       | _archiv |
|         |         |       |         |         |       | ed_to_b |
|         |         |       |         |         |       | pch_for |
|         |         |       |         |         |       | mat#ND7 |
|         |         |       |         |         |       | 3:_ISOR |
|         |         |       |         |         |       | ROPIA_d |
|         |         |       |         |         |       | iagnost |
|         |         |       |         |         |       | ics>`__ |
+---------+---------+-------+---------+---------+-------+---------+
| Chem_W  | IS      | μg/m3 |         | -  f    |       |         |
| aterSav | ORROPIA |       |         | ullchem |       |         |
|         | aerosol |       |         |         |       |         |
|         | water   |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Chem_Su | IS      | M     |         | -  f    |       |         |
| lRatSav | ORROPIA |       |         | ullchem |       |         |
|         | sulfate |       |         |         |       |         |
|         | concen  |       |         |         |       |         |
|         | tration |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Chem_N  | IS      | M     |         | -  f    |       |         |
| aRatSav | ORROPIA |       |         | ullchem |       |         |
|         | Na+     |       |         |         |       |         |
|         | concen  |       |         |         |       |         |
|         | tration |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| C       | IS      | M     |         | -  f    |       |         |
| hem_Aci | ORROPIA |       |         | ullchem |       |         |
| dPurSav | acidpur |       |         |         |       |         |
|         | ??      |       |         |         |       |         |
|         | concen  |       |         |         |       |         |
|         | tration |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Chem_B  | IS      | M     |         | -  f    |       |         |
| iSulSav | ORROPIA |       |         | ullchem |       |         |
|         | bi      |       |         |         |       |         |
|         | sulfate |       |         |         |       |         |
|         | (       |       |         |         |       |         |
|         | general |       |         |         |       |         |
|         | acid)   |       |         |         |       |         |
|         | concen  |       |         |         |       |         |
|         | tration |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Chem_   | Cloud   | 1     |         | -  f    |       |         |
| phCloud | PH      |       |         | ullchem |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Che     | Sea     | 1     |         | -  f    |       |         |
| m_SSAlk | salt    |       |         | ullchem |       |         |
|         | alk     |       |         |         |       |         |
|         | alinity |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Chem    | Cloud   | mol/L |         | -  f    |       |         |
| _HSO3AQ | bi      |       |         | ullchem |       |         |
|         | sulfite |       |         |         |       |         |
|         | concen  |       |         |         |       |         |
|         | tration |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Che     | Cloud   | mol/L |         | -  f    |       |         |
| m_SO3AQ | sulfite |       |         | ullchem |       |         |
|         | concen  |       |         |         |       |         |
|         | tration |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Ch      | Cor     | mol/L |         | -  f    |       |         |
| em_fupd | rection |       |         | ullchem |       |         |
| ateHOBr | factor  |       |         |         |       |         |
|         | for     |       |         |         |       |         |
|         | HOBr    |       |         |         |       |         |
|         | removal |       |         |         |       |         |
|         | by SO2  |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+



.. _the_wetlossconv_collection:

The WetLossConv collection
--------------------------

The **WetLossConv** collection contains diagnostics fluxes of soluble
species lost to wet scaveinging in convective updrafts.

Here is a sample definition section for the WetLossConv collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  WetLossConv.template:       '%y4%m2%d2_%h2%n2z.nc4',
  WetLossConv.format:         'CFIO',
  WetLossConv.frequency:      00000100 000000
  WetLossConv.duration:       00000100 000000
  WetLossConv.mode:           'time-averaged'
  WetLossConv.fields:         'WetLossConv_?WET?             ', 'GIGCchem',
                              'WetLossConvFrac_?WET?         ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
WetLossConv collection.

+---------+---------+-------+---------+---------+-------+---------+
| Dia     | Desc    | Units | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |       | ldcards | lations |       | equiv.  |
| name    |         |       |         |         |       |  <List_ |
|         |         |       |         |         |       | of_diag |
|         |         |       |         |         |       | nostics |
|         |         |       |         |         |       | _archiv |
|         |         |       |         |         |       | ed_to_b |
|         |         |       |         |         |       | pch_for |
|         |         |       |         |         |       | mat>`__ |
+=========+=========+=======+=========+=========+=======+=========+
| WetLos  | Loss of | kg/s  | -       | -  all  |       |         |
| sConv\_ | soluble |       |   ?WET? |    simu |       |         |
|         | species |       |         | lations |       |         |
|         | sc      |       |         |         |       |         |
|         | avenged |       |         |         |       |         |
|         | by      |       |         |         |       |         |
|         | cloud   |       |         |         |       |         |
|         | u       |       |         |         |       |         |
|         | pdrafts |       |         |         |       |         |
|         | in      |       |         |         |       |         |
|         | moist   |       |         |         |       |         |
|         | con     |       |         |         |       |         |
|         | vection |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+
| Wet     | F       | 1     | -       | -  all  |       |         |
| LossCon | raction |       |   ?WET? |    simu |       |         |
| vFrac\_ | of      |       |         | lations |       |         |
|         | species |       |         |         |       |         |
|         | sc      |       |         |         |       |         |
|         | avenged |       |         |         |       |         |
|         | by      |       |         |         |       |         |
|         | cloud   |       |         |         |       |         |
|         | u       |       |         |         |       |         |
|         | pdrafts |       |         |         |       |         |
|         | in      |       |         |         |       |         |
|         | moist   |       |         |         |       |         |
|         | con     |       |         |         |       |         |
|         | vection |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+

.. _the_wetlossls_collection:

The WetLossLS collection
------------------------

The **WetLossLS** collection contains diagnostics fluxes of soluble
species lost to rainout and washout in large-scale wet deposition.

Here is a sample definition section for the WetLossConv collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  WetLossLS.template:         '%y4%m2%d2_%h2%n2z.nc4',
  WetLossLS.format:           'CFIO',
  WetLossLS.frequency:        010000
  WetLossLS.duration:         010000
  WetLossLS.mode:             'time-averaged'
  WetLossLS.fields:           'WetLossLS_?WET?               ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
WetLossLS collection.

+---------+---------+-------+---------+---------+-------+---------+
| Dia     | Desc    | Units | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |       | ldcards | lations |       | equiv.  |
| name    |         |       |         |         |       |  <List_ |
|         |         |       |         |         |       | of_diag |
|         |         |       |         |         |       | nostics |
|         |         |       |         |         |       | _archiv |
|         |         |       |         |         |       | ed_to_b |
|         |         |       |         |         |       | pch_for |
|         |         |       |         |         |       | mat>`__ |
+=========+=========+=======+=========+=========+=======+=========+
| WetL    | Loss of | kg/s  | -       | -  all  |       |         |
| ossLS\_ | soluble |       |   ?WET? |    simu |       |         |
|         | species |       |         | lations |       |         |
|         | in      |       |         |         |       |         |
|         | larg    |       |         |         |       |         |
|         | e-scale |       |         |         |       |         |
|         | precip  |       |         |         |       |         |
|         | itation |       |         |         |       |         |
+---------+---------+-------+---------+---------+-------+---------+

.. _the_concabovesfc_collection:

The ConcAboveSfc collection
---------------------------

The **ConcAboveSfc** diagnostic collection uses dry deposition
quantities (surface resistance, dry deposition velocity) to compute the
species concentration of O3 and HNO3 at a given altitude (such as 10 m)
above the surface. This will facilitate comparison between GEOS-Chem and
observational networks (e.g. CASTNET), which often place instruments
above the canopy at approx. 10m height.

Here is a sample definition section for the ConcAboveSfc collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.
-  The ConcAboveSfc collection is best used as a timeseries diagnostic
   (set mode to "instantaneous").

*NOTE: If dry deposition is turned off in your simulation, then you must
disable this collection, or else your run will stop with an error.*

  ConcAboveSfc.template:      '%y4%m2%d2_%h2%n2z.nc4',
  ConcAboveSfc.format:        'CFIO',
  ConcAboveSfc.frequency:     00000000 010000
  ConcAboveSfc.duration:      00000000 010000
  ConcAboveSfc.mode:          'instantaneous'
  ConcAboveSfc.fields:        'DryDepRaALT1                  ', 'GIGCchem',
                              'DryDepVelForALT1_?DRYALT?     ', 'GIGCchem',
                              'SpeciesConcALT1_?DRYALT?      ', 'GIGCchem',
::

This table describes the diagnostic quantities belonging to the
ConcAboveSfc collection:

+----------+----------+----------+----------+----------+-------+
| Di       | Des      | Units    | W        | Sim      | Notes |
| agnostic | cription |          | ildcards | ulations |       |
| name     |          |          |          |          |       |
+==========+==========+==========+==========+==========+=======+
| DryD     | Dry      | s cm-1   |          | -        |       |
| epRaALT1 | de       |          |          | fullchem |       |
|          | position |          |          | -  tagO3 |       |
|          | aer      |          |          |          |       |
|          | odynamic |          |          |          |       |
|          | re       |          |          |          |       |
|          | sistance |          |          |          |       |
|          | computed |          |          |          |       |
|          | at ALT1  |          |          |          |       |
|          | meters   |          |          |          |       |
|          | above    |          |          |          |       |
|          | the      |          |          |          |       |
|          | surface  |          |          |          |       |
+----------+----------+----------+----------+----------+-------+
| Dr       | Dry      | cm s-1   | -        | -        |       |
| yDepVelf | de       |          | ?DRYALT? | fullchem |       |
| orALT1\_ | position |          |          | -  tagO3 |       |
|          | velocity |          |          |          |       |
|          | for all  |          |          |          |       |
|          | species  |          |          |          |       |
|          | tagged   |          |          |          |       |
|          | with the |          |          |          |       |
|          | ?DRYALT? |          |          |          |       |
|          | tag.     |          |          |          |       |
+----------+----------+----------+----------+----------+-------+
| S        | Chemical | mol/mol  | -        | -        |       |
| peciesCo | species  | dry air  | ?DRYALT? | fullchem |       |
| ncALT1\_ | concen   |          |          | -  tagO3 |       |
|          | trations |          |          |          |       |
|          | of O3 or |          |          |          |       |
|          | HNO3 at  |          |          |          |       |
|          | a        |          |          |          |       |
|          | user-s   |          |          |          |       |
|          | pecified |          |          |          |       |
|          | height   |          |          |          |       |
|          | (e.g. 10 |          |          |          |       |
|          | m) above |          |          |          |       |
|          | the      |          |          |          |       |
|          | surface  |          |          |          |       |
+----------+----------+----------+----------+----------+-------+

.. _the_drydep_collection:

The DryDep collection
---------------------

The **DryDep** collection contains diagnostics for the flux and velocity
of each species lost to dry-deposition.

Here is a sample definition section for the DryDep collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  DryDep.template:            '%y4%m2%d2_%h2%n2z.nc4',
  DryDep.format:              'CFIO',
  DryDep.frequency:           00000100 000000
  DryDep.duration:            00000100 000000
  DryDep.mode:                'time-averaged'
  DryDep.fields:              'DryDepVel_?DRY?               ', 'GIGCchem',
                              'DryDepMix_?DRY?               ', 'GIGCchem',
                              'DryDepChm_?DRY?               ', 'GIGCchem',
                              'DryDep_?DRY?                  ', 'GIGCchem',
::

This table describes the diagnostic quantities belonging to the DryDep
collection:

+---------+---------+---------+---------+---------+-------+---------+
| Dia     | Desc    | Units   | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |         | ldcards | lations |       | equiv.  |
| name    |         |         |         |         |       |  <List_ |
|         |         |         |         |         |       | of_diag |
|         |         |         |         |         |       | nostics |
|         |         |         |         |         |       | _archiv |
|         |         |         |         |         |       | ed_to_b |
|         |         |         |         |         |       | pch_for |
|         |         |         |         |         |       | mat>`__ |
+=========+=========+=========+=========+=========+=======+=========+
| DryD    | Dry     | cm/s    | -       | -  all  |       |         |
| epVel\_ | dep     |         |   ?DRY? |    simu |       |         |
|         | osition |         |         | lations |       |         |
|         | v       |         |         |    with |       |         |
|         | elocity |         |         |         |       |         |
|         |         |         |         |  drydep |       |         |
|         |         |         |         |         |       |         |
|         |         |         |         | species |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| DryD    | | Dry   | mole    | -       | -  all  |       |         |
| epMix\_ |   dep   | c/cm2/s |   ?DRY? |    simu |       |         |
|         | osition |         |         | lations |       |         |
|         |   flux  |         |         |    with |       |         |
|         | | (c    |         |         |         |       |         |
|         | omputed |         |         |  drydep |       |         |
|         |   in    |         |         |         |       |         |
|         |   PBL   |         |         | species |       |         |
|         |         |         |         |         |       |         |
|         | mixing) |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| DryD    | | Dry   | mole    | -       | -  all  |       |         |
| epChm\_ |   dep   | c/cm2/s |   ?DRY? |    simu |       |         |
|         | osition |         |         | lations |       |         |
|         |   flux  |         |         |    with |       |         |
|         | | (c    |         |         |         |       |         |
|         | omputed |         |         |  drydep |       |         |
|         |   in    |         |         |         |       |         |
|         |   che   |         |         | species |       |         |
|         | mistry) |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| D       | Total   | mole    | -       | -  all  |       |         |
| ryDep\_ | dry     | c/cm2/s |   ?DRY? |    simu |       |         |
|         | dep     |         |         | lations |       |         |
|         | osition |         |         |    with |       |         |
|         | flux    |         |         |         |       |         |
|         |         |         |         |  drydep |       |         |
|         |         |         |         |         |       |         |
|         |         |         |         | species |       |         |
+---------+---------+---------+---------+---------+-------+---------+

.. _the_mercurychem_collection:

The MercuryChem collection
--------------------------

\ **NOTE: This collection has not yet been validated and should be
considered experimental.**\

The *'MercuryChem* collection contains concentrations and prod/loss
diagnostic outputs for the `Hg specialty simulationl <Mercury>`__

Here is a sample definition section for the MercuryEmis collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  MercuryChem.template:       '%y4%m2%d2_%h2%n2z.nc4',
  MercuryChem.format:         'CFIO',
  MercuryChem.frequency:      00000000 040000
  MercuryChem.duration:       00000000 040000
  MercuryChem.mode:           'time-averaged'
  MercuryChem.fields:         'ConcBr                        ', 'GIGCchem',
                              'ConcBrO                       ', 'GIGCchem',
                              'LossHg2bySeaSalt              ', 'GIGCchem',
                              'LossRateHg2bySeaSalt          ', 'GIGCchem',
                              'PolarConcBr                   ', 'GIGCchem',
                              'PolarConcBrO                  ', 'GIGCchem',
                              'PolarConcO3                   ', 'GIGCchem',
                              'ProdHg2fromBr                 ', 'GIGCchem',
                              'ProdHg2fromBrY                ', 'GIGCchem',
                              'ProdHg2fromClY                ', 'GIGCchem',
                              'ProdHg2fromHg0                ', 'GIGCchem',
                              'ProdHg2fromHgBrPlusBr2        ', 'GIGCchem',
                              'ProdHg2fromHgBrPlusBrBrO      ', 'GIGCchem',
                              'ProdHg2fromHgBrPlusBrClO      ', 'GIGCchem',
                              'ProdHg2fromHgBrPlusBrHO2      ', 'GIGCchem',
                              'ProdHg2fromHgBrPlusBrNO2      ', 'GIGCchem',
                              'ProdHg2fromHgBrPlusBrOH       ', 'GIGCchem',
                              'ProdHg2fromO3                 ', 'GIGCchem',
                              'ProdHg2fromOH                 ', 'GIGCchem',
                              'ParticulateBoundHg            ', 'GIGCchem',
                              'ReactiveGaseousHg             ', 'GIGCchem',
::

The table below describes the diagnostic quantities belonging to the
MercuryChem collection:

+---------------------+---------------------+-----------+-----------+
| Diagnostic name     | Description         | Units     | Wildcards |
+=====================+=====================+===========+===========+
| ConcBr              | Br concentration    | molec/cm3 |           |
+---------------------+---------------------+-----------+-----------+
| ConcBrO             | BrO concentration   | molec/cm3 |           |
+---------------------+---------------------+-----------+-----------+
| LossHg2bySeaSalt    | Loss of Hg2 by      | kg/s      |           |
|                     | reaction with sea   |           |           |
|                     | salt aerosols       |           |           |
+---------------------+---------------------+-----------+-----------+
| L                   | Rate of loss of Hg2 | 1/s       |           |
| ossRateHg2bySeaSalt | by reaction with    |           |           |
|                     | sea salt aerosols   |           |           |
+---------------------+---------------------+-----------+-----------+
| PolarConcBr         | Br concentration in | pptv      |           |
|                     | polar regions       |           |           |
+---------------------+---------------------+-----------+-----------+
| PolarConcBrO        | BrO concentration   | molec/cm3 |           |
|                     | in polar regions    |           |           |
+---------------------+---------------------+-----------+-----------+
| PolarConcO3         | O3 concentration in | molec/cm3 |           |
|                     | polar regions       |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdHg2fromBr       | Chemical production | kg/s      |           |
|                     | of Hg2 from Br      |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdHg2fromBrY      | Chemical production | kg/s      |           |
|                     | of Hg2 from         |           |           |
|                     | reaction with BrY   |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdHg2fromClY      | Chemical production | kg/s      |           |
|                     | of Hg2 from         |           |           |
|                     | reaction with ClY   |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdHg2fromHg0      | Chemical production | kg/s      |           |
|                     | of Hg2 from         |           |           |
|                     | reaction with Hg0   |           |           |
+---------------------+---------------------+-----------+-----------+
| Pro                 | Chemical production | kg/s      |           |
| dHg2fromHgBrPlusBr2 | of Hg2 from         |           |           |
|                     | reaction with HgBr  |           |           |
|                     | + Br2               |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdH               | Chemical production | kg/s      |           |
| g2fromHgBrPlusBrBrO | of Hg2 from         |           |           |
|                     | reaction with HgBr  |           |           |
|                     | + BrBrO             |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdH               | Chemical production | kg/s      |           |
| g2fromHgBrPlusBrClO | of Hg2 from         |           |           |
|                     | reaction with HgBr  |           |           |
|                     | + BrClO             |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdH               | Chemical production | kg/s      |           |
| g2fromHgBrPlusBrHO2 | of Hg2 from         |           |           |
|                     | reaction with HgBr  |           |           |
|                     | + BrHO2             |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdH               | Chemical production | kg/s      |           |
| g2fromHgBrPlusBrNO2 | of Hg2 from         |           |           |
|                     | reaction with HgBr  |           |           |
|                     | + BrNO2             |           |           |
+---------------------+---------------------+-----------+-----------+
| Prod                | Chemical production | kg/s      |           |
| Hg2fromHgBrPlusBrOH | of Hg2 from         |           |           |
|                     | reaction with HgBr  |           |           |
|                     | + BrOH              |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdHg2fromO3       | Chemical production | kg/s      |           |
|                     | of Hg2 from         |           |           |
|                     | reaction with O3    |           |           |
+---------------------+---------------------+-----------+-----------+
| ProdHg2fromOH       | Chemical production | kg/s      |           |
|                     | of Hg2 from         |           |           |
|                     | reaction with OH    |           |           |
+---------------------+---------------------+-----------+-----------+
| ParticulateBoundHg  | Particulate bound   | pptv      |           |
|                     | mercury (PBM)       |           |           |
+---------------------+---------------------+-----------+-----------+
| ReactiveGaseousHg   | Reactive gaseous    | pptv      |           |
|                     | mercury             |           |           |
+---------------------+---------------------+-----------+-----------+

.. _the_mercuryemis_collection:

The MercuryEmis Collection
--------------------------

\ **NOTE: This collection has not yet been validated and should be
considered experimental.**\

The **MercuryEmis** collection contains emission diagnostics for the `Hg
specialty simulation <Mercury>`__.

Here is a sample definition section for the MercuryEmis collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  MercuryEmis.template:       '%y4%m2%d2_%h2%n2z.nc4',
  MercuryEmis.format:         'CFIO',
  MercuryEmis.frequency:      00000000 040000
  MercuryEmis.duration:       00000000 040000
  MercuryEmis.mode:           'time-averaged'
  MercuryEmis.fields:         'EmisHg0anthro                 ', 'GIGCchem',
                              'EmisHg0biomass                ', 'GIGCchem',
                              'EmisHg0geogenic               ', 'GIGCchem',
                              'EmisHg0land                   ', 'GIGCchem',
                              'EmisHg0ocean                  ', 'GIGCchem',
                              'EmisHg0soil                   ', 'GIGCchem',
                              'EmisHg0snow                   ', 'GIGCchem',
                              'EmisHg0vegetation             ', 'GIGCchem',
                              'EmisHg2HgPanthro              ', 'GIGCchem',
                              'EmisHg2snowToOcean            ', 'GIGCchem',
                              'EmisHg2rivers                 ', 'GIGCchem',
                              'FluxHg2HgPfromAirToSnow       ', 'GIGCchem',
::

The table below describes the diagnostic quantities belonging to the
MercuryEmis collection:

+-----------------------+-----------------------+-------+-----------+
| Diagnostic name       | Description           | Units | Wildcards |
+=======================+=======================+=======+===========+
| EmisHg0anthro         | Emission of Hg0 from  | kg/s  |           |
|                       | anthropogenic sources |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0biomass        | Emissions of Hg0 from | kg/s  |           |
|                       | biomass burning       |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0geogenic       | Emissions of Hg0 from | kg/s  |           |
|                       | geogenic sources      |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0land           | Re-emission of Hg0    | kg/s  |           |
|                       | from land             |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0ocean          | Emissions of Hg0 from | kg/s  |           |
|                       | oceans                |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0snow           | Emission of Hg0 from  | kg/s  |           |
|                       | snowpack              |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0soil           | Emissions of Hg0 from | kg/s  |           |
|                       | soils                 |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg0vegetation     | Emission of Hg0 from  | kg/s  |           |
|                       | vegetation            |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg2HgPanthro      | Emission of Hg2 + HgP | kg/s  |           |
|                       | from anthropogenic    |       |           |
|                       | sources               |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg2snowToOcean    | Hg2 delivered to      | kg/s  |           |
|                       | oceans from melting   |       |           |
|                       | snow                  |       |           |
+-----------------------+-----------------------+-------+-----------+
| EmisHg2rivers         | Emissions of Hg2 from | kg/s  |           |
|                       | delivered to oceans   |       |           |
|                       | from melting snow     |       |           |
+-----------------------+-----------------------+-------+-----------+
| Fl                    | Deposition flux of    | kg/s  |           |
| uxHg2HgPfromAirToSnow | Hg2 + HgP from the    |       |           |
|                       | atmosphere onto snow  |       |           |
+-----------------------+-----------------------+-------+-----------+

.. _the_mercuryocean_collection:

The MercuryOcean collection
---------------------------

\ **NOTE: This collection has not yet been validated and should be
considered experimental.**\

The **MercuryOcean** collection contains diagnostics from the mercury
ocean model, used in the `Hg specialty simulaton <Mercury>`__.

Here is a sample definition section for the MercuryOcean collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  MercuryOcean.template:      '%y4%m2%d2_%h2%n2z.nc4',
  MercuryOcean.format:        'CFIO',
  MercuryOcean.frequency:     00000000 040000
  MercuryOcean.duration:      00000000 040000
  MercuryOcean.mode:          'time-averaged'
  MercuryOcean.fields:        'FluxHg0fromAirToOcean         ', 'GIGCchem',
                              'FluxHg0fromOceanToAir         ', 'GIGCchem',
                              'FluxHg2HgPfromAirToOcean      ', 'GIGCchem',
                              'FluxHg2toDeepOcean            ', 'GIGCchem',
                              'FluxOCtoDeepOcean             ', 'GIGCchem',
                              'MassHg0inOcean                ', 'GIGCchem',
                              'MassHg2inOcean                ', 'GIGCchem',
                              'MassHgPinOcean                ', 'GIGCchem',
                              'MassHgTotalInOcean            ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
MercuryOcean collection

+--------------------------+-----------------------------+-------+
| Diagnostic name          | Description                 | Units |
+==========================+=============================+=======+
| FluxHg0fromAirToOcean    | Deposition flux of Hg0 from | kg/s  |
|                          | the atmosphere to the ocean |       |
+--------------------------+-----------------------------+-------+
| FluxHg0fromOceanToAir    | Volatilizatoin flux of Hg0  | kg/s  |
|                          | from the ocean to the       |       |
|                          | atmosphere                  |       |
+--------------------------+-----------------------------+-------+
| FluxHg2HgPfromAirToOcean | Deposition flux of Hg2 +    | kg/s  |
|                          | HgP from the atmosphere to  |       |
|                          | the ocean                   |       |
+--------------------------+-----------------------------+-------+
| FluxHg2toDeepOcean       | Flux of Hg2 sunk to the     | kg/s  |
|                          | deep ocean                  |       |
+--------------------------+-----------------------------+-------+
| MassHg0inOcean           | Total mass of oceanic Hg0   | kg    |
+--------------------------+-----------------------------+-------+
| MassHg2inOcean           | Total mass of oceanic Hg2   | kg    |
+--------------------------+-----------------------------+-------+
| MassHgPinOcean           | Total mass of oceanic Hg2   | kg    |
+--------------------------+-----------------------------+-------+
| MassHgTotalInOcean       | Total mass of all organic   | kg    |
|                          | mercury                     |       |
+--------------------------+-----------------------------+-------+

.. _the_leveledgediags_collection:

The LevelEdgeDiags collection
-----------------------------

The **LevelEdgeDiags** collection contains diagnostics for quantities
(mostly met fields) that are defined on the vertical edges of each grid
box. According to the COARDS convention, all of the data variables in a
netCDF file must be defined with the same vertical dimension.

Here is a sample definition section for the LevelEdgeDiags collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  LevelEdgeDiags.template:    '%y4%m2%d2_%h2%n2z.nc4',
  LevelEdgeDiags.format:      'CFIO',
  LevelEdgeDiags.frequency:   00000100 000000
  LevelEdgeDiags.duration:    00000100 000000
  LevelEdgeDiags.mode:        'time-averaged'
  LevelEdgeDiags.fields:      'Met_CMFMC                     ', 'GIGCchem',
                              'Met_PEDGE                     ', 'GIGCchem',
                              'Met_PEDGEDRY                  ', 'GIGCchem',
                              'Met_PFICU                     ', 'GIGCchem',
                              'Met_PFILSAN                   ', 'GIGCchem',
                              'Met_PFLCU                     ', 'GIGCchem',
                              'Met_PFLLSAN                   ', 'GIGCchem',
::

This table describes the diagnostic quantities belonging to the
JValuesLocalNoon collection:

+-----------------+----------------------------------------+---------+
| Diagnostic name | Description                            | Units   |
+=================+========================================+=========+
| Met_CMFMC       | Upward moist convective mass flux      | kg/m2/s |
+-----------------+----------------------------------------+---------+
| Met_PEDGE       | Surface pressure at level edges (based | hP      |
|                 | on moist air)                          |         |
+-----------------+----------------------------------------+---------+
| Met_PEDGEDRY    | Surface pressure at level edges (based | hPa     |
|                 | on dry air)                            |         |
+-----------------+----------------------------------------+---------+
| Met_PFICU       | 3d flux of ice convective              | kg/m2/s |
|                 | precipitation                          |         |
+-----------------+----------------------------------------+---------+
| Met_PFILSAN     | 3d flux of ice non-convective          | kg/m2/s |
|                 | precipitation                          |         |
+-----------------+----------------------------------------+---------+
| Met_PFLCU       | 3d flux of liquid convective           | kg/m2/s |
|                 | precipitation                          |         |
+-----------------+----------------------------------------+---------+
| Met_PFLLSAN     | 3d flux of liquid non-convective       | kg/m2/s |
|                 | precipitation                          |         |
+-----------------+----------------------------------------+---------+

.. _the_statemet_collection:

The StateMet collection
-----------------------

The **StateMet** collection contains met fields and other derived
quantities that are carried in the State_Met object.

Here is a sample definition section for the **StateMet** collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Other fields of the State_Met object can be added to this
   collection by prefixing the field name with Met_.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  StateMet.template:          '%y4%m2%d2_%h2%n2z.nc4',
  StateMet.format:            'CFIO',
  StateMet.frequency:         00000100 000000
  StateMet.duration:          00000100 000000
  StateMet.mode:              'time-averaged'
  StateMet.fields:            'Met_AD                        ', 'GIGCchem',
                              'Met_AIRDEN                    ', 'GIGCchem',
                              'Met_AIRVOL                    ', 'GIGCchem',
                              'Met_ALBD                      ', 'GIGCchem',
                              'Met_AREAM2                    ', 'GIGCchem',
                              'Met_AVGW                      ', 'GIGCchem',
                              'Met_BXHEIGHT                  ', 'GIGCchem',
                              'Met_ChemGridLev               ', 'GIGCchem',
                              'Met_CLDF                      ', 'GIGCchem',
                              'Met_CLDFRC                    ', 'GIGCchem',
                              'Met_CLDTOPS                   ', 'GIGCchem',
                              'Met_DELP                      ', 'GIGCchem',
                              'Met_DQRCU                     ', 'GIGCchem',
                              'Met_DQRLSAN                   ', 'GIGCchem',
                              'Met_DTRAIN                    ', 'GIGCchem',
                              'Met_EFLUX                     ', 'GIGCchem',
                              'Met_FRCLND                    ', 'GIGCchem',
                              'Met_FRLAKE                    ', 'GIGCchem',
                              'Met_FRLAND                    ', 'GIGCchem',
                              'Met_FRLANDIC                  ', 'GIGCchem',
                              'Met_FROCEAN                   ', 'GIGCchem',
                              'Met_FRSEAICE                  ', 'GIGCchem',
                              'Met_FRSNO                     ', 'GIGCchem',
                              'Met_GWETROOT                  ', 'GIGCchem',
                              'Met_GWETTOP                   ', 'GIGCchem',
                              'Met_HFLUX                     ', 'GIGCchem',
                              'Met_LAI                       ', 'GIGCchem',
                              'Met_LWI                       ', 'GIGCchem',
                              'Met_PARDR                     ', 'GIGCchem',
                              'Met_PARDF                     ', 'GIGCchem',
                              'Met_PBLTOPL                   ', 'GIGCchem',
                              'Met_PBLH                      ', 'GIGCchem',
                              'Met_PHIS                      ', 'GIGCchem',
                              'Met_PMID                      ', 'GIGCchem',
                              'Met_PMIDDRY                   ', 'GIGCchem',
                              'Met_PRECANV                   ', 'GIGCchem',
                              'Met_PRECCON                   ', 'GIGCchem',
                              'Met_PRECLSC                   ', 'GIGCchem',
                              'Met_PRECTOT                   ', 'GIGCchem',
                              'Met_PS1DRY                    ', 'GIGCchem',
                              'Met_PS1WET                    ', 'GIGCchem',
                              'Met_PS2DRY                    ', 'GIGCchem',
                              'Met_PS2WET                    ', 'GIGCchem',
                              'Met_PSC2WET                   ', 'GIGCchem',
                              'Met_PSC2DRY                   ', 'GIGCchem',
                              'Met_QI                        ', 'GIGCchem',
                              'Met_QL                        ', 'GIGCchem',
                              'Met_OMEGA                     ', 'GIGCchem',
                              'Met_OPTD                      ', 'GIGCchem',
                              'Met_REEVAPCN                  ', 'GIGCchem',
                              'Met_REEVAPLS                  ', 'GIGCchem',
                              'Met_SLP                       ', 'GIGCchem',
                              'Met_SNODP                     ', 'GIGCchem',
                              'Met_SNOMAS                    ', 'GIGCchem',
                              'Met_SPHU                      ', 'GIGCchem',
                              'Met_SPHU1                     ', 'GIGCchem',
                              'Met_SPHU2                     ', 'GIGCchem',
                              'Met_SUNCOS                    ', 'GIGCchem',
                              'Met_SUNCOSmid                 ', 'GIGCchem',
                              'Met_SWGDN                     ', 'GIGCchem',
                              'Met_T                         ', 'GIGCchem',
                              'Met_TAUCLI                    ', 'GIGCchem',
                              'Met_TAUCLW                    ', 'GIGCchem',
                              'Met_THETA                     ', 'GIGCchem',
                              'Met_TMPU1                     ', 'GIGCchem',
                              'Met_TMPU2                     ', 'GIGCchem',
                              'Met_TO3                       ', 'GIGCchem',
                              'Met_TropHt                    ', 'GIGCchem',
                              'Met_TropLev                   ', 'GIGCchem',
                              'Met_TropP                     ', 'GIGCchem',
                              'Met_TS                        ', 'GIGCchem',
                              'Met_TSKIN                     ', 'GIGCchem',
                              'Met_TV                        ', 'GIGCchem',
                              'Met_U                         ', 'GIGCchem',
                              'Met_U10M                      ', 'GIGCchem',
                              'Met_USTAR                     ', 'GIGCchem',
                              'Met_UVALBEDO                  ', 'GIGCchem',
                              'Met_V                         ', 'GIGCchem',
                              'Met_V10M                      ', 'GIGCchem',
                              'Met_Z0                        ', 'GIGCchem',
::

+------------------+---------------------------+---------------------+
| Diagnostic name  | Description               | Units               |
+==================+===========================+=====================+
| Met_AD           | Dry air mass              | kg                  |
+------------------+---------------------------+---------------------+
| Met_AIRDEN       | Dry air density           | kg/m3               |
+------------------+---------------------------+---------------------+
| Met_AIRVOL       | Grid box volume, dry air  | m3                  |
+------------------+---------------------------+---------------------+
| Met_ALBD         | Surface albedo            | 1                   |
+------------------+---------------------------+---------------------+
| Met_AREAM2       | Grid box area             | m2                  |
+------------------+---------------------------+---------------------+
| Met_AVGW         | Water vapor volume mixing | vol H2O/vol dry air |
|                  | ratio                     |                     |
+------------------+---------------------------+---------------------+
| Met_BXHEIGHT     | Grid box height           | m                   |
+------------------+---------------------------+---------------------+
| Met_ChemGridLev  | Chemistry grid level      | 1                   |
+------------------+---------------------------+---------------------+
| Met_CLDF         | 3-D cloud fraction        |                     |
+------------------+---------------------------+---------------------+
| Met_CLDFRC       | Column cloud fraction     | 1                   |
+------------------+---------------------------+---------------------+
| Met_CLDTOPS      | Maximum cloud top height  | 1                   |
+------------------+---------------------------+---------------------+
| Met_DELP         | Delta-pressure between    | hPa                 |
|                  | top and bottom edges of   |                     |
|                  | grid box (wet air)        |                     |
+------------------+---------------------------+---------------------+
| Met_DQRCU        | Convective precipitation  | kg/kg/s             |
|                  | production rate (dry air) |                     |
+------------------+---------------------------+---------------------+
| Met_DTRAIN       | Detrainment flux          | kg/m2/s             |
+------------------+---------------------------+---------------------+
| Met_EFLUX        | Latent heat flux          | W/m2                |
+------------------+---------------------------+---------------------+
| Met_FRCLND       | Olson land fraction       | 1                   |
+------------------+---------------------------+---------------------+
| Met_FRLAKE       | Fraction of grid box      | 1                   |
|                  | covered by lakes          |                     |
+------------------+---------------------------+---------------------+
| Met_FRLAND       | Fraction of grid box      | 1                   |
|                  | covered by land           |                     |
+------------------+---------------------------+---------------------+
| Met_FRLANDIC     | Fraction of grid box      | 1                   |
|                  | covered by land ice       |                     |
+------------------+---------------------------+---------------------+
| Met_FROCEAN      | Fraction of grid box      | 1                   |
|                  | covered by ocean          |                     |
+------------------+---------------------------+---------------------+
| Met_FRSEAICE     | Fraction of grid box      | 1                   |
|                  | covered by sea ice        |                     |
+------------------+---------------------------+---------------------+
| Met_FRSNO        | Fraction of grid box      | 1                   |
|                  | covered by snow           |                     |
+------------------+---------------------------+---------------------+
| Met_GWETROOT     | Root soil moisture        | 1                   |
+------------------+---------------------------+---------------------+
| Met_GWETTOP      | Topsoil moisture          | 1                   |
+------------------+---------------------------+---------------------+
| Met_HFLUX        | Sensible heat flux        | W/m2                |
+------------------+---------------------------+---------------------+
| Met_LAI          | Leaf area index from met  | m2/m2               |
|                  | field archive             |                     |
+------------------+---------------------------+---------------------+
| Met_LWI          | Land-water-ice indices    | 1                   |
+------------------+---------------------------+---------------------+
| Met_PARDF        | Diffuse                   | W/m2                |
|                  | photosynthetically active |                     |
|                  | radiation                 |                     |
+------------------+---------------------------+---------------------+
| Met_PARDR        | Diffuse                   | W/m2                |
|                  | photosynthetically active |                     |
|                  | radiation                 |                     |
+------------------+---------------------------+---------------------+
| Met_PBLTOPL      | PBL top layer             | 1                   |
+------------------+---------------------------+---------------------+
| Met_PBLH         | PBL height                | m                   |
+------------------+---------------------------+---------------------+
| Met_PHIS         | Surface geopotential      | m                   |
|                  | height                    |                     |
+------------------+---------------------------+---------------------+
| Met_PMID         | Pressure at midpoint of   | hPa                 |
|                  | model layers, defined as  |                     |
|                  | arithmetic average of     |                     |
|                  | edge pressures (wet air)  |                     |
+------------------+---------------------------+---------------------+
| Met_PMIDDRY      | Pressure at midpoint of   | hPa                 |
|                  | model layers, defined as  |                     |
|                  | arithmetic average of     |                     |
|                  | edge pressures (dry air)  |                     |
+------------------+---------------------------+---------------------+
| Met_PRECANV      | Anvil precipitation (at   | mm/day              |
|                  | surface)                  |                     |
+------------------+---------------------------+---------------------+
| Met_PRECCON      | Convective precipitation  | mm/day              |
|                  | (at surface)              |                     |
+------------------+---------------------------+---------------------+
| Met_PRECLSC      | Large-scale precipitation | mm/day              |
|                  | (at surface)              |                     |
+------------------+---------------------------+---------------------+
| Met_PRECTOT      | Total precipitation (at   | mm/day              |
|                  | surface)                  |                     |
+------------------+---------------------------+---------------------+
| Met_PS1DRY       | Instantaneous surface     | hPa                 |
|                  | pressure at start of 3-hr |                     |
|                  | met field interval (dry   |                     |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_PS2DRY       | Instantaneous surface     | hPa                 |
|                  | pressure at end of 3-hr   |                     |
|                  | met field interval (dry   |                     |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_PSC2DRY      | Surface pressure          | hPa                 |
|                  | interpolated to current   |                     |
|                  | time (dry air)            |                     |
+------------------+---------------------------+---------------------+
| Met_PS1WET       | Instantaneous surface     | hPa                 |
|                  | pressure at start of 3-hr |                     |
|                  | met field interval (wet   |                     |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_PS2WET       | Instantaneous surface     | hPa \\              |
|                  | pressure at end of 3-hr   |                     |
|                  | met field interval (wet   |                     |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_PSC2WET      | Surface pressure          | hPa                 |
|                  | interpolated to current   |                     |
|                  | time (wet air)            |                     |
+------------------+---------------------------+---------------------+
| Met_QI           | Ice mixing ratio (dry     | kg/kg dry air       |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_QL           | Liquid water mixing ratio | kg/kg dry air       |
|                  | (dry air)                 |                     |
+------------------+---------------------------+---------------------+
| Met_OMEGA        | Updraft velocity          | Pa/s                |
+------------------+---------------------------+---------------------+
| Met_OPTD         | Visible optical depth     | 1                   |
+------------------+---------------------------+---------------------+
| Met_REEVAPCN     | Evaporation of convective | kg/kg/s             |
|                  | precipitation (dry air)   |                     |
+------------------+---------------------------+---------------------+
| Met_REEVAPLS     | Evaporation of            | kg/kg/s             |
|                  | large-scale + anvil       |                     |
|                  | precipitation (dry air)   |                     |
+------------------+---------------------------+---------------------+
| Met_SLP          | Sea level pressure        | hPa                 |
+------------------+---------------------------+---------------------+
| Met_SNODP        | Snow depth                | m                   |
+------------------+---------------------------+---------------------+
| Met_SNOMAS       | Snow mass                 | kg/m2               |
+------------------+---------------------------+---------------------+
| Met_SPHU1        | Instantaneous specific    | kg/kg               |
|                  | humidity at start of 3 hr |                     |
|                  | met field interval (wet   |                     |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_SPHU2        | Instantaneous specific    | kg/kg               |
|                  | humidity at end of 3-hr   |                     |
|                  | met field interval (wet   |                     |
|                  | air)                      |                     |
+------------------+---------------------------+---------------------+
| Met_SPHU         | Specific humidity         | g H2O/kg air        |
|                  | interpolated to current   |                     |
|                  | time (wet air)            |                     |
+------------------+---------------------------+---------------------+
| Met_SUNCOS       | Cosine of solar zenith    | 1                   |
|                  | angle at current time     |                     |
+------------------+---------------------------+---------------------+
| Met_SUNCOSMID    | Cosine of solar zenith    | 1                   |
|                  | angle at midpoint of      |                     |
|                  | chemistry timestep        |                     |
+------------------+---------------------------+---------------------+
| Met_SWGDN        | Incident shortwave        | W/m2                |
|                  | radiation at ground       |                     |
+------------------+---------------------------+---------------------+
| Met_TAUCLI       | Visible optical depth of  | 1                   |
|                  | ice clouds                |                     |
+------------------+---------------------------+---------------------+
| Met_TAUCLW       | Visible optical depth of  | 1                   |
|                  | water clouds              |                     |
+------------------+---------------------------+---------------------+
| Met_THETA        | Potential temperature     | K                   |
+------------------+---------------------------+---------------------+
| Met_TMPU1        | Instantaneous temperature | K                   |
|                  | at start of 3-hr met      |                     |
|                  | field interval            |                     |
+------------------+---------------------------+---------------------+
| Met_TMPU2        | Instantaneous temperature | K                   |
|                  | at end of 3-hr met field  |                     |
|                  | interval                  |                     |
+------------------+---------------------------+---------------------+
| Met_T            | Temperature interpolated  | K                   |
|                  | to current time           |                     |
+------------------+---------------------------+---------------------+
| Met_TO3          | Total overhead ozone      | Dobsons             |
|                  | column                    |                     |
+------------------+---------------------------+---------------------+
| Met_TropHt       | Tropopause height         | u                   |
+------------------+---------------------------+---------------------+
| Met_TropLev      | Tropopause level          | 1                   |
+------------------+---------------------------+---------------------+
| Met_TROPP        | Tropopause pressure       | hPa                 |
+------------------+---------------------------+---------------------+
| Met_TS           | Surface temperature       | K                   |
+------------------+---------------------------+---------------------+
| Met_TSKIN        | Surface skin temperature  | K                   |
+------------------+---------------------------+---------------------+
| Met_U            | East-west ccomponent of   | m/s                 |
|                  | wind                      |                     |
+------------------+---------------------------+---------------------+
| Met_U10M         | East-west component of    | m/s                 |
|                  | wind at 10 m height above |                     |
|                  | surface                   |                     |
+------------------+---------------------------+---------------------+
| Met_USTAR        | Friction velocity         | m/s                 |
+------------------+---------------------------+---------------------+
| Met_UVALBEDO     | Ultraviolet surface       | 1                   |
|                  | albedo                    |                     |
+------------------+---------------------------+---------------------+
| Met_V            | North-south ccomponent of | m/s                 |
|                  | wind                      |                     |
+------------------+---------------------------+---------------------+
| Met_V10M         | North-south component of  | m/s                 |
|                  | wind at 10 m height above |                     |
|                  | surface                   |                     |
+------------------+---------------------------+---------------------+
| Met_Z0           | Surface roughness height  | m                   |
+------------------+---------------------------+---------------------+
| FracOfTimeInTrop | Fraction of time spent in | 1                   |
|                  | the troposphere           |                     |
+------------------+---------------------------+---------------------+



.. _the_boundaryconditions_collection:



.. _the_restart_collection:

The Restart collection
----------------------

The **Restart** diagnostic collection contains fields for saving out to
the GEOS-Chem restart file. This type of diagnostic output is used in
all GEOS-Chem simulations; therefore, we have listed Restart first in
the HISTORY.rc files that ship with each `GEOS-Chem run
directory <Creating_GEOS-Chem_run_directories>`__.

Here is a sample definition section for the Restart collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

*NOTE: The restart file will be created in the GEOS_Chem run directory,
not in the OutputDir sub-folder.*

  Restart.filename:           './GEOSChem.Restart.%y4%m2%d2_%h2%n2z.nc4',
  Restart.format:             'CFIO',
  Restart.frequency:          'End',
  Restart.duration:           'End',
  Restart.mode:               'instantaneous'
  Restart.fields:             'SpeciesRst_?ALL?               ', 'GIGCchem',
                              'Chem_H2O2AfterChem             ', 'GIGCchem',
                              'Chem_SO2AfterChem              ', 'GIGCchem',
                              'Chem_DryDepNitrogen            ', 'GIGCchem',
                              'Chem_WetDepNitrogen            ', 'GIGCchem',
                              'Chem_KPPHvalue                 ', 'GIGCchem',
                              'Met_DELPDRY                    ', 'GIGCchem',
                              'Met_PS1WET                     ', 'GIGCchem',
                              'Met_PS1DRY                     ', 'GIGCchem',
                              'Met_SPHU1                      ', 'GIGCchem',
                              'Met_TMPU1                      ', 'GIGCchem',
::

This table describes the diagnostic quantities belonging to the
SpeciesConc collection:

+--------------------+-----------------------------+-----------------+
| Diagnostic name    | Description                 | Units           |
+====================+=============================+=================+
| SpeciesRst_?ALL?   | Instantaneous chemical      | mol/mol dry air |
|                    | species concentrations for  |                 |
|                    | use in starting subsequent  |                 |
|                    | GEOS-Chem simulations       |                 |
+--------------------+-----------------------------+-----------------+
| Chm_H2O2AfterChem  | Concentration of H2O2 after | v/v             |
|                    | sulfate chemistry           |                 |
+--------------------+-----------------------------+-----------------+
| Chm_SO2AfterChem   | Concentration of SO2 after  | v/v             |
|                    | sulfate chemistry           |                 |
+--------------------+-----------------------------+-----------------+
| Chm_DryDepNitrogen | Dry deposited nitrogen      | molec/cm2/s     |
+--------------------+-----------------------------+-----------------+
| Chm_WetDepNitrogen | Wet deposited nitrogen      | molec/cm2/s     |
+--------------------+-----------------------------+-----------------+
| Chm_KPPHvalue      | H-value for Rosenbrock      | unitless        |
|                    | solver                      |                 |
+--------------------+-----------------------------+-----------------+
| Chm_StatePSC       | Polar stratospheric cloud   | count           |
|                    | type                        |                 |
+--------------------+-----------------------------+-----------------+
| Met_DELPDRY        | Delta-pressure across grid  | hPa             |
|                    | box (dry air)               |                 |
+--------------------+-----------------------------+-----------------+
| Met_PS1WET         | Wet surface pressure at dt  | hPa             |
|                    | start                       |                 |
+--------------------+-----------------------------+-----------------+
| Met_PS1DRY         | Dry surface pressure at dt  | hPa             |
|                    | start                       |                 |
+--------------------+-----------------------------+-----------------+
| Met_SPHU1          | Instantaneous specific      | g kg-1          |
|                    | humidity at time=T          |                 |
+--------------------+-----------------------------+-----------------+
| Met_TMPU1          | Instantaneous temperature   | K               |
|                    | at time=T                   |                 |
+--------------------+-----------------------------+-----------------+

.. _the_speciesconc_collection:

The SpeciesConc collection
--------------------------

The **SpeciesConc** diagnostic collection contains advected species
concentrations. This type of diagnostic output is used in all GEOS-Chem
simulations.

Here is a sample definition section for the SpeciesConc collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  SpeciesConc.template:       '%y4%m2%d2_%h2%n2z.nc4',
  SpeciesConc.format:         'CFIO',
  SpeciesConc.frequency:      00000100 000000
  SpeciesConc.duration:       00000100 000000
  SpeciesConc.mode:           'time-averaged'
  SpeciesConc.fields:         'SpeciesConc_?ADV?             ', 'GIGCchem',
::

This table describes the diagnostic quantities belonging to the
SpeciesConc collection:

+---------+---------+---------+---------+---------+-------+---------+
| Dia     | Desc    | Units   | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |         | ldcards | lations |       | equiv.  |
| name    |         |         |         |         |       |  <List_ |
|         |         |         |         |         |       | of_diag |
|         |         |         |         |         |       | nostics |
|         |         |         |         |         |       | _archiv |
|         |         |         |         |         |       | ed_to_b |
|         |         |         |         |         |       | pch_for |
|         |         |         |         |         |       | mat>`__ |
+=========+=========+=========+=========+=========+=======+=========+
| Spec    | C       | mol/mol |         |         |       |         |
| iesConc | hemical | dry air |         |         |       |         |
| VV_<spc | species |         |         |         |       |         |
| name|wi | concent |         |         |         |       |         |
| ldcard> | rations |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+
| Speci   | C       | mo      |         |         |       |         |
| esConcM | hemical | lec/cm3 |         |         |       |         |
| ND_<spc | species |         |         |         |       |         |
| name|wi | concent |         |         |         |       |         |
| ldcard> | rations |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+

.. _the_radionuclide_collection:

The RadioNuclide Collection
---------------------------

The **RadioNuclide** collection contains diagnostic outputs from the
`TransportTracers simulation <TransportTracers_simulation>`__.

*NOTE: Emissions of Rn and Be7 are archived to diagnostic output by
HEMCO, and are thus not contained in this collection. For more
information, please see the*\ `HEMCO diagnostics for the Rn-Pb-Be
simulation <#HEMCO_diagnostics_for_the_Rn-Pb-Be_simulation>`__\ *below.*

Here is a sample definition section for the RadioNuclide collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  RadioNuclide.template:      '%y4%m2%d2_%h2%n2z.nc4',
  RadioNuclide.format:        'CFIO',
  RadioNuclide.frequency:     00000100 000000
  RadioNuclide.duration:      00000100 000000
  RadioNuclide.mode:          'time-averaged'
  RadioNuclide.fields:        'PbFromRnDecay                 ', 'GIGCchem',
                              'RadDecay_Rn                   ', 'GIGCchem',
                              'RadDecay_Pb                   ', 'GIGCchem',
                              'RadDecay_Be7                  ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
RadioNuclide collection.

=============== ================================================ =====
Diagnostic name Description                                      Units
=============== ================================================ =====
PbFromRnDecay   Production of 210Pb from 222Rn radioactive decay kg/s
RadDecay_Rn     Loss of 222Rn due to radiactive decay
RadDecay_Pb     Loss of 210Pb due to radiaoactive decay          kg/s
RadDecay_Be7    Loss of 7Be due to radioactive decay             kg/s
=============== ================================================ =====

The **CO2** collection contains diagnostic outputs from the `CO2
simulation <CO2_simulation>`__.

''NOTE: Many other diagnostics for the CO2 simulation are archived via
HEMCO diagnostics.`\`

Here is a sample definition section for the CO2 collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

  CO2.template:      '%y4%m2%d2_%h2%n2z.nc4',
  CO2.format:        'CFIO',
  CO2.frequency:     00000100 000000
  CO2.duration:      00000100 000000
  CO2.mode:          'time-averaged'
  CO2.fields:        'ProdCO2fromCO                        ', 'GIGCchem',
::

The table below describes diagnostic quantities belonging to the
RadioNuclide collection.

+---------+---------+---------+---------+---------+-------+---------+
| Dia     | Desc    | Units   | Wi      | Simu    | Notes | `Bpch   |
| gnostic | ription |         | ldcards | lations |       | equiv.  |
| name    |         |         |         |         |       |  <List_ |
|         |         |         |         |         |       | of_diag |
|         |         |         |         |         |       | nostics |
|         |         |         |         |         |       | _archiv |
|         |         |         |         |         |       | ed_to_b |
|         |         |         |         |         |       | pch_for |
|         |         |         |         |         |       | mat>`__ |
+=========+=========+=========+=========+=========+=======+=========+
| ProdCO  | C       | kg/m2/s |         |         |       |         |
| 2fromCO | hemical |         |         |         |       |         |
|         | pro     |         |         |         |       |         |
|         | duction |         |         |         |       |         |
|         | of CO2  |         |         |         |       |         |
|         | from CO |         |         |         |       |         |
+---------+---------+---------+---------+---------+-------+---------+

.. _the_satdiagn_collection_replaces_bpch_nd51:

The SatDiagn collection (replaces bpch ND51)
--------------------------------------------

*NOTE: If you are not setting up for a nested-grid simulation, then you
can disable this collection.*

The **BoundaryConditions** diagnostic collection contains advected
species concentrations (archived from a global simulation) that will be
used by nested-grid simulations as transport boundary conditions. Like
SpeciesConc, it archives data with units of mol/mol dry air.

Here is a sample definition section for the SpeciesConc collection.

-  If this collection is not already present in the HISTORY.rc file
   in `the GEOS-Chem run directory for your selected
   simulation <Creating_GEOS-Chem_run_directories>`__, you can copy and
   paste this into your HISTORY.rc file and edit accordingly.
-  To prevent an individual field from being included in the diagnostic
   output, place a comment character # in front of the field name.
-  Please see our `Legend for History
   diagnostics <Legend_for_History_diagnostics>`__ page for more
   information about each of the collection tags.

#==============================================================================
# %%%%% THE SatDiagn COLLECTION %%%%%
#
# GEOS-Chem data during satellite overpass
#
# Available for all simulations
#==============================================================================
  SatDiagn.template:          '%y4%m2%d2_%h2%n2z.nc4',
  SatDiagn.format:            'CFIO',
  SatDiagn.frequency:         00000001 000000
  SatDiagn.duration:          00000100 000000
  SatDiagn.hrrange:           11.98 15.02
  SatDiagn.mode:              'time-averaged'
  SatDiagn.fields:            'SatDiagnConc_          ',
                              'SatDiagnOH                     ',
                              'SatDiagnRH                     ',
                              'SatDiagnAirDen                 ',
                              'SatDiagnBoxHeight              ',
                              'SatDiagnPEdge                  ',
                              'SatDiagnTROPP                  ',
                              'SatDiagnPBLHeight              ',
                              'SatDiagnPBLTop                 ',
                              'SatDiagnTAir                   ',
                              'SatDiagnGWETROOT               ',
                              'SatDiagnGWETTOP                ',
                              'SatDiagnPARDR                  ',
                              'SatDiagnPARDF                  ',
                              'SatDiagnPRECTOT                ',
                              'SatDiagnSLP                    ',
                              'SatDiagnSPHU                   ',
                              'SatDiagnTS                     ',
                              'SatDiagnPBLTOPL                ',
                              'SatDiagnMODISLAI               ',
                              'SatDiagnWetLossLS_     ',
                              'SatDiagnWetLossConv_   ',
                              'SatDiagnJval_          ',
                              'SatDiagnJvalO3O1D              ',
                              'SatDiagnJvalO3O3P              ',
                              'SatDiagnDryDep_        ',
                              'SatDiagnDryDepVel_     ',
                              'SatDiagnOHreactivity           ',
                              'SatDiagnColEmis_       ',
                              'SatDiagnSurfFlux_      ',
                              'SatDiagnProd_?PRD?             ',
                              'SatDiagnLoss_?LOS?             ',
                              'SatDiagnRxnRate_EQnnn          ',
::


== Adding New Diagnostics ==

To add your own diagnostics we recommend that you find a similar
existing diagnostic and use its implementation as a template. Most of
the work is done in Headers/state_diag_mod.F90. Briefly, the
following updates to that file are essential for adding in your own
netcdf diagnostics:

#. Declare diagnostic array at top of module
#. Set diagnostic array pointer to null in Zero_State_Diag
   subroutine
#. Create a section in Init_State_Diag subroutine to allocate and
   register the array
#. Deallocate the diagnostic array in subroutine Cleanup_State_Diag
#. Add an if block for the diagnostic within subroutine
   Get_Metadata_State_Diag to define its metadata, making sure to
   list the diagnostic name with all capital letters

Good diagnostics to use as templates are SpeciesConc for
3-dimensional arrays that are for all species and DryDepVel for
2-dimensional arrays that are for a subset of species. If your
diagnostic is not per species then AODDust is a good diagnostic to
look at. Search the file Headers/state_diag_mod.F90 for any of these
strings to find all instances of code related to their implementation.

Note that information about the dimensions and species collection the
diagnostic will include must be specified when allocating the array in
Init_State_Diag and in Get_Metadata_State_Diag. In the latter
subroutine the Rank is the integer number of dimensions of the
diagnostic (not including species) and the TagID string, if any,
specifies the species collection to output the diagnostic per. TagIDs
are defined in subroutine Get_TagInfo. Each TagID string can also be
used as a wildcard within HISTORY.rc to simplify diagnostic name
specification (for GEOS-Chem classic only).

Once you have implemented your diagnostic in
Headers/state_diag_mod.F90, try adding it to HISTORY.rc and running.
You should get your diagnostic output in the netcdf output file as all
zeros. The next step is to populate the array with whatever value you
want to output. You should do this by passing the State_Diag array
to the location where you want to set the values. Then write code to
fill the array. A simple test of your understanding is to initially set
values to a constant other than zero and see if the output matches what
you set the arrays to.

For additional help implementing your own GEOS-Chem diagnostics please
contact the GEOS-Chem Support Team.
