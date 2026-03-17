.. |br| raw:: html

   <br />

.. _tomas-guide:

##########################
TOMAS aerosol microphysics
##########################

This Guide describes the TOMAS aerosol microphysics option in GEOS-Chem.
TOMAS is one of two aerosol microphysics packages being incorporated into
GEOS-Chem, the other being :ref:`apmguide`.

========
Overview
========

.. list-table:: TOMAS authors and collaborators (in alphabetical order)
   :header-rows: 1
   :align: center

   * - Author or Collaborator
     - Institution
   * - Peter Adams
     - Carnegie-Mellon University, USA
   * - Betty Croft
     - Washington University in St. Louis
   * - Salvatore Farina
     - Colorado State University, USA (formerly)
   * - Jack Kodros
     - Colorado State University, USA (formerly)
   * - Marguerite Marks
     - Carnegie-Mellon University, USA (formerly)
   * - Jeffrey Pierce
     - Colorado State University, USA
   * - Win Trivitayanurak
     - Chulalongkorn University, Thailand
   * - Dan Westervelt
     - Carnegie-Mellon University, USA (formerly)

The TwO-Moment Aerosol Sectional (TOMAS) microphysics package was
developed for implementation into GEOS-Chem at Carnegie-Mellon
University (:cite:t:`Adams_and_Seinfeld_2002`,
:cite:t:`Trivitayanurak_et_al._2008`).  Using a moving sectional and
moment-based approach, TOMAS tracks two independent moments
(number and mass) of the aerosol size distribution for a number of
discrete size bins. It also contains codes to simulate nucleation,
condensation, and coagulation processes. The aerosol species
considered with high size resolution are sulfate, sea-salt, OC, EC,
and dust. An advantage of TOMAS is the full size resolution for all
chemical species and the conservation of aerosol number, the latter of
which allows one to construct aerosol and CCN number budgets that will
balance.

:cite:t:`Croft_et_al._2024` have made TOMAS compatible with `GEOS-Chem
High Performance (aka GCHP) <https://gchp.readthedocs.io>`_.  From the
abstract:

   Global modeling of aerosol-particle number and size is important
   for understanding aerosol effects on Earth's climate and air
   quality. Fine-resolution global models are desirable for
   representing nonlinear aerosol-microphysical processes, their
   nonlinear interactions with dynamics and chemistry, and spatial
   heterogeneity. However, aerosol-microphysical simulations are
   computationally demanding, which can limit the achievable global
   horizontal resolution. Here, we present the first coupling of the
   TwO-Moment Aerosol Sectional (TOMAS) microphysics scheme with the
   High-Performance configuration of the GEOS-Chem model of
   atmospheric composition (GCHP), a coupling termed
   GCHP-TOMAS. GCHP's architecture allows massively parallel
   GCHP-TOMAS simulations including on the cloud, using hundreds of
   computing cores, faster runtimes, more memory, and finer global
   horizontal resolution (e.g., 25 km × 25 km, 7.8 × 10\ :sup:`5`
   model columns) versus the previous single-node capability of
   GEOS-Chem-TOMAS (tens of cores, 200 km × 250 km, 1.3 × 10\ :sup:`4`
   model columns). GCHP-TOMAS runtimes have near-ideal scalability
   with computing-core number. Simulated global-mean number
   concentrations increase (dominated by free-tropospheric over-ocean
   sub-10-nm-diameter particles) toward finer GCHP-TOMAS horizontal
   resolution. Increasing the horizontal resolution from 200 km ×
   200–50 km × 50 km increases the global monthly mean
   free-tropospheric total particle number by 18.5%, and over-ocean
   sub-10-nm-diameter particles by 39.8% at 4-km altitude. With a
   cascade of contributing factors, free-tropospheric
   particle-precursor concentrations increase (32.6% at 4-km altitude)
   with resolution, promoting new-particle formation and growth that
   outweigh coagulation changes. These nonlinear effects have the
   potential to revise current understanding of processes controlling
   global aerosol number and aerosol impacts on Earth's climate and
   air quality.

=================
TOMAS simulations
=================

Size Resolution
---------------

The different TOMAS simulations have the following characteristics:

.. list-table::
   :header-rows: 1

   * - Simulation
     - Size Resolution
     - Currently supported?
   * - TOMAS12 |br|
       (12 bins)
     - All 7 chemical species have size resolution ranging from 10 nm
       to 1 µm, spanned by 10 logarithmically spaced (mass quadrupling)
       bins and two supermicron bins. |br|
       |br|
       Coarser than TOMAS30, with improved computation time.
     - No
   * - TOMAS15 |br|
       (15 bins)
     - Same as TOMAS12 with 3 additional (mass quadrupling) sub-10 nm
       bins with a lower limit of ~2 nm. |br|
       |br|

       Analogous to TOMAS40 with improved computation time.
     - Yes
   * - TOMAS30 |br|
       (30 bins)
     - All 7 chemical species have size resolution ranging from 10 nm
       to 10 µm, spanned by 30 logarithmically spaced (mass doubling)
       bins.
     - No
   * - TOMAS40 |br|
       (40 bins)
     - Same as TOMAS30 with 10 additional (mass doubling) sub-10 nm
       bins with a lower limit of ~1 nm.
     - Yes

In the past, all TOMAS versions were supported in GEOS-Chem.  At
present, only the TOMAS15 and TOMAS40 simulations are supported.  The
source code for the TOMAS12 and TOMAS30 simulations is still intact,
and these simulations could be restored in the future if there is demand.

Species
-------

TOMAS adds the following species to the standard :ref:`fullchem
simulation <fullchem-sim>`:

.. list-table::
   :header-rows: 1
   :align: center

   * - TOMAS15
     - TOMAS40
     - Description
   * - H2SO4
     - H2SO4
     - Sulfuric acid
   * - NK01 ... NK15
     - NK01 ... NK40
     - Number
   * - SF01 ... SF15
     - SF01 ... SF40
     - Size-resolved sulfate
   * - SS01 ... SS15
     - SS01 ... SS40
     - Size-resolved sea salt
   * - ECOB01 ... ECOB15
     - ECOB01 ... ECOB40
     - Size-resolved hydrophilic elemental carbon
   * - ECIL01 ... ECIL15
     - ECIL01 ... ECIL40
     - Size-resolved hydrophobic elemental carbon
   * - OCOB01 ... OCOB15
     - OCOB01 ... OCOB40
     - Size-resolved hydrophilic organic carbon
   * - OCIL01 ... OCIL15
     - OCIL01 ... OCIL40
     - Size-resolved hydrophobic organic carbon
   * - DUST01 ... DUST15
     - DUST01 ... DUST40
     - Size-resolved mineral dust
   * - AW01 ... AW15
     - AW01 ... AW40
     - Size-resolved aerosol water

Nucleation
----------

The choice of nucleation theory is selected in the header section of
:file:`GeosCore/tomas_mod.F90`. The available options are:

#. Binary homogeneous nucleation (:cite:t:`Vehkamaki_et_al._2002`) |br|
   |br|

#. Ternary homogeneous nucleation (:cite:t:`Napari_et_al._2002`) --- the
   ternary nucleation rate is typically scaled by a globally uniform
   tuning factor of 10 \ :sup:`-4` or 10\ :sup:`-5` |br|
   |br|

#. Ion-mediated nucleation (:cite:t:`Yu_2010a`) |br|
   |br|

#. Activation nucleation (:cite:t:`Kulmala_et_al._2006`)

In TOMAS12 and TOMAS30, nucleated particles follow the Kerminen
approximation to grow to the smallest size bin. This has a tendency to
overpredict the number of particles in the smallest bins of those models.
See :cite:t:`Lee_et_al._2013` for more details.

Condensation
------------

*(Documentation forthcoming)*

Coagulation
-----------

*(Documentation forthcoming)*

Biomass Burning Subgrid Coagulation Switch
------------------------------------------

:cite:t:`Ramnarine_et_al._2018` created code that allows the emitted
size distribution in the model to be a function of properties that
include the mean emissions rate per fire in the grid box. From the paper:

   This parameterization, based on :cite:t:`Sakamoto_et_al._2016`,
   estimates the amount of near-source, sub-grid scale coagulation
   happening in a biomass burning plume. It can be turned on or
   off. When on, the default assumption is that each smoke plume is
   completely separate from the others (i.e., there is no overlap of
   the plumes). There is also an option for all smoke plumes in a grid
   box to overlap completely. When being used, this parameterization
   changes the median diameter and modal width of biomass burning
   emissions to account for coagulation.

==========
Validation
==========

Figure 1 in :cite:t:`Kodros_and_Pierce_2017` documents the performance
of GEOS-Chem-Classic-TOMAS for predicting aerosol number (N10 = number
of particles larger than 10 nm, etc.) against measurements at 20 global
sites. Details of observations are in :cite:t:`D_Andrea_et_al._2013`.

See :cite:t:`Croft_et_al._2024` for validation of GCHP-TOMAS.

===========
Source Code
===========

The aerosol microphysics code is largely contained within the file
:file:`GeosCore/tomas_mod.F90`, which is modular---they use all their
own internal variables. For details, see the source code.

All TOMAS-specific sections of code are segregated from the rest of
GEOS-Chem with C-preprocessor :code:`#if defined` (or :code:`#ifdef`
for short) statements such as:

.. code-block:: Fortran90

   #if defined( TOMAS )

   # if defined( TOMAS40 )
     ... Code for 40 bin TOMAS simulation (optional) goes here ...
   # elif defined( TOMAS12 )
     ... Code for 12 bin TOMAS simulation (optional) goes here ...
   # elif defined( TOMAS15 )
     ... Code for 15 bin TOMAS simulation (optional) goes here ...
   # else
     ... Code for 30 bin TOMAS simulation (default) goes here ...
   # endif

   #endif

.. note::

   Although the TOMAS12 and TOMAS30 simulations are not currently
   supported, we have preserved the source code.  These simulations
   may be restored if there is demand.

TOMAS is invoked by configuring and compiling GEOS-Chem as follows:

.. code-block:: console

   $ cmake -DTOMAS=y -DTOMAS_BINS=15 ... etc ...  # For 15 bins, or
   $ cmake -DTOMAS=y -DTOMAS_BINS=40 ... etc ...  # For 40 bins
   $ make -j
   $ make install

==========
References
==========

#. TOMAS initial paper (sulfate only):
   :cite:t:`Adams_and_Seinfeld_2002`
#. TOMAS implementation in GEOS-Chem:
   :cite:t:`Trivitayanurak_et_al._2008`
#. Nucleation in GEOS-Chem: :cite:t:`Westervelt_et_al._2013`
#. TOMAS with sea-salt: :cite:t:`Pierce_and_Adams_2006`
#. TOMAS with carbonaceous aerosol: :cite:t:`Pierce_et_al._2007`,
   :cite:t:`Trivitayanurak_and_Adams_2014`
#. TOMAS with dust::cite:t:`Lee_et_al._2009`
#. TOMAS with SOA: :cite:t:`D_Andrea_et_al._2013`
#. TOMAS with offline DRE/AIE: :cite:t:`Kodros_et_al._2016`
#. TOMAS compared to the standard GEOS-Chem fullchem simulation:
   :cite:t:`Kodros_and_Pierce_2017`
#. TOMAS in GCHP: :cite:t:`Croft_et_al._2024`
#. Input data used by TOMAS: :cite:t:`Usoskin_and_Kovaltsov_2006`,
   :cite:t:`Yu_2010a`
