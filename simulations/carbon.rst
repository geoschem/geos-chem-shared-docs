.. _carbon-sim:

#######################
Carbon gases simulation
#######################

.. _carbon-sim-overview:

========
Overview
========

The :program:`GEOS-Chem carbon gases simulation` carbon simulation
combines the chemistry reactions of the individual CH4, CO2, and
tagged CO simulations.  Users may configure the GEOS-Chem carbon gases
simulation to use all of the advected species, or any one of the
advected species.

The individual CH4, CO2, and Tagged CO simulations were removed in
GEOS-Chem 14.7.0.  These simulations have been superseded by the
carbon gases simulation.

**Reference**:  Bukosa, B., Fisher, J., Deutscher, N., and Jones, D. A
Coupled CH4, CO and CO2 Simulation for Improved Chemical Source
Modelling. Atmosphere, 14:764, 2023, DOI: `10.3390/atmos14050764
<https://doi.org/10.3390/atmos14050764>`_.

===============
List of species
===============

.. list-table:: Transported species
   :header-rows: 1
   :align: center

   * - Species
     - Description
     - Formula
     - MW (g)
   * - CH4
     - Methane
     - CH4
     - 16.04
   * - CO
     - Carbon monoxide
     - CO
     - 28.01
   * - CO2
     - Carbon dioxide
     - CO2
     - 44.01
   * - OCS (aka COS)
     - Carbonyl sulfate
     - OCS
     - 60.07

.. list-table:: Non-transported species
   :header-rows: 1
   :align: center

   * - Species
     - Description
     - Formula
     - MW (g)
   * - DummyCH4strat
     - Dummy species for tropospheric CH4 (external input)
     - Cl
     - 16.04
   * - DummyCH4trop
     - Dummy species for stratospheric CH4 (external input)
     - CH4
     - 16.04
   * - DummyNMVOC
     - CO produced from NMVOC oxidation (external input)
     - CO
     - 28.01
   * - FixedCl
     - Atomic chlorine (external input)
     - Cl
     - 35.45
   * - FixedOH
     - Hydroxyl radical (external input)
     - OH
     - 17.01
   * - LCH4byCl
     - Dummy species to track loss of CH4 by Cl
     - CH4
     - 1.0 [#A]_
   * - LCH4byOH
     - Dummy species to track loss of CH4 by OH
     - CH4
     - 1.0 [#A]_
   * - LCH4inStrat
     - Dummy species to track the loss of CH4 in the stratopshere
     - CH4
     - 1.0 [#A]_
   * - LCObyOH
     - Dummy species to track the loss of CO by OH
     - CO
     - 1.0 [#A]_
   * - LCOinStrat
     - Dummy species to track the loss of CO in the stratosphere
     - CO
     - 1.0 [#A]_
   * - PCOfromCH4
     - CO produced from methane oxidation
     - CO
     - 28.01
   * - PCOfromNMVOC
     - CO produced from non-methane VOCs oxidation
     - CO
     - 28.01

.. rubric:: Notes

.. [#A] Uses a placeholder molecular weight value of 1 to avoid
	incurring "Missing molecular weight" errors.
