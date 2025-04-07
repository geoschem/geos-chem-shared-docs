.. _transport-sim:

###########################
TransportTracers simulation
###########################

.. _transport-sim-overview:

========
Overview
========

The :program:`GEOS-Chem Rn-Pb-Be simulation` was based on that of the
old Harvard/GISS CTM model.  In version 12.2.0, this simulation was
extended to include additional passive species for benchmarking purposes and for
diagnosing transport in GEOS-Chem. At this time the simulation was
renamed to the :program:`TransportTracers` simulation.

In GEOS-Chem 14.2.0, the TransportTracers simulation was further
modified so that species names and definitions are now consistent with
GMAO's tracer gridded component (aka TR_GridComp). This will
facilitate comparison of transport within GEOS-Chem, GCHP, and GEOS.

References
----------

#. Liu, H., D. Jacob, I. Bey, and R.M. Yantosca, `Constraints from
   210Pb and 7Be on wet deposition and transport in a global
   three-dimensional chemical tracer model driven by assimilated
   meteorological fields`, J. Geophys. Res, 106, D11,
   12109-12128, 2001.
#. Jacob et al., `Evaluation and intercomparison of global atmospheric
   transport models using 222Rn and other short-lived
   tracers`, J. Geophys. Res, 102, 5953-5970, 1997.
#. Koch, D.M., D.J. Jacob, and W.C. Graustein, `Vertical transport of
   tropospheric aerosols as indicated by 7Be and 210Pb in a chemical
   tracer model`, J. Geophys. Res, 101, D13, 18651-18666, 1996.
#. Koch, D., and D. Rind, `Beryllium 10/beryllium 7 as a tracer of
   stratospheric transport`, J. Geophys. Res., 103, D4,
   3907-3917, 1998.
#. Lal, D., and B. Peters, `Cosmic ray produced radioactivity on the
   Earth. Handbuch der Physik`, 46/2, 551-612, edited by K. Sitte,
   Springer-Verlag, New York, 1967.

.. _transport-sim-species:

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
   * - Rn222
     - Radon-222 isotope
     - Rn222
     - 222.0
   * - Pb210
     - Lead-210 isotope
     - Pb210
     - 210.0
   * - Pb210s
     - Lead-210 isotope stratospheric-source tracer
     - Pb210
     - 210.0
   * - Be7
     - Beryllium-7 isotope
     - Be7
     - 7.0
   * - Be7s
     - Beryllium-7 isotope stratospheric-source tracer
     - Be7
     - 7.0
   * - Be10
     - Beryllium-10 isotope
     - Be10
     - 10.0
   * - Be10s
     - Beryllium-10 isotope stratospheric-source tracer
     - Be10
     - 10.0
   * - aoa
     - Age of air uniform source tracer
     - not listed
     - 1.0
   * - aoa_bl
     - Age of air uniform source tracer with sink restricted to the boundary layer
     - not listed
     - 1.0
   * - aoa_nh
     - Age of air uniform source tracer with surface sink restricted to a zone in the northern hemisphere
     - not listed
     - 1.0
   * - CH3I
     - Methyl iodide
     - CH3I
     - 141.94
   * - CO_25
     - Anthropogenic CO 25 day tracer
     - CO
     - 28.01
   * - CO_50
     - Anthropogenic CO 50 day tracer
     - CO
     - 28.01
   * - e90
     - Constant burden 90 day tracer
     - not listed
     - 1.0
   * - e90_n
     - Constant burden Northern Hemisphere 90 day tracer
     - not listed
     - 1.0
   * - e90_s
     - Constant burden Southern Hemisphere 90 day tracer
     - not listed
     - 1.0
   * - nh_5
     - Northern Hemisphere 5 day tracer
     - not listed
     - 1.0
   * - nh_50
     - Northern Hemisphere 50 day tracer
     - not listed
     - 1.0
   * - PassiveTracer
     - Passive tracer for mass conservation evaluation
     - not listed
     - 1.0
   * - SF6
     - Sulfur hexafluoride
     - SF6
     - 146.06
   * - st80_25
     - Stratosphere source 25 day tracer
     - not listed
     - 1.0
