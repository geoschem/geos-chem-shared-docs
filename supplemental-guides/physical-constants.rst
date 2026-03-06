.. _phys-consts:

##################
Physical constants
##################

Prior and updated values of global physical constants used in
GEOS-Chem are listed in the table below.  These constants are located
in the source code file :file:`Headers/physconstants.F90`.

.. list-table:: Physical Constants in GEOS-Chem
   :header-rows: 1
   :widths: 40 15 15 30

   * - Parameter
     - Variable name
     - Units
     - Value
   * - Avogadro's Number
     - AVO
     - particles/mol
     - 6.022140857e+23 [#A]_
   * - Acceleration due to gravity at earth's surface
     - g0
     - m/s\ :sup:`2`
     - 9.80665 [#A]_
   * - Pi
     - PI
     - unitless
     - 3.14159265358979323 [#B]_
   * - Molar gas constant
     - RSTARG
     - J/K/mol
     - 8.3144598 [#A]_
   * - Standard atmosphere
     - ATM
     - Pa
     - 101325 [#A]_
   * - Boltzmann's constant
     - BOLTZ
     - J/K
     - 1.38064852e-23 [#A]_
   * - Von Karman's constant
     - VON_KARMAN
     - unitless
     - 0.4 [#B]_
   * - Radius of earth
     - Re
     - m
     - 6.375e+6 [#B]_
   * - Dry air gas constant
     - Rd
     - J/K/kg
     - 287.0 [#B]_
   * - Water vapor gas constant
     - Rv
     - J/K/kg
     - 461.0 [#B]_
   * - Molecular weight of dry air
     - AIRMW
     - g/mol
     - 28.97 [#B]_
   * - Molecular weight of water
     - H2OMW
     - g/mol
     - 18.016 [#B]_
   * - Scale height of the atmosphere
     - SCALE_HEIGHT
     - m
     - 7600 [#B]_
   * - Molecules dry air per kg dry air
     - XNUMOLAIR
     - molecules/kg
     - AVO / (AIRMW \* 1e-3) [#B]_
   * - 100 divided by acceleration due to gravity
     - g0_100
     - s\ :sup:`2`/m
     - 100.0 / g0 [#B]_
   * - Pi divided by 180
     - PI_180
     - unitless
     - PI / 180.0 [#B]_
   * - Dry air gas constant divided by acceleration due to gravity
     - Rdg0
     - J s\ :sup:`2`/K/kg/m
     - Rd / g0 [#B]_

.. rubric:: Notes

.. [#A] Taken from `NIST CODATA Fundamental Physical Constants
	<https://data.nist.gov/od/id/FDB5909746705200E043065706813E54120>`_ (2014)

.. [#B] Legacy usage in GEOS-Chem versions prior to v11-01
