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

.. attention::

   The carbon gases simulation is slated to replace the individual
   CH4, CO2, and tagged CO simulations, likely in GEOS-Chem version
   14.7.0.

**Reference**:  Bukosa, B., Fisher, J., Deutscher, N., and Jones, D. A
Coupled CH4, CO and CO2 Simulation for Improved Chemical Source
Modelling. Atmosphere, 14:764, 2023, DOI: `10.3390/atmos14050764
<https://doi.org/10.3390/atmos14050764>`_.

===============
List of species
===============

.. list-table:: Advected species
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

.. list-table:: Non-advected species
   :header-rows: 1
   :align: center

   * - Species
     - Description
     - Formula
     - MW (g)
   * - COfromCH4
     - CO produced from methane oxidation (carbon mechanism)
     - CO
     - 28.01
   * - COfromNMVOC
     - CO produced from non-methane VOCs oxidation (carbon mechanism)
     - CO
     - 28.01
   * - CO2fromOH
     - Carbon dioxide loss by OH (carbon mechanism)
     - CO2
     - 44.01
   * - Dummy
     - Dummy species (carbon mechanism)
     - not listed
     - 1.0
   * - FixedOH
     - Hydroxyl radical (external input for carbon mechanism)
     - OH
     - 17.01
   * - FixedCl
     - Atomic chlorine (external input for carbon mechanism)
     - Cl
     - 35.45
   * - DummyCH4
     - Methane (external input for carbon mechanism)
     - CH4
     - 16.04
   * - DummyNMVOC
     - CO produced from NMVOC oxidation (external input for carbon mechanism)
     - CO
     - 28.01
