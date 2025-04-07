.. _tago3-sim:

####################
Tagged O3 simulation
####################

.. _tago3-sim-overview:

========
Overview
========

The :program:`GEOS-Chem tagged ozone simulation` allows you to use
archived ozone production and loss rates to perform a simulation for
geographically tagged ozone tracers without having to run the
full-chemistry simulation. The tagged O3 simulation is also used to
spin up the ozone into steady-state when validating a new
meterorolgical field product.

.. _tago3-sim-species:

===============
List of species
===============

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - Formula
     - MW (g)
   * - O3
     - Ozone
     - O3
     - 48.0
   * - O3Strat
     - Ozone produced in the stratosphere
     - O3
     - 48.0
   * - O3ut
     - Ozone produced in the upper troposphere
     - O3
     - 48.0
   * - O3mt
     - Ozone produced in the middle troposphere
     - O3
     - 48.0
   * - O3row
     - Ozone produced in the rest of the world
     - O3
     - 48.0
   * - O3pcbl
     - Ozone produced in the Pacific Ocean boundary layer
     - O3
     - 48.0
   * - O3nabl
     - Ozone produced in the North America boundary layer
     - O3
     - 48.0
   * - O3atbl
     - Ozone produced in the Atlantic Ocean boundary layer
     - O3
     - 48.0
   * - O3eubl
     - Ozone produced in the European boundary layer
     - O3
     - 48.0
   * - O3afbl
     - Ozone produced in the African boundary layer
     - O3
     - 48.0
   * - O3asbl
     - Ozone produced in the Asian boundary layer
     - O3
     - 48.0
   * - O3init
     - Ozone from the initial condition
     - O3
     - 48.0
   * - O3usa
     - Ozone produced over the United States in PBL
     - O3
     - 48.0
