.. |br| raw:: html

   <br />

.. _histguide:

###########################################
Archive output with the History diagnostics
###########################################

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
   <https://hemco.readthedocs.io/en/latest/hco-ref-guide/diagnostics.html>`_
   chapter of `The HEMCO User's Guide <https://hemco.readthedocs.io>`_
   more information. Note that HEMCO diagnostics are configured
   from configuration file :file:`HEMCO_Diagn.rc` for both GCHP and GEOS-Chem Classic
   but diagnostics are also specified in :file:`HISTORY.rc` for GCHP only.

.. _histguide-configfile:

The HISTORY.rc configuration file
---------------------------------

You can request GEOS-Chem diagnostics by editing the
:file:`HISTORY.rc` configuration file.  This file lists the groups of
diagnostic outputs (called **collections**), and the parameters for
each collection (frequency of archival, mode of archiving, fields per
collection, etc.). GEOS-Chem Classic will write netCDF files for each
collection at the output intervals that you specify in
:file:`HISTORY.rc`. If using GCHP this is done by the MAPL History component
also using configuration file :file:`HISTORY.rc`.

.. _histguide-configfile-sample:

Sample HISTORY.rc file
~~~~~~~~~~~~~~~~~~~~~~~

The following is a stripped-down :file:`HISTORY.rc` file example for
illustration purposes.  The :file:`HISTORY.rc` file that you will find
in your GEOS-Chem run directory will contain more collections than
what is shown below. This example is for GEOS-Chem Classic.

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
   SpeciesConc.duration :          00000001 000000
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
parameters shown in the :file:`HISTORY.rc` file above.

.. _histguide-configfile-sample-expid:

EXPID
^^^^^


Thi :literal:`EXPID` parameter controls the filename prefix, which is
set to :file:`./OutputDir/GEOSChem` by default. This means that
diagnostic files will be written to the :file:`./OutputDir` directory
of the GEOS-Chem run directory, and will start with the prefix
:file:`GEOSChem`.

   .. note::

      Restart files are placed in the :file:`./Restarts` subdirectory
      of the run directory instead of :file:`./OutputDir`, which only
      contains diagnostic files.

.. _histguide-configfile-sample-coll:

COLLECTIONS
^^^^^^^^^^^

The :literal:`COLLECTIONS:` tag specifies all of the diagnostic
**collections** that you wish to activate during a GEOS-Chem
simulation. Each collection represents a group of diagnostic
quantities that will be written to disk in netCDF file format. The
collection name will be automatically added to the netCDF file name
along with the date/or time.

Each GEOS-Chem run directory will ship with its own customized
:file:`HISTORY.rc` configuration file.  Only the diagnostic
collections pertaining to a particular GEOS-Chem simulation will be
included in the corresponding :file:`HISTORY.rc` file.

Each collection name must be bracketed by single quotes, and be
followed by a comma.

To disable an entire diagnostic collection, simply put a
:literal:`#` comment character in front of the collection name in
the :literal:`COLLECTIONS:` section.

GEOS-Chem will expect to find a collection definition section for each
of the activated collections listed under the
:literal:`COLLECTIONS:` section.  In other words, if you have
:ref:`histguide-configfile-sample-sc` listed under
:literal:`COLLECTIONS:`, but there is no further information provided
about the
:ref:`histguide-configfile-sample-sc` collection, then
GEOS-Chem will halt with an error message.

.. note::

   You are not limited to the collections that are pre-defined in
   the :file:`HISTORY.rc` configuration file.  You may create
   additional diagnostic collections to suit your research
   purposes.

.. _histguide-configfile-sample-sc:

SpeciesConc
^^^^^^^^^^^

Name of the first collection in this :file:`HISTORY.rc` file.

.. _histguide-configfile-sample-sc-tmpl:

SpeciesConc.template
^^^^^^^^^^^^^^^^^^^^
Determines the date and time format for the
:ref:`histguide-configfile-sample-sc` collection filename suffix, as
described below:

- :literal:`%y4%m2%d2_%h2%n2z.nc4` prints
  :literal:`YYYYMMDD_hhmmz.nc4` to the end of each netCDF filename.
- :literal:`YYYYMMDD` is the date in year/month/day format;
- :literal:`hhmm` is the time in :literal:`hour:minutes` format.
- :literal:`z` denotes "Zulu", which is an abbreviation for UTC time.
- :literal:`.nc4` denotes that the data file is in the netCDF-4 format.

.. note::

   For example, the complete file path for the
   :ref:`histguide-configfile-sample-sc` collection at 00:00 UTC on
   2020/01/01 will be
   :file:`./OutputDir/GEOSChem.SpeciesConc.20200101_0000z.nc4`,
   where:

   - :ref:`histguide-configfile-sample-expid` specifies the filename prefix
     (:file:`./OutputDir/GEOSChem`).

   - :ref:`histguide-configfile-sample-sc-tmpl` specifies the format
     of the filename suffix (:file:`.20200101_0000z.nc4`).

.. _histguide-configfile-sample-sc-freq:

SpeciesConc.frequency
^^^^^^^^^^^^^^^^^^^^^

Determines how often the diagnostic quantities belonging to
the  :ref:`histguide-configfile-sample-sc` collection will be saved to
a netCDF file. This can be specified as either :literal:`hhmmss` or
:literal:`YYYYMMDD hhmmss`.

In the above example, data belonging to the collection will be
written to  the file every 6 hours.  Because
:ref:`histguide-configfile-sample-sc` is an instantaneous collection,
that
will be used.

SpeciesConc.duration
^^^^^^^^^^^^^^^^^^^^

Determines how often a new :ref:`histguide-configfile-sample-sc`
netCDF file will be created.  Uses the same format as
:ref:`histguide-configfile-sample-sc-freq`.

SpeciesConc.mode
^^^^^^^^^^^^^^^^

Determines the averaging method for the  :ref:`SpeciesConc
<histguide-configfile-sample-sc>` collection.  Allowed values are:

- :literal:`instantaneous`: Archives instantaneous values at the
  interval specified by :ref:`histguide-configfile-sample-sc-freq`.
- :literal:`time-averaged`: Archives values averaged over the
  interval specified by :ref:`histguide-configfile-sample-sc-freq`.

SpeciesConc.fields
^^^^^^^^^^^^^^^^^^

Lists the diagnostic quantities to be archived in the
:ref:`histguide-configfile-sample-sc` collection.  Some diagnostic
quantities (e.g. concentrations, fluxes, masses) may also have an
extra dimension, which represents species, size bins, reaction
numbers, etc.

For example, to request the ozone species concentration (in mixing
ratio units) you may use the field name
:literal:`SpeciesConcVV_O3`.  The species name is separated from
the quantity name by a single underscore.

.. note::

   For GCHP, each diagnostic field must be followed by the name of the
   ESMF gridded component that it is associated with. For arrays in GEOS-Chem
   objects State_Met, State_Chm, and State_Diag this is
   :literal:`'GCHPchem',`. A few diagnostics may also be output from
   the advection component of GCHP which has ESMF gridded component
   name :literal:`DYNAMICS`.


If you are using GEOS-Chem Classic,you may also use a
:ref:`wildcard <histguide-wildcards>` to specify a given category
of species. In the above example :literal:`SpeciesConcVV_?ADV?`
refers to all advected species and :literal:`SpeciesConcVV_?ALL?`
refers to all species (both advected and non-advected).

.. note::

   GCHP does not allow the use of wildcards.  Each diagnostic
   quantity must be listed individually.

:literal:`::` separator
^^^^^^^^^^^^^^^^^^^^^^^

Signifies the end of the :ref:`histguide-configfile-sample-sc`
definition section. The :literal:`::` may be placed at any column.

SpeciesConc.subset
^^^^^^^^^^^^^^^^^^

Name of the second diagnostic collection specified in this sample
:file:`HISTORY.rc` configuration file.  In this collection we
will request output to be restricted to a subset of the horizontal
grid.

The :literal:`.template`, :literal:`.frequency`,
:literal:`.duration`,  :literal:`:mode`, and :literal:`.fields`
are described for the :ref:`histguide-configfile-sample-sc` collection
above, so we will not repeat them here.

SpeciesConcSubset.LON_RANGE
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Defines the longitude range (:literal:`min max`) where diagnostic
data will be archived.  Data outside of this range will be
ignored.  If this option is omitted, values at all longitudes
(:literal:`-180 180`) will be included.

SpeciesConcSubset.LAT_RANGE
^^^^^^^^^^^^^^^^^^^^^^^^^^^

Defines the latitude range (:literal:`min max`) where diagnostic
data will be archived.  Data outside of this range will be ignored.
If this option is omitted, values at all latitudes
(:literal:`-90 90`) will be included.

SpeciesConcSubset.levels
^^^^^^^^^^^^^^^^^^^^^^^^

Specifies the levels that you wish to be included in the diagnostic
archiving.  If omitted, data at all levels will be included.

.. note::

   In GEOS-Chem Classic, all levels between the minimum and
   maximum level specified will be included in the diagnostic
   archival.  This differs from the behavior in GCHP, which
   archives only the specified levels.

ConcAfterChem
^^^^^^^^^^^^^

Name of the third collection specified in this sample
:file:`HISTORY.rc` configuration file.

The :literal:`.template`, :literal:`.frequency`,
:literal:`.duration`,  :literal:`:mode`, and :literal:`.fields`
are described for the :ref:`histguide-configfile-sample-sc` collection
above, so we will not repeat them here.

ConcAftercChem.mode

In this example, the :literal:`ConcAfterChem.mode` setting
indicates that the :literal:`ConcAfterChem` collection will contain
ime-averaged data.  The averaging interval is set in the
:literal:`frequency` field.

.. _histguide-wildcards:

Wildcards (GEOS-Chem Classic only)
----------------------------------

For GEOS-Chem Classic diagnostic output, you can use the following
wildcards with diagnostic quantities that have a species/bin/reaction
dimension:

.. list-table::
   :header-rows: 1
   :widths: 18 43 40

   * - Wildcard
     - Description
     - Example
   * - ?ADV?
     - Advected species
     - SpeciesConcVV\_?ADV?
   * - ?AER?
     - Aerosol species
     - SpeciesConcVV\_?AER?
   * - ?ALL?
     - All species
     - SpeciesConcVV\_?ALL?
   * - ?DRY?
     - Dry-deposited species
     - SpeciesConcVV\_?DRY?
   * - ?DRYALT?
     - Species for the :ref:`histguide-concafterchem` collection
     - SpeciesConcVV\_?DRYALT
   * - ?DUSTBIN?
     - Dust bin number
     - AODdust550nm\_?DUSTBIN?
   * - ?FIX?
     - Fixed species in the KPP chemistry mechanism
     - SpeciesConcVV\_?FIX?
   * - ?GAS?
     - Gas-phase species
     - SpeciesConcVV\_?GAS?
   * - ?HYG?
     - Aerosol species that undergo hygroscopic growth - (e.g. black carbon)
     - AODhyg550nm\_?HYG?
   * - ?KPP?
     - All species (fixed variable) in the KPP chemistry mechanism
     - SpeciesConcVV\_?KPP?
   * - ?LOS?
     - Chemical loss species or familes
     - SpeciesConcVV\_?LOS?
   * - ?PHO?
     - Photolyzed species
     - SpeciesConcVV\_?PHO?
   * - ?PRD?
     - Chemical production species or families
     - SpeciesConcVV\_?PRD?
   * - ?RRTMG?
     - RRTMG-computed fluxes
     - RadAllSkywSurf\_?RRTMG?
   * - ?RXN?
     - KPP reaction rates
     - RxnRate\_?RXN?
   * - ?TOMASBIN?
     - TOMAS size bins
     - TomasH2SO4Mass\_?TOMASBIN?
   * - ?UVFLX?
     - UV flux bins
     - UVFluxDiffuse\_?UVFLX?
   * - ?VAR?
     - Variable (i.e. active) species in the KPP mechanism
     - SpeciesConcVV\_?VAR?
   * - ?WET?
     - Wet-deposited species
     - SpeciesConcVV\_?WET

.. _histguide-prefixes:

Prefixes
--------

You may add any field from the :code:`State_Met` and :code:`State_Chm`
objects to any diagnostic collection as well.  These fields must be
prefixed as described below:

.. list-table::
   :header-rows: 1

   * - Wildcard
     - Description
     - Example
   * - :literal:`Chem_`
     - Request diagnostic output from the :literal:`State_Chm` object
     - :literal:`Chem_pHCloud`
   * - :literal:`Met_`
     - Request diagnostic output from the :literal:`State_Met` object
     - :literal:`Met_SPHU`

.. _histguide-filename:

File naming convention
----------------------

As mentioned above, :ref:`histguide-configfile-sample-sc-tmpl`,
GEOS-Chem History files adhere to the following naming convention:

.. code-block:: none

   EXPID.collection-name.collection-template

e.g.

.. code-block:: none

   ../OutputDir/GEOSChem.SpeciesConc.20200101_0000z.nc4

The duration tag of each collection in :file:`HISTORY.rc` controls how
often a new file will be written to disk, as we saw :ref:`above
<histguide-configfile>`:

.. code-block:: none

   SpeciesConc.duration:           00000001 000000     # Write a new file each day
   SpeciesConcSubset.duration:     00000001 000000     # Write a new file each day
   ConcAfterChem.duration:         00000100 000000     # Write a new file each month

Therefore, based on all of these settings in our example
:file:`HISTORY.rc` file, GEOS-Chem will write the following netCDF
files to disk in the current run directory:

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

.. _histgude-vertcoords:

Vertical coordinates in netCDF files
------------------------------------

All netCDF files produced by GEOS-Chem (i.e. diagnostic files and
restart files) adhere to the :ref:`the COARDS netCDF convention
<coards-guide>`. for the lon, lat, and time dimensions.

For the vertical dimension, we have chosen to use the following
coordinate variables to include in GEOS-Chem Classic output files,
emulating the file format of the NCAR Community Earth System Model (CESM):

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

GCHP history files contain simply the lev coordinate which is the level index of the model.
Level 1 corresponds to surface for all diagnostics retrieved from GEOS-Chem
arrays State_Met, State_Diag, and State_Chm. These include all diagnostics
that have prefix :literal:`Met_` or :literal:`Chm_` as well as all other
:literal:`GCHPchem` diagnostics that do not begin with :literal:`Emis`.
Diagnostics that begin with :literal:`Emis` or that have gridded component
name other than :literal:`GCHPchem` have Level 1 correspond to top-of-atmosphere.

.. _histguide-collections:

======================
Diagnostic collections
======================

The diagnostic collections described in the sections below are used by
default in GEOS-Chem simulations. You can create your own customized
collections by modifying the :file:`HISTORY.rc` file.

The only restriction is that you cannot mix data that is placed on grid
box layer edges in the same collection as data placed on grid box layer
centers. This violates the netCDF convention that all data variables
have to be defined with the same vertical dimension.

.. note::

   For diagnostic quantities that have a species/bin/reaction
   dimension, we will use the abbreviation :literal:`<name|wc>` to
   indicate a species/bin/reaction name or wildcard.

   For example, :literal:`SpeciesConcVV_<name|wc>` means that the
   diagnostic quantity can be a single species
   (:literal:`SpeciesConcVV_O3`) or a wildcarded subset
   of species (:literal:`SpeciesConcVV_?ADV?`).

.. _histguide-advfluxvert:

AdvFluxVert
-----------

The **AdvFluxVert** collection contains diagnostics for vertical
transport in GEOS-Chem Classic advection.  In practice, this collection is only used to
obtain the vertical flux of O3 from GEOS-Chem Classic fullchem benchmark
simulations.  Most GEOS-Chem users will not need to activate this
collection.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     AdvFluxVert.template:    '%y4%m2%d2_%h2%n2z.nc4',
     AdvFluxVert.frequency:   00000100 000000
     AdvFluxVert.duration:    00000100 000000
     AdvFluxVert.mode:        'time-averaged'
     AdvFluxVert.fields:      'AdvFluxVert_O3',
   ::

**List of diagnostic fields in the AdvFluxVert collection**

.. list-table::
   :header-rows: 1

   * - Diagnostic field
     - Description
     - Units
     - Wildcards
   * - AdvFluxVert_<name|wc>
     - Vertical flux of species in advection
     - kg/s
     - ?ADV?

The following diagnostics are also available to be used, but are not
currently part of any collection:

.. list-table::
   :header-rows: 1

   * - Diagnostic field
     - Description
     - Units
     - Wildcards
   * - AdvFluxMerid_<name|wc>
     - Meridional (N/S) flux of species in advection
     - kg/s
     - ?ADV?
   * - AdvFluxZonal_<name|wc>
     - Zonal (E/W) flux of species in advection
     - kg/s
     - ?ADV?

.. _histguide-aerosolmass:

AerosolMass
-----------

The **AerosolMass** collection contains diagnostics for aerosol mass and
particulate matter from full-chemistry simulations.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     AerosolMass.template:       '%y4%m2%d2_%h2%n2z.nc4',
     AerosolMass.frequency:      00000100 000000
     AerosolMass.duration:       00000100 000000
     AerosolMass.mode:           'time-averaged'
     AerosolMass.fields:         'AerMassASOA    ',
                                 'AerMassBC      ',
                                 'AerMassINDIOL  ',
                                 'AerMassISN1OA  ',
                                 'AerMassISOA    ',
                                 'AerMassLVOCOA  ',
                                 'AerMassNH4     ',
                                 'AerMassNIT     ',
                                 'AerMassOPOA    ',
                                 'AerMassPOA     ',
                                 'AerMassSAL     ',
                                 'AerMassSO4     ',
                                 'AerMassSOAGX   ',
                                 'AerMassSOAIE   ',
                                 'AerMassSOAME   ',
                                 'AerMassSOAMG   ',
                                 'AerMassTSOA    ',
                                 'BetaNO         ',
                                 'PM25           ',
                                 'TotalBiogenicOA',
                                 'TotalOA        ',
                                 'TotalOC        ',
   ::

**List of diagnostic fields in the AerosolMass collection**

.. list-table::
   :header-rows: 1
   :widths: 20 50 20
   :class: top-align-table

   * - Diagnostic field
     - Description
     - Units
   * - AerMassASOA\ [#B]_
     - Aerosol products of light aromatics + IVOC oxidation
     - :math:`{\mu}g/m^3`
   * - AerMassBC
     - Aerosol products of light aromatics + IVOC oxidation
     - :math:`{\mu}g/m^3`
   * - AerMassINDIOL\ [#B]_
     - Generic aerosol-phase organonitrate hydrolysis product
     - :math:`{\mu}g/m^3`
   * - AerMassISN10A\ [#B]_
     - Aerosol phase 2nd generation hydroxynitrates formed from ISOP +
       NO3 rxn pathway
     - :math:`{\mu}g/m^3`
   * - AerMassISOA\ [#B]_
     - Aerosol products of isoprene oxidation
     - :math:`{\mu}g/m^3`
   * - AerMassLVOCOA\ [#B]_
     - Aerosol-phase low-volatility non-IEPOX product of ISOPOOH (RIP) oxidation
     - :math:`{\mu}g/m^3`
   * - AerMassNH4
     - Ammonium
     - :math:`{\mu}g/m^3`
   * - AerMassNIT
     - Inorganic nitrate aerosol
     - :math:`{\mu}g/m^3`
   * - AerMassPOA\ [#B]_
     - Aerosols from SVOCs
     - :math:`{\mu}g/m^3`
   * - AerMassOPOA\ [#B]_
     - Aerosols products of POG oxidation
     - :math:`{\mu}g/m^3`
   * - AerMassSAL
     - Sea salt aerosol (SALA+SALC)
     - :math:`{\mu}g/m^3`
   * - AerMassSO4
     - Sulfate
     - :math:`{\mu}g/m^3`
   * - AerMassSOAGX\ [#B]_
     - Aerosol phase glyoxal
     - :math:`{\mu}g/m^3`
   * - AerMassSOAIE\ [#B]_
     - Aerosol phase IEPOX
     - :math:`{\mu}g/m^3`
   * - AerMassSOAME\ [#B]_
     - Aerosol phase IMAE
     - :math:`{\mu}g/m^3`
   * - AerMassSOAMG\ [#B]_
     - Aerosol phase methylglyoxal
     - :math:`{\mu}g/m^3`
   * - AerMassTSOA\ [#B]_
     - Aerosol products of terpene oxidation
     - :math:`{\mu}g/m^3`
   * - BetaNO\ [#B]_
     - NO branching ratio
     - :math:`{\mu}g/m^3`
   * - PM25
     - Particulate matter (d < 2.5 :math:`{\mu}m`)
     - :math:`{\mu}g/m^3`
   * - TotalBiogenicOA\ [#C]_
     - Sum of all organic aerosol
     - :math:`{\mu}g/m^3`
   * - TotalOA\ [#B]_
     - Sum of all organic aerosol
     - :math:`{\mu}g/m^3`
   * - TotalOC
     - Sum of all organic carbon
     - :math:`{\mu}g/m^3`

.. rubric:: Notes for the AerosolMass collection

.. [#B] Only defined for fullchem simulations with complex SOA
 	species.

.. [#C] Defined for aerosol-only simulations or fullchem simulations.

.. _histguide-aerosols:

Aerosols
--------

The **Aerosols** collection contains diagnostics for aerosol optical
depth and related quantities from full-chemistry simulations.

.. note::

   Some diagnostic fields in the Aerosols collection may be computed
   at up to 3 wavelengths (:literal:`WL1`, :literal:`WL2`,
   :literal:`WL3`) as specified in this menu of the
   :file:`geoschem_config.yml` file:

   .. code-block:: yaml

       rrtmg_rad_transfer_model:
           ... etc ...
	   aod_wavelengths_in_nm:
           - 550

   For GEOS-Chem simulations that do not use the RRTMG radiative
   transfer model, you may specify only one wavelength :literal:`WL1`,
   which is set to a default value of 550 nm.  For GEOS-Chem
   simulations using RRTMG, you may specify up to 2 more additional
   wavelengths (:literal:`WL2` and :literal:`WL3`).  GEOS-Chem will
   replace the tokens :literal:`WL1`, :literal:`WL2`, :literal:`WL3`
   in diagnostic field names with the corresponding wavelength.

   For example, these diagnostic fields:

   .. code-block::

      AODHygWL1_BCPI
      AODDustWL1_DST1
      AODStratLiquidAerWL1
      AODPolarStratCloudWL1
      AODSOAfromAqIsopreneWL1
      AODStratLiquidAerWL1
      AODPolarStratCloudWL1

   will be saved to the :file:`GEOSChem.Aerosols.YYYYMMDD_hhmmz.nc4`
   file(s) under these names:

   .. code-block::

      AODHyg550nm_BCPI
      AODDust550nm_DST1
      AODStratLiquidAer550nm
      AODPolarStratCloud550nm
      AODSOAfromAqIsoprene550nm
      AODStratLiquidAer550nm
      AODPolarStratCloud550nm

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     Aerosols.template:    '%y4%m2%d2_%h2%n2z.nc4',
     Aerosols.frequency:   00000100 000000
     Aerosols.duration:    00000100 000000
     Aerosols.mode:        'time-averaged'
     Aerosols.fields:      'AODDust                      ',
                           'AODDustWL1_?DUSTBIN?         ',
                           'AODHygWL1_?HYG?              ',
                           'AODSOAfromAqIsopreneWL1      ',
                           'AODStratLiquidAerWL1         ',
                           'AODPolarStratCloudWL1        ',
                           'AerHygroscopicGrowth_?HYG?   ',
                           'AerNumDensityStratLiquid     ',
                           'AerNumDensityStratParticulate',
                           'AerAqueousVolume             ',
                           'AerSurfAreaDust              ',
                           'AerSurfAreaHyg_?HYG?         ',
                           'AerSurfAreaStratLiquid       ',
                           'AerSurfAreaPolarStratCloud   ',
                           'Chem_AeroAreaMDUST1          ',
                           'Chem_AeroAreaMDUST2          ',
                           'Chem_AeroAreaMDUST3          ',
                           'Chem_AeroAreaMDUST4          ',
                           'Chem_AeroAreaMDUST5          ',
                           'Chem_AeroAreaMDUST6          ',
                           'Chem_AeroAreaMDUST7          ',
                           'Chem_AeroAreaSULF            ',
                           'Chem_AeroAreaBC              ',
                           'Chem_AeroAreaOC              ',
                           'Chem_AeroAreaSSA             ',
                           'Chem_AeroAreaSSC             ',
                           'Chem_AeroAreaBGSULF          ',
                           'Chem_AeroAreaICEI            ',
                           'Chem_AeroRadiMDUST1          ',
                           'Chem_AeroRadiMDUST2          ',
                           'Chem_AeroRadiMDUST3          ',
                           'Chem_AeroRadiMDUST4          ',
                           'Chem_AeroRadiMDUST5          ',
                           'Chem_AeroRadiMDUST6          ',
                           'Chem_AeroRadiMDUST7          ',
                           'Chem_AeroRadiSULF            ',
                           'Chem_AeroRadiBC              ',
                           'Chem_AeroRadiOC              ',
                           'Chem_AeroRadiSSA             ',
                           'Chem_AeroRadiSSC             ',
                           'Chem_AeroRadiBGSULF          ',
                           'Chem_AeroRadiICEI            ',
                           'Chem_WetAeroAreaMDUST1       ',
                           'Chem_WetAeroAreaMDUST2       ',
                           'Chem_WetAeroAreaMDUST3       ',
                           'Chem_WetAeroAreaMDUST4       ',
                           'Chem_WetAeroAreaMDUST5       ',
                           'Chem_WetAeroAreaMDUST6       ',
                           'Chem_WetAeroAreaMDUST7       ',
                           'Chem_WetAeroAreaSULF         ',
                           'Chem_WetAeroAreaBC           ',
                           'Chem_WetAeroAreaOC           ',
                           'Chem_WetAeroAreaSSA          ',
                           'Chem_WetAeroAreaSSC          ',
                           'Chem_WetAeroAreaBGSULF       ',
                           'Chem_WetAeroAreaICEI         ',
                           'Chem_WetAeroRadiMDUST1       ',
                           'Chem_WetAeroRadiMDUST2       ',
                           'Chem_WetAeroRadiMDUST3       ',
                           'Chem_WetAeroRadiMDUST4       ',
                           'Chem_WetAeroRadiMDUST5       ',
                           'Chem_WetAeroRadiMDUST6       ',
                           'Chem_WetAeroRadiMDUST7       ',
                           'Chem_WetAeroRadiSULF         ',
                           'Chem_WetAeroRadiBC           ',
                           'Chem_WetAeroRadiOC           ',
                           'Chem_WetAeroRadiSSA          ',
                           'Chem_WetAeroRadiSSC          ',
                           'Chem_WetAeroRadiBGSULF       ',
                           'Chem_WetAeroRadiICEI         ',
                           'Chem_StatePSC                ',
                           'Chem_KhetiSLAN2O5H2O         ',
                           'Chem_KhetiSLAN2O5HCl         ',
                           'Chem_KhetiSLAClNO3H2O        ',
                           'Chem_KhetiSLAClNO3HCl        ',
                           'Chem_KhetiSLAClNO3HBr        ',
                           'Chem_KhetiSLABrNO3H2O        ',
                           'Chem_KhetiSLABrNO3HCl        ',
                           'Chem_KhetiSLAHOClHCl         ',
                           'Chem_KhetiSLAHOClHBr         ',
                           'Chem_KhetiSLAHOBrHCl         ',
                           'Chem_KhetiSLAHOBrHBr         ',
   ::

**List of diagnostic fields in the Aerosols collection**

.. list-table::
   :header-rows: 1
   :widths: 25 40 10 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - AODDust\ [#X]_
     - Mineral dust optical depth @ WL1
     - 1
     -
   * - AODDustWL1\_<name|wc>\ [#X]_
     - AOD for each dust bin @ WL1
     - 1
     - ?DUSTBIN?
   * - AODHygWL1\_<name|wc>\ [#X]_
     - AOD @ WL1 for aerosol species
     - 1
     - ?HYG?
   * - AODSOAfromAqIsopreneWL1\ [#Y]_
     - Optical depth of SOA from aqueous isoprene @ WL1
     - 1
     -
   * - AODStratLiquidAerWL1
     - Stratospheric liquid optical depth @ WL1
     - 1
     -
   * - AODPolarStratCloudWL1
     - Polar stratospheric cloud type 1a/2 optical depth @ WL1
     - 1
     -
   * - AerHygroscopicGrowth\_<name|wc>\ [#X]_
     - Hygroscopic growth of aerosol species
     - 1
     - ?HYG?
   * - AerNumDensityStratLiquid
     - Stratospheric liquid aerosol number density
     - 1/cm3
     -
   * - AerNumDensityStratParticulate
     - Stratospheric particulate aerosol number density
     - 1/cm3
     -
   * - AerAqueousVolume
     - Aqueous aerosol volume
     - cm2/cm3
     -
   * - AerSurfAreaDust
     - Surface area of mineral dust
     - cm2/cm3
     -
   * - AerSurfAreaHyg\_<name|wc>
     - Surface area of aerosol species
     - cm2/cm3
     - ?HYG?
   * - AerSurfAreaStratLiquid
     - Stratospheric liquid surface area
     - cm2/cm3
     -
   * - AerSurfaceAreaPolarStratCloud
     - Polar stratospheric cloud type 1a/2 surface area
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST1\ [#X]_
     - Dry aerosol area for mineral dust (0.15 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST2\ [#X]_
     - Dry aerosol area for mineral dust (0.25 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST3\ [#X]_
     - Dry aerosol area for mineral dust (0.4 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST4\ [#X]_
     - Dry aerosol area for mineral dust (0.8 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST5\ [#X]_
     - Dry aerosol area for mineral dust (1.5 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST6\ [#X]_
     - Dry aerosol area for mineral dust (2.5 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST7\ [#X]_
     - Dry aerosol area for mineral dust (4.0 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaSULF\ [#X]_
     - Dry aerosol area for sulfate aerosol
     - cm2/cm3
     -
   * - Chem_AeroAreaBC\ [#X]_
     - Dry aerosol area for black carbon
     - cm2/cm3
     -
   * - Chem_AeroAreaOC\ [#X]_
     - Dry aerosol area for organic carbon
     - cm2/cm3
     -
   * - Chem_AeroAreaSSA\ [#X]_
     - Dry aerosol area for sea salt aerosol, accumulation mode
     - cm2/cm3
     -
   * - Chem_AeroAreaSSC\ [#X]_
     - Dry aerosol area for sea salt aerosol, coarse mode
     - cm2/cm3
     -
   * - Chem_AeroAreaBGSULF
     - Dry aerosol area for background stratospheric sulfate
     - cm2/cm3
     -
   * - Chem_AeroAreaICEI
     - Dry aerosol area for irregular ice cloud
     - cm2/cm3
     -
   * - Chem_AeroRadiMDUST1\ [#X]_
     - Dry aerosol radius for mineral dust (0.15 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRadiMDUST2\ [#X]_
     - Dry aerosol radius for mineral dust (0.25 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRadiMDUST3\ [#X]_
     - Dry aerosol radius for mineral dust (0.4 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRadiMDUST4\ [#X]_
     - Dry aerosol radius for mineral dust (0.8 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRadiMDUST5\ [#X]_
     - Dry aerosol radius for mineral dust (1.5 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRAdiMDUST6\ [#X]_
     - Dry aerosol radius for mineral dust (2.5 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRadiMDUST7\ [#X]_
     - Dry aerosol radius for mineral dust (4.0 :math:`{\mu}m`)
     - cm
     -
   * - Chem_AeroRadiSULF\ [#X]_
     - Dry aerosol radius for sulfate aerosol
     - cm
     -
   * - Chem_AeroRadiBC\ [#X]_
     - Dry aerosol radius for black carbon
     - cm
     -
   * - Chem_AeroRadiOC\ [#X]_
     - Dry aerosol radius for organic carbon
     - cm
     -
   * - Chem_AeroRadiSSA\ [#X]_
     - Dry aerosol radius for sea salt aerosol, accumulation mode
     - cm
     -
   * - Chem_AeroRadiSSC\ [#X]_
     - Dry aerosol radius for sea salt aerosol, coarse mode
     - cm
     -
   * - Chem_AeroRadiBGSULF
     - Dry aerosol radius for background stratospheric sulfate
     - cm
     -
   * - Chem_AeroRadiICEI
     - Dry aerosol radius for irregular ice cloud
     - cm
     -
   * - Chem_WetAeroAreaMDUST1\ [#X]_
     - Wet aerosol area for mineral dust (0.15 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_WetAeroAreaMDUST2\ [#X]_
     - Wet aerosol area for mineral dust (0.25 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_WetAeroAreaMDUST3\ [#X]_
     - Wet aerosol area for mineral dust (0.4 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_WetAeroAreaMDUST4\ [#X]_
     - Wet aerosol area for mineral dust (0.8 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_WetAeroAreaMDUST5\ [#X]_
     - Wet aerosol area for mineral dust (1.5 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_WetAeroAreaMDUST6\ [#X]_
     - Wet aerosol area for mineral dust (2.5 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_AeroAreaMDUST7\ [#X]_
     - Dry aerosol area for mineral dust (4.0 :math:`{\mu}m`)
     - cm2/cm3
     -
   * - Chem_WetAeroAreaSULF\ [#X]_
     - Wet aerosol area for sulfate aerosol
     - cm2/cm3
     -
   * - Chem_WetAeroAreaBC\ [#X]_
     - Wet aerosol area for black carbon
     - cm2/cm3
     -
   * - Chem_WetAeroAreaOC\ [#X]_
     - Wet aerosol area for organic carbon
     - cm2/cm3
     -
   * - Chem_WetAeroAreaSSA\ [#X]_
     - Wet aerosol area for sea salt aerosol, accumulation mode
     - cm2/cm3
     -
   * - Chem_WetAeroAreaSSC\ [#X]_
     - Wet aerosol area for sea salt aerosol, coarse mode
     - cm2/cm3
     -
   * - Chem_WetAeroAreaBGSULF
     - Wet aerosol area for background stratospheric sulfate
     - cm2/cm3
     -
   * - Chem_WetAeroAreaICEI
     - Wet aerosol area for irregular ice cloud
     - cm2/cm3
     -
   * - Chem_WetAeroRadiMDUST1\ [#X]_
     - Wet aerosol radius for mineral dust (0.15 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiMDUST2\ [#X]_
     - Wet aerosol radius for mineral dust (0.25 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiMDUST3\ [#X]_
     - Wet aerosol radius for mineral dust (0.4 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiMDUST4\ [#X]_
     - Wet aerosol radius for mineral dust (0.8 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiMDUST5 \ [#X]_
     - Wet aerosol radius for mineral dust (1.5 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiMDUST6\ [#X]_
     - Wet aerosol radius for mineral dust (2.5 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiMDUST7\ [#X]_
     - Wet aerosol radius for mineral dust (4.0 :math:`{\mu}m`)
     - cm
     -
   * - Chem_WetAeroRadiSULF\ [#X]_
     - Wet aerosol radius for sulfate aerosol
     - cm
     -
   * - Chem_WetAeroRadiBC\ [#X]_
     - Wet aerosol radius for black carbon
     - cm
     -
   * - Chem_WetAeroRadiOC\ [#X]_
     - Wet aerosol radius for organic carbon
     - cm
     -
   * - Chem_WetAeroRadiSSA\ [#X]_
     - Wet aerosol radius for sea salt aerosol, accumulation mode
     - cm
     -
   * - Chem_WetAeroRadiSSC\ [#X]_
     - Wet aerosol radius for sea salt aerosol, coarse mode
     - cm
     -
   * - Chem_WetAeroRadiBGSULF
     - Wet aerosol radius for background stratospheric sulfate
     - cm
     -
   * - Chem_WetAeroRadiICEI
     - Wet aerosol radius for irregular ice cloud
     - cm
     -
   * - Chem_KhetiSLAN2O5H2O
     - Sticking coefficient for N2O5 + H2O rxn
     - 1
     -
   * - Chem_KhetiSLAN2O5HCl
     - Sticking coefficient for N2O5 + HCl rxn
     - 1
     -
   * - Chem_KhetiSLACLNO3H2O
     - Sticking coefficient for ClNO3 + H2O rxn
     - 1
     -
   * - Chem_KhetiSLACLNO3HCL
     - Sticking coefficient for ClNO3 + HCl rxn
     - 1
     -
   * - Chem_KhetiSLACLNO3HBR
     - Sticking coefficient for ClNO3 + HBr rxn
     - 1
     -
   * - Chem_KhetiSLABRNO3H2O
     - Sticking coefficient for BrNO3 + H2O rxn
     - 1
     -
   * - Chem_KhetiSLABRNO3HCL
     - Sticking coefficient for BrNO3 + HCl rxn
     - 1
     -
   * - Chem_KhetiSLAHOCLHCL
     - Sticking coefficient for HOCl + HCl rxn
     - 1
     -
   * - Chem_KhetiSLAHOCLHBR
     - Sticking coefficient for HOCl + HBr rxn
     - 1
     -
   * - Chem_KhetiSLAHOBRHCL
     - Sticking coefficient for HOBr + HCl rxn
     - 1
     -
   * - Chem_KhetiSLAHOBRHBR
     - Sticking coefficient for HOBr + HBr rxn
     - 1
     -

.. rubric:: Notes for the Aerosols colletion

.. [#X] Defined for aerosol-only and fullchem simulations.

.. [#Y] Only defined for fullchem simulation with complex SOA species.

.. _histguide-boundaryconditions:

BoundaryConditions
------------------

The **BoundaryConditions** diagnostic collection contains advected
species concentrations (archived from a global simulation) that will
be used by GEOS-Chen Classic nested-grid simulations as transport
boundary conditions.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     BoundaryConditions.template:   '%y4%m2%d2_%h2%n2z.nc4',
     BoundaryConditions.frequency:  00000000 030000
     BoundaryConditions.duration:   00000100 000000
     BoundaryConditions.mode:       'instantaneous'
     BoundaryConditions.fields:     'SpeciesBC_?ADV?',
   ::

**List of diagnostic fields in the BoundaryConditions collection**

.. list-table::
   :header-rows: 1
   :widths: 25 40 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - SpeciesBC\_<name|wc>\ [#D]_
     - Advected species concentrations used as boundary conditions
       in GEOS-Chem Classic nested-grid simulations
     - mol/mol dry air
     - ?ADV?

.. rubric:: Footnotes

.. [#D] This diagnostic is only for use with GEOS-Chem Classic.

.. _histguide-budget:

Budget
------

The **Budget** diagnostic collection is a 2D diagnostic containing the
mass tendencies per grid cell, in kg/s, for each species within a region
of the column and across each GEOS-Chem component. The diagnostic is
calculated by taking the difference in vertically summed column mass
before and after major GEOS-Chem components.

There are three pre-defined column regions defined for this diagnostic:
troposphere-only, PBL-only, and full column, as well as a user-defined
column region.  By post-processing this diagnostic you can calculate
global mass change or mass change across regions by summing the
diagnostic values for the relevant grid cells. You can also retrieve
the mass change across a longer chunk of time by multiplying the
time-averaged output by the number of seconds in the averaging period.

While there are seven major components in GEOS-Chem, there are only six
implemented for the budget diagnostics: chemistry, mixing, convection,
transport (GEOS-Chem Classic only), wet deposition, and combined emissions
and dry deposition.

.. attention::

   Emissions and dry deposition components are combined together for this
   diagnostic because of the way they are applied at the same time. Furthermore,
   if using non-local PBL mixing then the emissions and dry deposition budget
   diagnostic will not capture all fluxes from these sources and sinks. This is
   because emissions and dry deposition tendencies below the PBL are applied within
   mixing instead. When using full mixing, however, mixing and
   emissions/dry deposition budget diagnostics are fully separated.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     Budget.template:     '%y4%m2%d2_%h2%n2z.nc4',
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

**List of diagnostic fields in the Budget collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 20

   * - Diagnostic field
     - Mass tendency (kg/s) across ...
     - Wildcard
   * - BudgetChemistryFull\_<name|wc>
     - Chemistry (full atmosphere)
     - ?ADV?
   * - BudgetChemistryLevs1to35\_<name|wc>\ [#E]_
     - Chemistry (fixed level range)
     - ?ADV?
   * - BudgetChemistryPBL\_<name|wc>
     - Chemistry (PBL only)
     - ?ADV?
   * - BudgetChemistryTrop\_<name|wc>
     - Chemistry (troposphere only)
     - ?ADV?
   * - BudgetConvectionFull\_<name|wc>
     - Convection (full atmosphere)
     - ?ADV?
   * - BudgetConvectionLevs1to35\_<name|wc>\ [#E]_
     - Convection (fixed level range)
     - ?ADV?
   * - BudgetConvectionPBL\_<name|wc>
     - Convection (PBL only)
     - ?ADV?
   * - BudgetConvectionTrop\_<name|wc>
     - Convection (troposphere only)
     - ?ADV?
   * - BudgetEmisDepFull\_<name|wc>\ [#F]_
     - Emissions & dry deposition (full atmosphere)
     - ?ADV?
   * - BudgetEmisDepLevs1to35\_<name|wc>\ [#E]_ [#F]_
     - Emissions & dry deposition (fixed level range)
     - ?ADV?
   * - BudgetEmisDepPBL\_<name|wc>\ [#F]_
     - Emissions & dry deposition (PBL only)
     - ?ADV?
   * - BudgetEmisDepTrop\_<name|wc>\ [#F]_
     - Emissions & dry deposition (troposphere only)
     - ?ADV?
   * - BudgetMixingFull\_<name|wc>\ [#G]_
     - PBL mixing (full atmosphere)
     - ?ADV?
   * - BudgetMixingLevs1to35\_<name|wc>\ [#E]_ [#G]_
     - PBL mixing (fixed level range)
     - ?ADV?
   * - BudgetMixingPBL\_<name|wc>\ [#G]_
     - PBL mixing (PBL only)
     - ?ADV?
   * - BudgetMixingTrop\_<name|wc>\ [#G]_
     - PBL mixing (troposphere only)
     - ?ADV?
   * - BudgetTransportFull\_<name|wc>\ [#H]_
     - Transport (full atmosphere)
     - ?ADV?
   * - BudgetTransportLevs1to35\_<name|wc>\ [#E]_ [#H]_
     - Transport (fixed level range)
     - ?ADV?
   * - BudgetTransportPBL\_<name|wc>\ [#H]_
     - Transport (PBL only)
     - ?ADV?
   * - BudgetTransportTrop\_<name|wc>\ [#H]_
     - Transport (troposphere only)
     - ?ADV?
   * - BudgetWetDepFull\_<name|wc>
     - Wet deposition (full atmosphere)
     - ?WET?
   * - BudgetWetDepLevs1to35\_<name|wc>\ [#E]_
     - Wet deposition (fixed level range)
     - ?WET?
   * - BudgetWetDepPBL\_<name|wc>
     - Wet deposition (PBL only)
     - ?WET?

.. rubric:: Notes for the Budget collection

.. [#E] These diagnostic quantities allow you to compute mass
	tendencies in a fixed level range.  The lower level and upper level of
	the range is specified in the diagnostic name
	(:literal:`LevsXtoY`).  Levels 1 to 35 (surface to
	approximately the tropopause) are the default settings.

.. [#F] The emissions and dry deposition budget diagnostics will not
	capture all fluxes if using the non-local PBL mixing scheme since
	these tendencies are applied within mixing in :file:`vdiff_mod.F90`
	below the  PBL. When using full mixing, however, mixing and emissions/dry
	deposition are fully separated.

.. [#G] The mixing budget diagnostics includes the application of
	 emissions and dry deposition below the PBL if using the non-local PBL
	 mixing scheme (vdiff).

.. [#H] GEOS-Chem Classic only

.. _histguide-carbon:

Carbon
------

The **Carbon** collection contains diagnostic fields specific to the
GEOS-Chem carbon gases simulation.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     Carbon.template:    '%y4%m2%d2_%h2%n2z.nc4',
     Carbon.frequency:   00000100 000000
     Carbon.duration:    00000100 000000
     Carbon.mode:        'time-averaged'
     Carbon.fields:      'OHconcAfterChem',
                         'ProdCOfromCH4  ',
                         'ProdCOfromNMVOC',
                         'ProdCO2fromCO  ',
   ::

**List of diagnostic fields in the Carbon collection**

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Diagnostic field
     - Description
     - Units
   * - OHconcAfterChem
     - OH concentration immediately after chemistry
     - molec/cm3
   * - ProdCOfromCH4
     - Production of CO from CH4
     - molec/cm3
   * - ProdCOfromNMVOC
     - Production of CO from non-methane VOCs
     - molec/cm3
   * - ProdCO2fromCO
     - Production of CO2 from CO oxidation
     - molec/cm3

.. _histguide-cloudconvflux:

CloudConvFlux
-------------

The **CloudConvFlux** collection contains diagnostics for mass fluxes in
cloud convection.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     CloudConvFlux.template:     '%y4%m2%d2_%h2%n2z.nc4',
     CloudConvFlux.frequency:    00000100 000000
     CloudConvFlux.duration:     00000100 000000
     CloudConvFlux.mode:         'time-averaged'
     CloudConvFlux.fields:       'CloudConvFlux_?ADV?',
   ::

**List of diagnostic fields in the CloudConvFlux collection**

.. list-table::
   :header-rows: 1
   :widths: 30 40 15 25

   * - Diagnostic field
     - Description
     - Units
     - Wildcards
   * - CloudConvFlux\_<name|wc>
     - Mass change due to cloud convection
     - kg/s
     - ?ADV? |br| ?GAS? |br| ?WET?

.. _histguide-concabovesfc:

ConcAboveSfc
------------

The **ConcAboveSfc** diagnostic collection uses dry deposition
quantities (surface resistance, dry deposition velocity) to compute the
species concentration of O3 and HNO3 at a given altitude (such as 10 m)
above the surface. This will facilitate comparison between GEOS-Chem and
observational networks (e.g. CASTNET), which often place instruments
above the canopy at approx. 10m height.

.. attention::

   If dry deposition is turned off in your simulation, then you must
   disable this collection, or else your run will stop with an error.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     ConcAboveSfc.template:      '%y4%m2%d2_%h2%n2z.nc4',
     ConcAboveSfc.mode:          'instantaneous'
     ConcAboveSfc.fields:        'DryDepRaALT1             ',
                                 'DryDepVelForALT1_?DRYALT?',
                                 'SpeciesConcALT1_?DRYALT? ',
     ::

**List of diagnostic fields in the ConcAboveSfc collection**

.. list-table::
   :header-rows: 1
   :widths: 40 30 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - DryDepRaALT1\ [#Z]_
     - Dry deposition aerodynamic resistance at ALT1 meters above the surface
     - s/cm
     -
   * - DryDepVelForALT1\_<name|wc>\ [#Z]_ [#I]_
     - Dry deposition velocity of species tagged with the |br| ?DRYALT? wildcard
     - cm/s
     - ?DRYALT?
   * - SpeciesConcALT1\_<name|wc>\ [#Z]_ [#I]_
     - Concentration of species tagged with the |br| ?DRYALT? wildcard
     - mol/mol dry air
     - ?DRYALT?

.. rubric:: Notes about the ConcAboveSfc collection

.. [#Z] Replace :literal:`ALT1` with the altitude in meters above the
	surface at which you would like these quantities computed.
	For example: :literal:`DryDepVelFor10m_?DRYALT?`, etc.

.. [#I] Currently the :literal:`?DRYALT?` species are O3 and HNO3.

.. _histguide-concafterchem:

ConcAfterChem
-------------

The **ConcAfterChem** collection contains diagnostics for OH, HO2, etc.
species immediately upon exiting the chemical solver.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     ConcAfterChem.template:     '%y4%m2%d2_%h2%n2z.nc4',
     ConcAfterChem.frequency:    00000100 000000
     ConcAfterChem.duration:     00000100 000000
     ConcAfterChem.mode:         'time-averaged'
     ConcAfterChem.fields:       'OHconcAfterChem ',
                                 'HO2concAfterChem',
                                 'O1DconcAfterChem',
                                 'O3PconcAfterChem',
                                 'O3concAfterChem ',
                                 'RO2concAfterChem',
   ::

**List of diagnostic fields in the ConcAfterChem collection**

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Diagnostic field
     - Description
     - Units
   * - HO2concAfterChem
     - HO2 immediately after exiting the chemical solver
     - mol/mol
   * - O1DconcAfterChem
     - O1D immediately after exiting the chemical solver
     - molec/cm3
   * - O3concAfterChem
     - O3 immediately after exiting the chemical solver
     - molec/cm3
   * - O3PconcAfterChem
     - O3P immediately after exiting the chemical solver
     - molec/cm3
   * - OHconcAfterChem
     - OH immediately after exiting the chemical solver
     - molec/cm3
   * - RO2concAfterChem
     - RO2 immediately after exiting the chemical solver
     - molec/cm3

.. _histguide-drydep:

DryDep
------

The **DryDep** collection contains diagnostics for the flux and velocity
of each species lost to dry-deposition.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     DryDep.template:     '%y4%m2%d2_%h2%n2z.nc4',
     DryDep.frequency:    00000100 000000
     DryDep.duration:     00000100 000000
     DryDep.mode:         'time-averaged'
     DryDep.fields:       'DryDepVel_?DRY?',
                          'DryDepMix_?DRY?',
                          'DryDepChm_?DRY?',
                          'DryDep_?DRY?   ',
   ::

**List of diagnostic fields in the DryDep collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 20 20

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - DryDep\_<name|wc>
     - Total dry deposition flux
     - molec/cm2/s
     - ?DRY?
   * - DryDepChm\_<name|wc>
     - Dry deposition flux (computed in chemistry)
     - molec/cm2/s
     - ?DRY?
   * - DryDepMix\_<name|wc>
     - Dry deposition flux (computed in the PBL)
     - molec/cm2/s
     - ?DRY?
   * - DryDepVel\_<name|wc>
     - Dry deposition velocity
     - cm/s
     - ?DRY?

.. _histguide-jvalues:

JValues
-------

The **JValues** collection contains diagnostics for photolysis rates for
various chemical species, obtained from the photolysis mechanism.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     JValues.template:           '%y4%m2%d2_%h2%n2z.nc4',
     JValues.frequency:          00000100 000000
     JValues.duration:           00000100 000000
     JValues.mode:               'time-averaged'
     JValues.fields:             'Jval_?PHO?',
                                 'JvalO3O1D ',
				 'JvalO3O3P ',
   ::

**List of diagnostic fields in the JValues collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 15 20

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - Jval\_<name|wc>
     - Photolysis rates
     - 1/s
     - ?PHO?
   * - JvalO3O1D
     - Photolysis rate of O3 :math:`\rightarrow` O1D
     - 1/s
     -
   * - JvalO3O3P
     - Photolysis rate of O3 :math:`\rightarrow` O3P
     - 1/s
     -

.. _histguide-kppardiags:

KppARDiags
----------

The **KppARDiags** collection contains diagnostics for the KPP
Rosenbrock solver with mechanism auto-reduction. You may leave this
collection disabled unless you are interested in assessing the
solver's performance.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     KppARDiags.template:        '%y4%m2%d2_%h2%n2z.nc4',
     KppARDiags.frequency:       00000100 000000
     KppARDiags.duration:        00000100 000000
     KppARDiags.mode:            'time-averaged'
     KppARDiags.fields:          'KppAutoReducerNVAR',
                                 'KppAutoReduceThres',
                                 'KppcNONZERO       ',
   ::

**List of diagnostic fields in the KppARDiags collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 20

   * - Diagnostic field
     - Description
     - Units
   * - KppAutoReducerNVAR
     - Number of species (:code:`rNVAR`) in the auto-reduced mechanism
     - count
   * - KppAutoReduceThres
     - Auto-reduction threshold
     - molec/cm3/s
   * - KppcNONZERO
     - Number of nonzero elements (:code:`cNONZERO`) in LU
       decomposition in the auto-reduced mechanism
     - count

.. _histguide-kppdiags:

KppDiags
--------

The **KppDiags** collection contains KPP solver diagnostics. You may
leave this collection disabled unless you are interested in assessing
the solver's performance.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

    KppDiags.template:   '%y4%m2%d2_%h2%n2z.nc4',
    KppDiags.frequency:  00000100 000000
    KppDiags.duration:   00000100 000000
    KppDiags.mode:       'time-averaged'
    KppDiags.fields:     'KppIntCounts',
                         'KppJacCounts',
                         'KppTotSteps ',
                         'KppAccSteps ',
                         'KppRejSteps ',
                         'KppLuDecomps',
                         'KppSubsts   ',
                         'KppSmDecomps',
   ::

**List of diagnostic fields in the KppDiags collection**

.. list-table::
   :header-rows: 1
   :widths: 25 55 20

   * - Diagnostic field
     - Description
     - Units
   * - KppAccSteps
     - Number of accepted integration timesteps
     - count
   * - KppIntCounts
     - Number of times the function to be evaluated was called from
       within the integrator
     - count
   * - KppJacCounts
     - Number of times the Jacobian matrix was constructed
     - count
   * - KppLuDecomps
     - Number of LU decompositions performed
     - count
   * - KppNegatives
     - Number of negative values
     - count
   * - KppNegatives0
     - Number of negative values except on the first timestep
     - count
   * - KppSmDecomps\ [#J]_
     - Number of singular matrix decompositions performed
     - count
   * - KppSubsts
     - Number of matrix substitutions performed (both forward &
       backward substitutions)
     - count
   * - KppRejSteps
     - Number of rejected integration timesteps
     - count
   * - KppTime\ [#JJ]_
     - Time spent within the integrator
     - s
   * - KppTotSteps
     - Total number of integration timesteps
     - count

.. rubric:: Footnotes

.. [#J] For Rosenbrock solvers, KppSmDecomps will be zero everywhere,
	because the Rosenbrock method utilizes LU decomposition
	instead of singular matrix decomposition.

.. [#JJ] KppTime will likely not be identical between two successive
  	 simulations, as the time spent in the integrator will depend
	 on local cluster conditions.

.. _histguide-mercurychem:

MercuryChem
-----------

The **MercuryChem** collection contains concentrations and prod/loss
diagnostic outputs for the GEOS-Chem mercury simulation.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     MercuryChem.template:    '%y4%m2%d2_%h2%n2z.nc4',
     MercuryChem.frequency:   ${RUNDIR_HIST_TIME_AVG_FREQ}
     MercuryChem.duration:    ${RUNDIR_HIST_TIME_AVG_DUR}
     MercuryChem.mode:        'time-averaged'
     MercuryChem.fields:      'HgBrAfterChem  ',
                              'HgClAfterChem  ',
                              'HgOHAfterChem  ',
                              'HgBrOAfterChem ',
                              'HgClOAfterChem ',
                              'HgOHOAfterChem ',
                              'Hg2GToHg2P     ',
                              'Hg2PToHg2G     ',
                              'Hg2GasToHg2StrP',
                              'Hg2GasToSSA    ',
   ::

**List of diagnostic fields in the MercuryChem collection**

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Diagnostic field
     - Description
     - Units
   * - Hg2GToHg2P
     - Hg2 gas transferred to Hg2P
     - molec/cm3/s
   * - Hg2GasToHg2StrP
     - Hg2 gas transferred to Hg2StrP
     - molec/cm3/s
   * - Hg2GasToSSA
     - Hg2 gas transferred to sea salt aerosol
     - molec/cm3/s
   * - Hg2PToHg2G
     - Hg2P transferred to Hg2 gas
     - molec/cm3/s
   * - HgBrAfterChem
     - HgBr concentration immediately after chemistry
     - mol/mol
   * - HgBrOAfterChem
     - HgBrO concentration immediately after chemistry
     - mol/mol
   * - HgClAfterChem
     - HgCl concentration immediately after chemistry
     - mol/mol
   * - HgClOAfterChem
     - HgClO concentration immediately after chemistry
     - mol/mol
   * - HgOHAfterChem
     - HgOH concentration immediately after chemistry
     - mol/mol
   * - HgOHOAfterChem
     - HgOHO concentration immediately after chemistry
     - mol/mol

.. _histguide-mercuryemis:

MercuryEmis
-----------

The **MercuryEmis** collection contains emission diagnostics for the
GEOS-Chem mercury simulation.

.. note::

   Several other mercury emission diagnostics are archived via
   `HEMCO diagnostics
   <https://hemco.readthedocs.io/en/latesthco-ref-guide/diagnostics.html>`_.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     MercuryEmis.template:   '%y4%m2%d2_%h2%n2z.nc4',
     MercuryEmis.frequency:  00000100 000000
     MercuryEmis.duration:   00000100 000000
     MercuryEmis.mode:       'time-averaged'
     MercuryEmis.fields:     'EmisHg0land ',
                             'EmisHg0ocean',
                             'EmisHg0soil ',
                             'EmisHg0snow ',
   ::

**List of diagnostic fields in the MercuryEmis collection**

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Diagnostic field
     - Description
     - Units
   * - EmisHg0land
     - Re-emission of Hg0 from land
     - kg/s
   * - EmisHg0ocean
     - Emissions of Hg0 from oceans
     - kg/s
   * - EmisHg0snow
     - Emission of Hg0 from snowpack
     - kg/s
   * - EmisHg0soil
     - Emissions of Hg0 from soils
     - kg/s

.. _histguide-mercuryocean:

MercuryOcean
------------

The **MercuryOcean** collection contains diagnostics from the mercury
ocean model, used in the GEOS-Chem mercury simulation.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     MercuryOcean.template:    '%y4%m2%d2_%h2%n2z.nc4',
     MercuryOcean.frequency:   00000000 040000
     MercuryOcean.duration:    00000000 040000
     MercuryOcean.mode:        'time-averaged'
     MercuryOcean.fields:      'FluxHg0fromAirToOcean   ',
                               'FluxHg0fromOceanToAir   ',
                               'FluxHg2HgPfromAirToOcean',
                               'FluxHg2toDeepOcean      ',
                               'FluxOCtoDeepOcean       ',
                               'MassHg0inOcean          ',
                               'MassHg2inOcean          ',
                               'MassHgPinOcean          ',
                               'MassHgTotalInOcean      ',
   ::

**List of diagnostic fields in the MercuryOcean collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 20

   * - Diagnostic field
     - Description
     - Units
   * - FluxHg0fromAirToOcean
     - Deposition flux of Hg0 from the atmosphere to the ocean
     - kg/s
   * - FluxHg0fromOceanToAir
     - Volatization flux of Hg0 from the ocean to the atmosphere
     - kg/s
   * - FluxHg2HgPfromAirToOcean
     - Deposition flux of Hg2 + HgP from atmosphere to ocean
     - kg/s
   * - FluxHg2toDeepOcean
     - Flux of Hg2 sunk to the deep ocean
     - kg/s
   * - MassHg0inOcean
     - Total mass of oceanic Hg0
     - kg
   * - MassHg2inOcean
     - Total mass of oceanic Hg2
     - kg
   * - MassHgPinOcean
     - Total mass of oceanic HgP
     - kg
   * - MassHgTotalInOcean
     - Total mass of all organic mercury
     - kg

.. _histguide-metrics:

Metrics
-------

The **Metrics** collection contains diagnostics for computing OH metrics
from a GEOS-Chem full chemistry simulation (needed for benchmarking).

To compute the OH metrics, you must run the Python script
:file:`metrics.py` that ships with each fullchem or CH4 run directory.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     Metrics.template:    '%y4%m2%d2_%h2%n2z.nc4',
     Metrics.frequency:   'End',
     Metrics.duration:    'End',
     Metrics.mode:        'time-averaged'
     Metrics.fields:      'AirMassColumnFull       ',
                          'LossOHbyCH4columnTrop   ',
                          'LossOHbyMCFcolumnTrop   ',
                          'OHwgtByAirMassColumnFull',
   ::

**List of diagnostic fields in the Metrics collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 20

   * - Diagnostic field
     - Description
     - Units
   * - AirMassColumnFull
     - Air mass column (full atmosphere)
     - kg
   * - LossOHbyCH4columnTrop
     - Loss rate of CH4 by OH (tropospheric column sums)
     - molec/cm3
   * - LossOHbyMCFcolumnTrop
     - Loss rate of CH4 by CH3CCl3 aka MCF (tropospheric column sums)
     - molec/cm3
   * - OHwgtByAirMassColumnFull
     - Airmass-weighted OH concentration (full atmosphere column sums)
     - kg air/kg OH/m3


.. _histguide-prodloss:

ProdLoss
--------

The **ProdLoss** collection contains chemical production and loss rates.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     ProdLoss.template:   '%y4%m2%d2_%h2%n2z.nc4',
     ProdLoss.frequency:  00000100 000000
     ProdLoss.duration:   00000100 000000
     ProdLoss.mode:       'time-averaged'
     ProdLoss.fields:     'Prod_?PRD?                  ',
                          'ProdBCPIfromBCPO            ',
                          'ProdOCPIfromOCPO            ',
                          'ProdHMSfromSO2andHCHOinCloud',
                          'ProdSO2andHCHOfromHMSinCloud',
                          'ProdSO4fromHMSinCloud       ',
                          'ProdSO4fromH2O2inCloud      ',
                          'ProdSO4fromO2inCloudMetal   ',
                          'ProdSO4fromO3inCloud        ',
                          'ProdSO4fromO3inSeaSalt      ',
                          'ProdSO4fromHOBrInCloud      ',
                          'ProdSO4fromSRO3             ',
                          'ProdSO4fromSRHObr           ',
                          'ProdSO4fromO3s              ',
                          'Loss_?LOS?                  ',
                          'LossHNO3onSeaSalt           ',
                          'ProdCOfromCH4               ',
                          'ProdCOfromNMVOC             ',
   ::

**List of diagnostic fields in the ProdLoss collection**

.. list-table::
   :header-rows: 1
   :widths: 42 38 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - Loss\_<name|wc>
     - Chemical loss for a given species or family
     - molec/cm3
     - ?LOS?
   * - LossHNO3onSeaSalt\ [#M]_
     - L(HNO3) on sea salt aerosols
     - kg S/s
     -
   * - Prod\_<name|wc>
     - Chemical production for a given species or family
     - molec/cm3
     - ?PRD?
   * - ProdBCPIfromBCPO\ [#M]_
     - Production of hydrophilic BC from hydrophobic BC
     - kg
     -
   * - ProdCOfromCH4\ [#N]_
     - P(CO) from CH4
     - molec/cm3
     -
   * - ProdCOfromNMVOC\ [#N]_
     - P(CO) from NMVOCs SO3-- loss by OH
     - molec/cm3
     -
   * - ProdOCPIfromOCPO\ [#M]_
     - Production of hydrophilic OC from hydrophobic OC
     - kg
     -
   * - ProdMSAfromDMS\ [#L]_
     - P(MSA) from DMS
     - kg S/s
     -
   * - ProdNITfromHNO3uptakeOnDust\ [#K]_
     - P(NIT) from HNO3 uptake on dust aerosols
     - kg N/s
     -
   * - ProdSO2fromDMS\ [#L]_
     - Total P(SO2) from DMS
     - kg S/s
     -
   * - ProdSO2fromDMSandNO3\ [#L]_
     - P(SO2) from DMS + NO3
     - kg S/s
     -
   * - ProdSO2fromDMSandOH\ [#L]_
     - P(SO2) from DMS + OH
     - kg S/s
     -
   * - ProdSO2fromOxidationOnDust\ [#K]_
     - P(SO2) from DMS+OH on dust aerosols
     - kg S/s
     -
   * - ProdSO4fromGasPhase\ [#L]_
     - P(SO4) in gas phase
     - kg S/s
     -
   * - ProdSO4fromH2O2inCloud\ [#M]_
     - P(SO4) from aqueous oxidation of H2O2 in clouds
     - kg S/s
     -
   * - ProdSO4fromHOBrinCloud\ [#N]_
     - P(SO4) from aqueous oxidation of HOBr in clouds
     - kg S/s
     -
   * - ProdSO4fromO2inCloudMetal\ [#M]_
     - P(SO4) from aqueous oxidation of O2 from metals in cloud
     - kg S/s
     -
   * - ProdSO4fromO3inCloud\ [#M]_
     - P(SO4) from aqueous oxidation of O3 in clouds
     - kg S/s
     -
   * - ProdSO4fromO3inSeaSalt\ [#M]_
     - P(SO4) from O3 in sea salt
     - kg S/s
     -
   * - ProdSO4fromO3s\ [#M]_
     - P(SO4) from aqueous phase SO3-- loss by OH
     - kg S/s
     -
   * - ProdSO4fromSRHOBR\ [#N]_
     - P(SO4) from sulfur production rate of HOBr + O3
     - kg S/s
     -
   * - ProdSO4fromSRO3\ [#M]_
     - P(SO4) from sulfur production rate of O3
     - kg S/s
     -
   * - ProdSO4fromUptakeOfH2SO4g\ [#K]_
     - P(SO4) from H2SO4 uptake on dust aerosols
     - kg S/s
     -

.. rubric:: Notes for the ProdLoss collection

.. [#K] Only defined for fullchem simulation with aciduptake on dust.

.. [#L] Only defined for the aerosol-only simulation.

.. [#M] Defined for aerosol-only and fullchem simulations.

.. [#N] Only defined for fullchem simulations.

.. _histguide-radionuclide:

RadioNuclide
------------

The **RadioNuclide** collection contains diagnostic outputs for
radionuclide species in the GEOS-Chem TransportTracers simulation.

.. note::

   Emissions of Rn222, Be7, and Be10 species are archived to
   diagnostic output by `HEMCO diagnostics
   <https://hemco.readthedocs.io/en/latesthco-ref-guide/diagnostics.html>`_,
   and are thus not contained in this collection.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     RadioNuclide.template:    '%y4%m2%d2_%h2%n2z.nc4',
     RadioNuclide.format:      'CFIO',
     RadioNuclide.frequency:   00000100 000000
     RadioNuclide.duration:    00000100 000000
     RadioNuclide.mode:        'time-averaged'
     RadioNuclide.fields:      'PbFromRnDecay  ',
                               'RadDecay_Rn222 ',
                               'RadDecay_Pb210 ',
                               'RadDecay_Pb210s',
                               'RadDecay_Be7   ',
                               'RadDecay_Be7s  ',
                               'RadDecay_Be10  ',
                               'RadDecay_Be10s ',
   ::

**List of diagnostic fields in the RadioNuclide collection**

.. list-table::
   :header-rows: 1
   :widths: 20 65 15

   * - Diagnostic field
     - Description
     - Units
   * - PbFromRnDecay
     - Pb :sup:`210` created from radioactive decay
     - kg/s
   * - RadDecay_Be7
     - Loss of Be :sup:`7` due to radioactive decay
     - kg/s
   * - RadDecay_Be7s
     - Loss of Be :sup:`7` (produced in the stratosphere) due to
       radioactive decay
     - kg/s
   * - RadDecay_Be10
     - Loss of Be :sup:`10` due to radioactive decay
     - kg/s
   * - RadDecay_Be10s
     - Loss of Be :sup:`10` (produced in the stratosphere) due to
       radioactive decay
     - kg/s
   * - RadDecay_Pb210
     - Loss of Pb :sup:`210` due to radioactive decay
     - kg/s
   * - RadDecay_Pb210s
     - Loss of Pb :sup:`210` (produced in the stratosphere) due to
       radioactive decay
     - kg/s
   * - RadDecay_Rn222
     - Loss of Rn :sup:`222` due to readioactive decay
     - kg/s

.. _histguide-restart:

Restart
-------

The **Restart** diagnostic collection contains fields for saving out to
the GEOS-Chem Classic restart file. This type of diagnostic output is used in
all GEOS-Chem Classic simulations; therefore, we have listed Restart first in
the :file:`HISTORY.rc` files that ship with each GEOS-Chem run directory. Note
that GCHP restart files are not handled by the MAPL History component and therefore
do not appear in :file:`HISTORY.rc`.

.. note::

   The restart file will be created in the :file:`Restarts/`
   subdirectory of the run directory, not in :file:`OutputDir/`.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     Restart.filename:   './GEOSChem.Restart.%y4%m2%d2_%h2%n2z.nc4',
     Restart.frequency:  'End',
     Restart.duration:   'End',
     Restart.mode:       'instantaneous'
     Restart.fields:     'SpeciesRst_?ALL?   ',
                         'Chem_H2O2AfterChem ',
                         'Chem_SO2AfterChem  ',
                         'Chem_DryDepNitrogen',
                         'Chem_WetDepNitrogen',
                         'Chem_KPPHvalue     ',
                         'Met_DELPDRY        ',
                         'Met_PS1WET         ',
                         'Met_PS1DRY         ',
                         'Met_SPHU1          ',
                         'Met_TMPU1          ',
   ::

**List of diagnostic fields in the Restart collection**

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Diagnostic field
     - Description
     - Units
   * - Chm_DryDepNitrogen
     - Dry deposited nitrogen
     - molec/cm2/s
   * - Chm_H2O2AfterChem
     - Concentration of H2O2 after sulfate chemistry
     - v/v
   * - Chm_KPPHvalue
     - H-value for Rosenbrock solver
     - unitless
   * - Chm_SO2AfterChem
     - Concentration of SO2 after sulfate chemistry
     - v/v
   * - Chm_StatePSC
     - Polar stratospheric cloud type
     - count
   * - Chm_WetDepNitrogen
     - Wet deposited nitrogen
     - molec/cm2/s
   * - Met_DELPDRY
     - Delta-pressure across grid box (dry air)
     - hPa
   * - Met_PS1WET
     - Wet surface pressure at dt start
     - hPa
   * - Met_PS1DRY
     - Dry surface pressure at dt start
     - hPa
   * - Met_SPHU1
     - Instantaneous specific humidity at time=T
     - g kg-1
   * - Met_TMPU1
     - Instantaneous temperature at time=T
     - K
   * - SpeciesRst\_?ALL?
     - Instantaneous chemical species concentrations for use in starting subsequent GEOS-Chem simulations
     - mol/mol dry air

.. _histguide-rrtmg:

RRTMG
-----

The **RRTMG** collection contains radiative flux diagnostics computed
by the RRTMG radiative transfer model.  You can leave this collection
disabled unless your simulation uses RRTMG.

.. note::

   You may compute RRTMG diagnostic quantities at up to 3 wavelengths
   (:literal:`WL1`, :literal:`WL2`, :literal:`WL3`). Specify the
   wavelengths in this menu of the :file:`geoschem_config.yml` file:

   .. code-block:: yaml

       rrtmg_rad_transfer_model:
           activate: true
	   aod_wavelengths_in_nm:
           - 550 700 1000
	   ... etc ...

   GEOS-Chem will replace the tokens :literal:`WL1`, :literal:`WL2`,
   :literal:`WL3` in diagnostic field names with the corresponding
   wavelength. For example:

   .. code-block::

      RadAODWL1_SU
      RadAODWL2_SU
      RadAODWL3_SU

   will be saved to the :file:`GEOSChem.RRTMG.YYYYMMDD_hhmmz.nc4`
   file(s) under these names:

   .. code-block::

      RadAOD550nm_SU
      RadAOD700nm_SU
      RadAOD1000nm_SU

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

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
     RRTMG.template:    %y4%m2%d2_%h2%n2z.nc4',
     RRTMG.frequency:   00000100 000000
     RRTMG.duration:    00000100 000000
     RRTMG.mode:        time-averaged'
     RRTMG.fields:      'RadClrSkyLWSurf_?RRTMG?',
                        'RadAllSkyLWSurf_?RRTMG?',
                        'RadClrSkySWSurf_?RRTMG?',
                        'RadAllSkySWSurf_?RRTMG?',
                        'RadClrSkyLWTOA_?RRTMG? ',
                        'RadAllSkyLWTOA_?RRTMG? ',
                        'RadClrSkySWTOA_?RRTMG? ',
                        'RadAllSkySWTOA_?RRTMG? ',
                        'RadAODWL1_?RRTMG?      ',
                        'RadAsymWL1_?RRTMG?     ',
   ::

**List of diagnostic fields in the RRTMG collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 10 20

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - DynHeating
     - Dynamic heating rate in baseline simulation
     - K/day
     - ?RRTMG?
   * - DtRad
     - Temperature change due to radiative heating
     - K
     - ?RRTMG?
   * - RadAODWL1\_<name|wc>
     - Aerosol optical depth computed @ WL1
     - 1
     - ?RRTMG?
   * - RadAODWL2\_<name|wc>
     - Aerosol optical depth computed @ WL2
     - 1
     - ?RRTMG?
   * - RadAODWL3\_<name|wc>
     - Aerosol optical depth computed @ WL3
     - 1
     - ?RRTMG?
   * - RadAsymWL1\_<name|wc>
     - Asymmetry parameter computed @ WL1
     - 1
     - ?RRTMG?
   * - RadAsymWL2\_<name|wc>
     - Asymmetry parameter computed @ WL2
     - 1
     - ?RRTMG?
   * - RadAsymWL3\_<name|wc>
     - Asymmetry parameter computed @ WL3
     - 1
     - ?RRTMG?
   * - RadAllSkyLWSurf\_<name|wc>
     - All-sky longwave radiation at the surface
     - W/m2
     - ?RRTMG?
   * - RadAllSkyLWTOA\_<name|wc>
     - All-sky longwave radiation at top-of-atmosphere
     - W/m2
     - ?RRTMG?
   * - RadAllSkyLWTrop\_<name|wc>
     - All-sky longwave radiation at the tropopause
     - W/m2
     - ?RRTMG?
   * - RadAllSkySWSurf\_<name|wc>
     - All-sky shortwave radiation at the surface
     - W/m2
     - ?RRTMG?
   * - RadAllSkySWTOA\_<name|wc>
     - All-sky shortwave radiation at top-of-atmosphere
     - W/m2
     - ?RRTMG?
   * - RadAllSkySWTrop\_<name|wc>
     - All-sky shortwave radiation at the tropopause
     - W/m2
     - ?RRTMG?
   * - RadClrSkyLWSurf\_<name|wc>
     - Clear-sky longwave radiation at the surface
     - W/m2
     - ?RRTMG?
   * - RadClrSkyLWTOA\_<name|wc>
     - Clear-sky longwave radiation at top-of-atmosphere
     - W/m2
     - ?RRTMG?
   * - RadClrSkyLWTrop\_<name|wc>
     - Clear-sky longwave radiation at the tropopause
     - W/m2
     - ?RRTMG?
   * - RadClrSkySWSurf\_<name|wc>
     - Clear-sky shortwave radiation at the surface
     - W/m2
     - ?RRTMG?
   * - RadClrSkySWTOA\_<name|wc>
     - Clear-sky shortwave radiation at top-of-atmosphere
     - W/m2
     - ?RRTMG?
   * - RadClrSkySWTrop\_<name|wc>
     - Clear-sky shortwave radiation at the tropopause
     - W/m2
     - ?RRTMG?
   * - RadSSAWL1\_<name|wc>
     - Single-scattering albedo computed @ WL1
     - 1
     - ?RRTMG?
   * - RadSSAWL2\_<name|wc>
     - Single-scattering albedo computed @ WL2
     - 1
     - ?RRTMG?
   * - RadSSAWL3\_<name|wc>
     - Single-scattering albedo computed @ WL3
     - 1
     - ?RRTMG?

.. _histguide-rxnconst:

RxnConst
--------

.. attention::

   As reported in `GitHub issue #1991
   <https://github.com/geoschem/geos-chem/issues/1991>`_, some
   reaction rates are computed within the precision of the model while
   other rates are computed incorrectly.  We recommend not to use this
   diagnostic until we can provide a solution.

The **RxnConst** collection contains reaction rate constants from the
KPP solver.

.. code-block:: kconfig

     # It is best to list individual reactions to avoid using too much memory.
     # Reactions should be listed as "RxnConst_EQnnn", where nnn is the reaction
     # index as listed in KPP/fullchem/gckpp_Monitor.F90 (pad zeroes as needed).
     #
     # The units of reaction rate constants vary according to the number of reactants
     # in the reaction.
     #
     # Available for the fullchem simulations.
     RxnConst.template:   '%y4%m2%d2_%h2%n2z.nc4',
     RxnConst.frequency:  ${RUNDIR_HIST_TIME_AVG_FREQ}
     RxnConst.duration:   ${RUNDIR_HIST_TIME_AVG_DUR}
     RxnConst.mode:       'time-averaged'
     RxnConst.fields:     'RxnConst_EQ001                          ',
                          'RxnConst_EQ002                          ',
   ::

**List of diagnostic fields in the RxnRate collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 15 20

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - RxnConst_EQnnn\ [#O]_ [#P]_
     - Rate constant for KPP reaction :code:`nnn`
     -
     - ?RXN?

.. rubric:: Notes for the RxnRates collection

.. [#O] See the :file:`gckpp_Monitor.F90` file to get a numbered list of reactions.

.. [#P] Units are :literal:`{ (cm3/molec)**(nreactants-1) }/s`

.. _histguide-rxnrates:

RxnRates
--------

.. attention::

   As reported in `GitHub issue #1991
   <https://github.com/geoschem/geos-chem/issues/1991>`_, some
   reaction rates are computed within the precision of the model while
   other rates are computed incorrectly.  We recommend not to use this
   diagnostic until we can provide a solution.

The **RxnRates** collection contains reaction rates (aka equation
rates) from the chemical mechanism (as computed by the KPP-generated
solver code). For example, in the case of the NO + O\ :sub:`3` → NO\
:sub:`2` + O\ :sub:`2` reaction the returned quantity is k[NO][O\
:sub:`3`] in molec/cm\ :sup:`3`/s.

Here is a sample definition section for the RxnRates collection.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

   #
   # It is best to list individual reactions to avoid using too much memory.
   # Reactions should be listed as "RxnRate_EQnnn", where nnn is the reaction
   # index as listed in KPP/fullchem/gckpp_Monitor.F90,
   # KPP/carbon/gckpp_Monitor.F90, and KPP/Hg/gckpp_monitor.F90
   # (pad zeroes as needed)
   #
   RxnRates.template:   '%y4%m2%d2_%h2%n2z.nc4',
   RxnRates.frequency:  00000000 010000
   RxnRates.duration:   00000000 010000
   RxnRates.mode:       'time-averaged'
   RxnRates.fields:     'RxnRate_EQ001                           ',
                        'RxnRate_EQ002                           ',
   ::

**List of diagnostic fields in the RxnRate collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 15 20

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - RxnRate_EQnnn\ [#Q]_
     - Rate for KPP reaction ``nnn``
     - molec/cm3/s
     - ?RXN?

.. rubric:: Notes for the RxnRates collection

.. [#Q] See the :file:`gckpp_Monitor.F90` file to get a numbered list of reactions.

.. _histguide-satdiagn:

SatDiagn
--------

The **SatDiagn** collection contains diagnostic quantities that will
be sampled within a specified local time range.  This is to mimic the
overpass sampling times of sun-synchronus satellite instruments.

.. tip::

   Set the the hours (local time) for the averaging interval with:

   .. code-block:: kconfig

      SatDiagn.hrrange:    11.98 15.02

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     SatDiagn.template:   '%y4%m2%d2_%h2%n2z.nc4',
     SatDiagn.frequency:  00000001 000000
     SatDiagn.duration:   00000100 000000
     SatDiagn.hrrange:    11.98 15.02
     SatDiagn.mode:       'time-averaged'
     SatDiagn.fields:     'SatDiagnConc_O3      ',
                          'SatDiagnOH           ',
                          'SatDiagnRH           ',
                          'SatDiagnAirDen       ',
                          'SatDiagnBoxHeight    ',
                          'SatDiagnPEdge        ',
                          'SatDiagnTROPP        ',
                          'SatDiagnPBLHeight    ',
                          'SatDiagnPBLTop       ',
                          'SatDiagnTAir         ',
                          'SatDiagnGWETROOT     ',
                          'SatDiagnGWETTOP      ',
                          'SatDiagnPARDR        ',
                          'SatDiagnPARDF        ',
                          'SatDiagnPRECTOT      ',
                          'SatDiagnSLP          ',
                          'SatDiagnSPHU         ',
                          'SatDiagnTS           ',
                          'SatDiagnPBLTOPL      ',
                          'SatDiagnMODISLAI     ',
                          'SatDiagnWetLossLS_   ',
                          'SatDiagnWetLossConv_ ',
                          'SatDiagnJval_        ',
                          'SatDiagnJvalO3O1D    ',
                          'SatDiagnJvalO3O3P    ',
                          'SatDiagnDryDep_      ',
                          'SatDiagnDryDepVel_   ',
                          'SatDiagnOHreactivity ',
                          'SatDiagnColEmis_     ',
                          'SatDiagnSurfFlux_    ',
                          'SatDiagnProd_?PRD?   ',
                          'SatDiagnLoss_?LOS?   ',
                          'SatDiagnRxnRate_EQnnn',
   ::

**List of diagnostic fields in the SatDiagn collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 15 20

   * - Diagnostic field
     - Description
     - Units
     - Wildcards
   * - SatDiagnAirDen
     - Air density
     - molec/cm3
     -
   * - SatDiagnBoxHeight
     - Grid box height
     - m
     -
   * - SatDiagnColEmis\_<name|wc>
     - Column emissions
     - kg/m2/s
     - ?ADV?
   * - SatDiagnConc\_<name|wc>
     - Dry mixing ratio of species
     - mol/mol
     - ?ADV?
   * - SatDiagnDryDep\_<name|wc>
     - Dry deposition flux of species
     - molec/cm2/s
     - ?DRY?
   * - SatDiagnDryDepVel\_<name|wc>
     - Dry deposition velocity of species
     - cm/s
     - ?DRY?
   * - SatDiagnGWETROOT
     - Root zone soil moisture
     - 1
     -
   * - SatDiagnGWETTOP
     - Topsoil moisture (or)
     - 1
     -
   * - SatDiagnJVal\_<name|wc>
     - Photolysis rate
     - 1/s
     - ?PHO?
   * - SatDiagnJvalO3O1D
     - Photolysis rate for O3 :math:`\rightarrow` O1D
     - 1/s
     -
   * - SatDiagnJvalO3O3P
     - Photolysis rate for O3 :math:`\rightarrow` O1D
     - 1/s
     -
   * - SatDiagnLoss\_<name|wc>
     - Chemical loss of species or families
     - molec/cm3/s
     - ?LOS?
   * - SatDiagnMODISLAI
     - MODIS daily LAI
     - m2/m2
     -
   * - SatDiagnOH
     - OH number density
     - molec/cm3
     -
   * - SatDiagnOHreactivity
     - OH reactivity
     - 1/s
     -
   * - SatDiagnPARDF
     - Diffuse photosynthetically active radiation
     - W/m2
     -
   * - SatDiagnPARDR
     - Direct photosynthetically active radiation
     - W/m2
     -
   * - SatDiagnPBLHeight
     - PBL Height
     - m
     -
   * - SatDiagnPBLTop
     - PBL Top
     - m
     -
   * - SatDiagnPBLTOPL
     - PBL top height
     - level
     -
   * - SatDiagnPRECTOT
     - Total precipitation at surface
     - mm/day
     -
   * - SatDiagnProd\_<name|wc>
     - Chemical production of species or families
     - molec/cm3/s
     - ?PRD?
   * - SatDiagnRH
     - Relative humidity
     - %
     -
   * - SatDiagnRxnRate_EQnnn
     - Rate for chemical reaction ```nnn```
     - molec/cm3/s
     - ?RXN?
   * - SatDiagnSLP
     - Sea level pressure
     - hPa
     -
   * - SatDiagnSPHU
     - Specific humidity interpolated to current time
     - g H2O/kg air
     -
   * - SatDiagnSurfFlux\_<name|wc>
     - Total surface fluxes (emis - drydep) from surface to top of PBL
     - kg/m2/s
     - ?ADV?
   * - SatDiagnTAir
     - Air temperature
     - K
     -
   * - SatDiagnTROPP
     - Tropopause pressure
     - hPa
     -
   * - SatDiagnTS
     - Surface temperature at 2m
     - K
     -
   * - SatDiagnWetLossConv\_<name|wc>
     - Loss of soluble species in convective updrafts
     - kg/s
     - ?WET?
   * - SatDiagnWetLossLS\_<name|wc>
     - Loss of soluble species in large-scale precipitation
     - kg/s
     - ?WET?

.. _histguide-satdiagnedge:

SatDiagnEdge
------------

The **SatDiagn** collection contains diagnostic quantities (placed on
level edges) that will be sampled within a specified local time range.
This is to mimic the overpass sampling times of sun-synchronus
satellite instruments.

.. tip::

   Set the the hours (local time) for the averaging interval with:

   .. code-block:: kconfig

      SatDiagn.hrrange:    11.98 15.02

**Sample definition section for HISTORY.rc**

.. code-block::

     SatDiagnEdge.template:      '%y4%m2%d2_%h2%n2z.nc4',
     SatDiagnEdge.frequency:     00000001 000000
     SatDiagnEdge.duration:      00000100 000000
     SatDiagnEdge.hrrange:       11.98 15.02
     SatDiagnEdge.mode:          'time-averaged'
     SatDiagnEdge.fields:        'SatDiagnConc_PEDGE',
   ::

**List of diagnostic fields in the SatDiagnEdge collection**

.. list-table::
   :header-rows: 1
   :widths: 35 45 20

   * - Diagnostic field
     - Description
     - Units
   * - SatDiagnPEDGE
     - Pressure at grid box edges
     - hPa

.. _histguide-speciesconc:

SpeciesConc
-----------

The **SpeciesConc** diagnostic collection contains species concentrations.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     SpeciesConc.template:       '%y4%m2%d2_%h2%n2z.nc4',
     SpeciesConc.format:         'CFIO',
     SpeciesConc.frequency:      00000100 000000
     SpeciesConc.duration:       00000100 000000
     SpeciesConc.mode:           'time-averaged'
     SpeciesConc.fields:         'SpeciesConcVV_?ALL?',
                                 'SpeciesConcMND_?ALL?',
   ::

**List of diagnostic fields in the SpeciesConc collection**

.. list-table::
   :header-rows: 1
   :widths: 35 40 15 25

   * - Diagnostic field
     - Description
     - Units
     - Wildcards
   * - SpeciesConcMND\_<name|wc>
     - Species concentration
     - molec/cm3
     - can be used with all wildcards
   * - SpeciesConcVV\_<name|wc>
     - Species concentration
     - mol/mol dry air
     - can be used with all wildcards

.. _histguide-statechm:

StateChm
--------

The '''StateChm ''' collection contains quantities from State_Chm,
the Chemistry State object (other than the species concentrations, which
are stored in the SpeciesConc collection).

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     StateChm.template:    '%y4%m2%d2_%h2%n2z.nc4',
     StateChm.frequency:   00000100 000000
     StateChm.duration:    00000100 000000
     StateChm.mode:        'time-averaged'
     StateChm.fields:      'Chem_phSav      ', 'GIGCchem',
                           'Chem_HplusSav   ', 'GIGCchem',
                           'Chem_WaterSav   ', 'GIGCchem',
                           'Chem_SulRatSav  ', 'GIGCchem',
                           'Chem_NaRatSav   ', 'GIGCchem',
                           'Chem_AcidPurSav ', 'GIGCchem',
                           'Chem_BiSulSav   ', 'GIGCchem',
                           'Chem_pHCloud    ', 'GIGCchem',
                           'Chem_SSAlk',    ', 'GIGCchem',
                           'Chem_HSO3AQ     ', 'GIGCchem',
                           'Chem_SO3AQ      ', 'GIGCchem',
                           'Chem_fupdateHOBr', 'GIGCchem',
   ::

**List of diagnostic fields in the StateChm collection**

.. list-table::
   :header-rows: 1
   :widths: 30 50 20

   * - Diagnostic field
     - Description
     - Units
   * - Chm_AcidPurSav
     - ATE Acidpur concentration
     - M
   * - Chm_BiSulSav
     - ATE Bisulfate general acid concentration
     - M
   * - Chm_fupdateHOBr
     - Correction factor for HOBr removal by SO2 grid box (wet air)
     - mol/L
   * - Chm_HplusSav
     - ATE H+ concentration
     - M
   * - Chm_HSO3AQ
     - Cloud bisulfite concentration
     - mol/L
   * - Chm_NaRatSav
     - ATE Na+ concentration
     - M
   * - Chm_phCloud
     - Cloud pH
     - 1
   * - Chm_phSav
     - Aerosol pH
     - 1
   * - Chm_SO3AQ
     - Cloud sulfite concentration
     - mol/L
   * - Chm_SulRatSav
     - Sulfate concentration
     - M
   * - Chm_SSalk
     - Sea salt alkalinity
     -
   * - Chm_WaterSav
     - ATE Aerosol water
     - :math:`{\mu}g/m3`

.. _histguide-statemet:

StateMet
--------

The **StateMet** collection contains met fields and other derived
quantities that are carried in the State_Met object.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     StateMet.template:    '%y4%m2%d2_%h2%n2z.nc4',
     StateMet.frequency:   00000100 000000
     StateMet.duration:    00000100 000000
     StateMet.mode:        'time-averaged'
     StateMet.fields:      'Met_AD         ',
                           'Met_AIRDEN     ',
                           'Met_AIRVOL     ',
                           'Met_ALBD       ',
                           'Met_AREAM2     ',
                           'Met_AVGW       ',
                           'Met_BXHEIGHT   ',
                           'Met_ChemGridLev',
                           'Met_CLDF       ',
                           'Met_CLDFRC     ',
                           'Met_CLDTOPS    ',
                           'Met_DELP       ',
                           'Met_DQRCU      ',
                           'Met_DQRLSAN    ',
                           'Met_DTRAIN     ',
                           'Met_EFLUX      ',
                           'Met_FRCLND     ',
                           'Met_FRLAKE     ',
                           'Met_FRLAND     ',
                           'Met_FRLANDIC   ',
                           'Met_FROCEAN    ',
                           'Met_FRSEAICE   ',
                           'Met_FRSNO      ',
                           'Met_GWETROOT   ',
                           'Met_GWETTOP    ',
                           'Met_HFLUX      ',
                           'Met_LAI        ',
                           'Met_LWI        ',
                           'Met_PARDR      ',
                           'Met_PARDF      ',
                           'Met_PBLTOPL    ',
                           'Met_PBLH       ',
                           'Met_PHIS       ',
                           'Met_PMID       ',
                           'Met_PMIDDRY    ',
                           'Met_PRECANV    ',
                           'Met_PRECCON    ',
                           'Met_PRECLSC    ',
                           'Met_PRECTOT    ',
                           'Met_PS1DRY     ',
                           'Met_PS1WET     ',
                           'Met_PS2DRY     ',
                           'Met_PS2WET     ',
                           'Met_PSC2WET    ',
                           'Met_PSC2DRY    ',
                           'Met_QI         ',
                           'Met_QL         ',
                           'Met_OMEGA      ',
                           'Met_OPTD       ',
                           'Met_REEVAPCN   ',
                           'Met_REEVAPLS   ',
                           'Met_SLP        ',
                           'Met_SNODP      ',
                           'Met_SNOMAS     ',
                           'Met_SPHU       ',
                           'Met_SPHU1      ',
                           'Met_SPHU2      ',
                           'Met_SUNCOS     ',
                           'Met_SUNCOSmid  ',
                           'Met_SWGDN      ',
                           'Met_T          ',
                           'Met_TAUCLI     ',
                           'Met_TAUCLW     ',
                           'Met_THETA      ',
                           'Met_TMPU1      ',
                           'Met_TMPU2      ',
                           'Met_TO3        ',
                           'Met_TropHt     ',
                           'Met_TropLev    ',
                           'Met_TropP      ',
                           'Met_TS         ',
                           'Met_TSKIN      ',
                           'Met_TV         ',
                           'Met_U          ',
                           'Met_U10M       ',
                           'Met_USTAR      ',
                           'Met_UVALBEDO   ',
                           'Met_V          ',
                           'Met_V10M       ',
                           'Met_Z0         ',
   ::

**List of diagnostic fields in the StateMet collection**

.. list-table::
   :header-rows: 1
   :widths: 20 50 20

   * - Diagnostic field
     - Description
     - Units
   * - Met_AD
     - Dry air mass
     - kg
   * - Met_AIRDEN
     - Dry air density
     - kg/m3
   * - Met_AIRVOL
     - Grid box volume, dry air
     - m3
   * - Met_ALBD
     - Surface albedo
     - 1
   * - Met_AREAM2
     - Grid box area
     - m2
   * - Met_AVGW
     - Water vapor volume mixing ratio
     - vol H2O/vol dry air
   * - Met_BXHEIGHT
     - Grid box height
     - m
   * - Met_ChemGridLev
     - Chemistry grid level
     - 1
   * - Met_CLDF
     - 3-D cloud fraction
     -
   * - Met_CLDFRC
     - Column cloud fraction
     - 1
   * - Met_CLDTOPS
     - Maximum cloud top height
     - 1
   * - Met_DELP
     - Delta-pressure between top and bottom edges of grid box (wet air)
     - hPa
   * - Met_DQRCU
     - Convective precipitation production rate (dry air)
     - kg/kg/s
   * - Met_DTRAIN
     - Detrainment flux
     - kg/m2/s
   * - Met_EFLUX
     - Latent heat flux
     - W/m2
   * - Met_FRCLND
     - Olson land fraction
     - 1
   * - Met_FRLAKE
     - Fraction of grid box covered by lakes
     - 1
   * - Met_FRLAND
     - Fraction of grid box covered by land
     - 1
   * - Met_FRLANDIC
     - Fraction of grid box covered by land ice
     - 1
   * - Met_FROCEAN
     - Fraction of grid box covered by ocean
     - 1
   * - Met_FRSEAICE
     - Fraction of grid box covered by sea ice
     - 1
   * - Met_FRSNO
     - Fraction of grid box covered by snow
     - 1
   * - Met_GWETROOT
     - Root soil moisture
     - 1
   * - Met_GWETTOP
     - Topsoil moisture
     - 1
   * - Met_HFLUX
     - Sensible heat flux
     - W/m2
   * - Met_LAI
     - Leaf area index from met field archive
     - m2/m2
   * - Met_LWI
     - Land-water-ice indices
     - 1
   * - Met_PARDF
     - Diffuse photosynthetically active radiation
     - W/m2
   * - Met_PARDR
     - Diffuse photosynthetically active radiation
     - W/m2
   * - Met_PBLTOPL
     - PBL top layer
     - 1
   * - Met_PBLH
     - PBL height
     - m
   * - Met_PHIS
     - Surface geopotential height
     - m
   * - Met_PMID
     - Pressure at midpoint of model layers, defined as arithmetic average of edge pressures (wet air)
     - hPa
   * - Met_PMIDDRY
     - Pressure at midpoint of model layers, defined as arithmetic average of edge pressures (dry air)
     - hPa
   * - Met_PRECANV
     - Anvil precipitation (at surface)
     - mm/day
   * - Met_PRECCON
     - Convective precipitation (at surface)
     - mm/day
   * - Met_PRECLSC
     - Large-scale precipitation (at surface)
     - mm/day
   * - Met_PRECTOT
     - Total precipitation (at surface)
     - mm/day
   * - Met_PS1DRY
     - Instantaneous surface pressure at start of 3-hr met field interval (dry air)
     - hPa
   * - Met_PS2DRY
     - Instantaneous surface pressure at end of 3-hr met field interval (dry air)
     - hPa
   * - Met_PSC2DRY
     - Surface pressure interpolated to current time (dry air)
     - hPa
   * - Met_PS1WET
     - Instantaneous surface pressure at start of 3-hr met field interval (wet air)
     - hPa
   * - Met_PS2WET
     - Instantaneous surface pressure at end of 3-hr met field interval (wet air)
     - hPa
   * - Met_PSC2WET
     - Surface pressure interpolated to current time (wet air)
     - hPa
   * - Met_QI
     - Ice mixing ratio (dry air)
     - kg/kg dry air
   * - Met_QL
     - Liquid water mixing ratio (dry air)
     - kg/kg dry air
   * - Met_OMEGA
     - Updraft velocity
     - Pa/s
   * - Met_OPTD
     - Visible optical depth
     - 1
   * - Met_REEVAPCN
     - Evaporation of convective precipitation (dry air)
     - kg/kg/s
   * - Met_REEVAPLS
     - Evaporation of large-scale + anvil precipitation (dry air)
     - kg/kg/s
   * - Met_SLP
     - Sea level pressure
     - hPa
   * - Met_SNODP
     - Snow depth
     - m
   * - Met_SNOMAS
     - Snow mass
     - kg/m2
   * - Met_SPHU1
     - Instantaneous specific humidity at start of 3 hr met field interval (wet air)
     - kg/kg
   * - Met_SPHU2
     - Instantaneous specific humidity at end of 3-hr met field interval (wet air)
     - kg/kg
   * - Met_SPHU
     - Specific humidity interpolated to current time (wet air)
     - g H2O/kg air
   * - Met_SUNCOS
     - Cosine of solar zenith angle at current time
     - 1
   * - Met_SUNCOSMID
     - Cosine of solar zenith angle at midpoint of chemistry timestep
     - 1
   * - Met_SWGDN
     - Incident shortwave radiation at ground
     - W/m2
   * - Met_TAUCLI
     - Visible optical depth of ice clouds
     - 1
   * - Met_TAUCLW
     - Visible optical depth of water clouds
     - 1
   * - Met_THETA
     - Potential temperature
     - K
   * - Met_TMPU1
     - Instantaneous temperature at start of 3-hr met field interval
     - K
   * - Met_TMPU2
     - Instantaneous temperature at end of 3-hr met field interval
     - K
   * - Met_T
     - Temperature interpolated to current time
     - K
   * - Met_TO3
     - Total overhead ozone column
     - Dobsons
   * - Met_TropHt
     - Tropopause height
     - u
   * - Met_TropLev
     - Tropopause level
     - 1
   * - Met_TROPP
     - Tropopause pressure
     - hPa
   * - Met_TS
     - Surface temperature
     - K
   * - Met_TSKIN
     - Surface skin temperature
     - K
   * - Met_U
     - East-west ccomponent of wind
     - m/s
   * - Met_U10M
     - East-west component of wind at 10 m height above surface
     - m/s
   * - Met_USTAR
     - Friction velocity
     - m/s
   * - Met_UVALBEDO
     - Ultraviolet surface albedo
     - 1
   * - Met_V
     - North-south ccomponent of wind
     - m/s
   * - Met_V10M
     - North-south component of wind at 10 m height above surface
     - m/s
   * - Met_Z0
     - Surface roughness height
     - m
   * - FracOfTimeInTrop
     - Fraction of time spent in the troposphere
     - 1

.. _histguide-statemetlevedge:

StateMetLevEdge
---------------

.. attention::

   The **LevelEdgeDiags** collection was renamed to
   **StateMetLevEdge** in GEOS-Chem 14.7.0.

The **StateMetLevEdge** collection contains diagnostics for quantities
(mostly met fields) that are defined on the vertical edges of each grid
box. According to the COARDS convention, all of the data variables in a
netCDF file must be defined with the same vertical dimension.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     LevelEdgeDiags.template:    '%y4%m2%d2_%h2%n2z.nc4',
     LevelEdgeDiags.frequency:   00000100 000000
     LevelEdgeDiags.duration:    00000100 000000
     LevelEdgeDiags.mode:        'time-averaged'
     LevelEdgeDiags.fields:      'Met_CMFMC   ',
                                 'Met_PEDGE   ',
                                 'Met_PEDGEDRY',
                                 'Met_PFICU   ',
                                 'Met_PFILSAN ',
                                 'Met_PFLCU   ',
                                 'Met_PFLLSAN ',
   ::

**List of diagnostic fields in the LevelEdgeDiags collection**


.. list-table::
   :header-rows: 1
   :widths: 20 50 15

   * - Diagnostic field
     - Description
     - Units
   * - Met_CMFMC
     - Upward moist convective mass flux
     - kg/m2/s
   * - Met_PEDGE
     - Surface pressure at level edges (based on moist air)
     - hPa
   * - Met_PEDGEDRY
     - Surface pressure at level edges (based on dry air)
     - hPa
   * - Met_PFICU
     - 3-D flux of ice convective precipitation
     - kg/m2/s
   * - Met_PFILSAN
     - 3-D flux of ice non-convective precipitation
     - kg/m2/s
   * - Met_PFLCU
     - 3-D flux of liquid convective precipitation
     - kg/m2/s
   * - Met_PFLLSAN
     - 3-D flux of liquid non-convective precipitation
     - kg/m2/s

.. _histguide-stratbm:

StratBM
-------

The **StratBM** collection contains diagnostic fields for GEOS-Chem
10-year stratospheric benchmark simulations.  Unless you are involved
with benchmarking GEOS-Chem, you may leave this collection
deactivated.

**Sample definition section for the StratBM collection**

.. code-block:: kconfig

     StratBM.template:       '%y4%m2%d2_%h2%n2z.nc4',
     StratBM.frequency:      00000000 010000
     StratBM.duration:       00000001 000000
     StratBM.mode:           'time-averaged'
     StratBM.fields:         'SpeciesConcVV_NO2                 ',
                             'SpeciesConcVV_O3                  ',
                             'SpeciesConcVV_ClO                 ',
                             'Met_PSC2WET                       ',
                             'Met_BXHEIGHT                      ',
                             'Met_AIRDEN                        ',
                             'Met_AD                            ',
   ::

**List of diagnostic fields in the StateMet collection**

.. list-table::
   :header-rows: 1
   :widths: 25 50 25

   * - Diagnostic field
     - Description
     - Units
   * - Met_AD
     - Dry air mass
     - kg
   * - Met_AIRDEN
     - Dry air density
     - kg/m3
   * - Met_BXHEIGHT
     - Grid box height
     - m
   * - Met_PSC2WET
     - Surface pressure interpolated to current time (wet air)
     - hPa
   * - SpeciesConcVV_ClO
     - ClO concentration
     - mol/mol dry air
   * - SpeciesConcVV_NO2
     - NO2 concentration
     - mol/mol dry air
   * - SpeciesConcVV_O3
     - O3 concentration
     - mol/mol dry air

.. _histguide-tomas:

Tomas
-----

The **TOMAS** collection contains diagnostic fields for fullchem simulations with TOMAS aerosol microphysics.

.. code-block:: kconfig

     Tomas.template:       '%y4%m2%d2_%h2%n2z.nc4',
     Tomas.format:         'CFIO',
     Tomas.timestampStart: .true.
     Tomas.monthly:        0
     Tomas.frequency:      010000
     Tomas.duration:       010000
     Tomas.mode:           'time-averaged'
     Tomas.fields:         'TomasH2SO4mass_?TOMASBIN?         ',
                           #----------------------------------------------
                           # NOTE: for GEOS-Chem Classic you can use the
			   # ?TOMASBIN? wildcard.  For GCHP you will need
			   # to list each diagnostic field individually
			   # such as is shown below:
                           #'TomasH2SO4mass_bin01             ',
                           #'TomasH2SO4mass_bin02             ',
                           #'TomasH2SO4mass_bin03             ',
                           #'TomasH2SO4mass_bin04             ',
                           #'TomasH2SO4mass_bin05             ',
                           #'TomasH2SO4mass_bin06             ',
                           #'TomasH2SO4mass_bin07             ',
                           #'TomasH2SO4mass_bin08             ',
                           #'TomasH2SO4mass_bin09             ',
                           #'TomasH2SO4mass_bin10             ',
                           #'TomasH2SO4mass_bin11             ',
                           #'TomasH2SO4mass_bin12             ',
                           #'TomasH2SO4mass_bin13             ',
                           #'TomasH2SO4mass_bin14             ',
                           #'TomasH2SO4mass_bin15             ',
                           #----------------------------------------------
                           'TomasH2SO4number_?TOMASBIN?      ',
                           'TomasCOAGmass_?TOMASBIN          ',
                           'TomasCOAGnumber_?TOMASBIN?       ',
                           'TomasNUCRATEFN                   ',
                           'TomasNUCLmass_?TOMASBIN?         ',
                           'TomasNUCLnumber_?TOMASBIN?       ',
                           'TomasNUCRATEnumber_?TOMASBIN?    ',
                           'TomasAQOXmass_?TOMASBIN?         ',
                           'TomasAQOXnumber_?TOMASBIN?       ',
                           'TomasMNFIXmass_?TOMASBIN?        ',
                           'TomasMNFIXnumber_?TOMASBIN?      ',
                           'TomasMNFIXh2so4mass_?TOMASBIN?   ',
                           'TomasMNFIXh2so4number_?TOMASBIN? ',
                           'TomasMNFIXcoagmass_?TOMASBIN?    ',
                           'TomasMNFIXcoagnumber_?TOMASBIN?  ',
                           'TomasMNFIXaqoxmass_?TOMASBIN?    ',
                           'TomasMNFIXaqoxnumber_?TOMASBIN?  ',
                           'TomasMNFIXezwat1number_?TOMASBIN?',
                           'TomasMNFIXezwat2mass_?TOMASBIN?  ',
                           'TomasMNFIXezwat2number_?TOMASBIN?',
                           'TomasMNFIXezwat3mass_?TOMASBIN?  ',
                           'TomasMNFIXezwat3number_?TOMASBIN?',
                           'TomasMNFIXcheck1mass_?TOMASBIN?  ',
                           'TomasMNFIXcheck1number_?TOMASBIN?',
                           'TomasMNFIXcheck2mass_?TOMASBIN?  ',
                           'TomasMNFIXcheck2number_?TOMASBIN?',
                           'TomasMNFIXcheck3mass_?TOMASBIN?  ',
                           'TomasMNFIXcheck3number_?TOMASBIN?',
                           'TomasSOAmass_?TOMASBIN?          ',
                           'TomasSOAnumber_?TOMASBIN?        ',
   ::

**List of diagnostic fields for the Tomas collection**

.. list-table::
   :header-rows: 1
   :widths: 30 40 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - TomasAQOXmass_<name|wc>
     - TomasAQOX mass rate
     - kg/kg/s
     - [#R]_
   * - TomasAQOXnumber_<name|wc>
     - TomasAQOX number rate
     - #/kg/s
     - [#R]_
   * - TomasCOAGmass_<name|wc>
     - TOMASCOAG mass rate
     - kg/kg/s
     - [#R]_
   * - TomasCOAGnumber_<name|wc>
     - TomasCOAG number rate
     - #/kg/s
     - [#R]_
   * - TomasH2SO4mass_<name|wc>
     - TomasH2SO4 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasH2SO4number_<name|wc>
     - TomasH2SO4 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIX
     - Tomas error rate
     - 1
     - [#R]_
   * - TomasMNFIXmass_<name|wc>
     - TomasMNFIX mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXnumber_<name|wc>
     - TomasMNFIX number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXaqoxmass_<name|wc>
     - TOMASMNFIXAQOX mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXaqoxnumber_<name|wc>
     - TOMASMNFIXAQOX number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXcoagmass_<name|wc>
     - TomasMNFIXCOAG mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXcoagnumber_<name|wc>
     - TomasMNFIXCOAG number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXcheck1mass_<name|wc>
     - TOMASMNFIXCHECK1 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXcheck1number_<name|wc>
     - TOMASMNFIXCHECK1 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXcheck2mass_<name|wc>
     - TOMASMNFIXCHECK2 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXcheck2number_<name|wc>
     - TOMASMNFIXCHECK2 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXcheck3mass_<name|wc>
     - TOMASMNFIXCHECK3 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXcheck3number_<name|wc>
     - TOMASMNFIXCHECK3 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXezwat1mass_<name|wc>
     - TOMASMNFIXEZWAT1 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXezwat1number_<name|wc>
     - TOMASMNFIXEZWAT1 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXezwat2mass_<name|wc>
     - TOMASMNFIXEZWAT2 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXezwat2number_<name|wc>
     - TOMASMNFIXEZWAT2 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXezwat3mass_<name|wc>
     - TOMASMNFIXEZWAT3 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXezwat3number_<name|wc>
     - TOMASMNFIXEZWAT3 number rate
     - #/kg/s
     - [#R]_
   * - TomasMNFIXh2so4mass_<name|wc>
     - TomasMNFIXH2SO4 mass rate
     - kg/kg/s
     - [#R]_
   * - TomasMNFIXh2so4number_<name|wc>
     - TomasMNFIXH2SO4 number rate
     - #/kg/s
     - [#R]_
   * - TomasNUCL
     - Tomas nucleation rate
     - 1
     - [#R]_
   * - TomasNUCLmass_<name|wc>
     - TomasNUCL mass rate
     - kg/kg/s
     - [#R]_
   * - TomasNUCLnumber_<name|wc>
     - TomasNUCL number rate
     - #/kg/s
     - [#R]_
   * - TomasSOA
     - TomasSOA rate
     - 1
     - [#R]_
   * - TomasSOAmass_<name|wc>
     - TomasSOA mass rate
     - kg/kg/s
     - [#R]_
   * - TomasSOAnumber_<name|wc>
     - TomasSOA number rate
     - #/kg/s
     - [#R]_

.. rubric:: Notes for the Tomas collection

.. [#R] This diagnostic field can use the ?TOMASBIN? wildcard (for GEOS-Chem Classic only).

.. _histguide-uvflux:

UVFlux
------

The **UVFlux** diagnostic contains diffuse, direct, and net UV fluxes
at each of the photolysis wavelength bins.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     UVFlux.template:    '%y4%m2%d2_%h2%n2z.nc4',
     UVFlux.frequency:   00000100 000000
     UVFlux.duration:    00000100 000000
     UVFlux.mode:        'time-averaged'
     UVFlux.template:    'UVFluxDiffuse_?UVFLX?',
                         'UVFluxDirect_?UVFLX? ',
                         'UVFluxNet_?UVFLX?    ',
   ::

**List of diagnostic fields in the UvFlux collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcards
   * - UVFluxDiffuse_<name|wc>
     - Diffuse UV flux in wavelength bin
     - W/m2
     - ?UVFLX?
   * - UVFluxDirect_<name|wc>
     - Direct UV flux in wavelength bin
     - W/m2
     - ?UVFLX?
   * - UVFluxNet_<name|wc>
     - Net UV flux in wavelength bin
     - W/m2
     - ?UVFLX?

.. _histguide-wetlossconv:

WetLossConv
-----------

The **WetLossConv** collection contains diagnostics fluxes of soluble
species lost to wet scavenging in convective updrafts.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     WetLossConv.template:    '%y4%m2%d2_%h2%n2z.nc4',
     WetLossConv.frequency:   00000100 000000
     WetLossConv.duration:    00000100 000000
     WetLossConv.mode:        'time-averaged'
     WetLossConv.fields:      'WetLossConv_?WET?    ',
                              'WetLossConvFrac_?WET?',
   ::

**List of diagnostic fields in the WetLossConv collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - WetLossConv_<name|wc>
     - Loss of soluble species scavenged by cloud updrafts in moist convection
     - kg/s
     - ?WET?
   * - WetLossConvFrac_<name|wc>
     - Fraction of species scavenged by cloud updrafts in moist convection
     - 1
     - ?WET?

.. _histguide-wetlossls:

WetLossLS
---------

The **WetLossLS** collection contains diagnostics fluxes of soluble
species lost to rainout and washout in large-scale wet deposition.

**Sample definition section for HISTORY.rc**

.. code-block:: kconfig

     WetLossLS.template:     '%y4%m2%d2_%h2%n2z.nc4',
     WetLossLS.frequency:    00000100 000000
     WetLossLS.duration:     00000100 000000
     WetLossLS.mode:         'time-averaged'
     WetLossLS.fields:       'WetLossLS_?WET?',
   ::

**List of diagnostic fields in the WetLossLS collection**

.. list-table::
   :header-rows: 1
   :widths: 30 45 15 15

   * - Diagnostic field
     - Description
     - Units
     - Wildcard
   * - WetLossLS_<name|wc>
     - Loss of soluble species in large-scale precipitation
     - kg/s
     - ?WET?

.. _histguide-add-new-diags:

==============================
Adding new History diagnostics
==============================

To add your own diagnostics we recommend that you find a similar
existing diagnostic and use its implementation as a template. Most of
the work is done in :file:`Headers/state_diag_mod.F90`. Briefly, the
following updates to that file are essential for adding in your own
netCDF diagnostics:

#. Declare diagnostic array at top of module. |br|
   |br|

#. Set diagnostic array pointer to null in :code:`Zero_State_Diag`
   subroutine. |br|
   |br|

#. Create a section in :code:`Init_State_Diag` subroutine to allocate
   and register the array. |br|
   |br|

#. Deallocate the diagnostic array in subroutine
   :code:`Cleanup_State_Diag`. |br|
   |br|

#. Add an :code:`IF` block for the diagnostic within subroutine
   :code:`Get_Metadata_State_Diag` to define its metadata, making sure
   to list the diagnostic name with all capital letters.

Good diagnostics to use as templates are :code:`SpeciesConcVV` for
3-dimensional arrays that are for all species and :code:`DryDepVel` for
2-dimensional arrays that are for a subset of species. If your
diagnostic is not per species then :code:`AODDust` is a good
diagnostic to look at. Search the file
:code:`Headers/state_diag_mod.F90` for any of these strings to find
all instances of code related to their implementation.

Note that information about the dimensions and species collection the
diagnostic will include must be specified when allocating the array in
:code:`Init_State_Diag` and in :code:`Get_Metadata_State_Diag`. In the
latter subroutine the :code:`Rank` is the integer number of dimensions
of the diagnostic (not including species) and the :code:`TagID`
string, if any, specifies the species collection to output the
diagnostic per. Each :code:`TagID` is defined in subroutine
Get_TagInfo. Each TagID string can also be used as a wildcard within
:file:`HISTORY.rc` to simplify diagnostic name specification (for
GEOS-Chem Classic only).

Once you have implemented your diagnostic in
:code:`Headers/state_diag_mod.F90`, try adding it to HISTORY.rc and
running. You should get your diagnostic output in the netCDF output
file as all zeros. The next step is to populate the array with
whatever value you want to output. You should do this by passing the
:code:`State_Diag` array to the location where you want to set the
values. Then write code to fill the array. A simple test of your
understanding is to initially set values to a constant other than zero
and see if the output matches what you set the arrays to.

For additional help implementing your own GEOS-Chem diagnostics please
contact the GEOS-Chem Support Team.
