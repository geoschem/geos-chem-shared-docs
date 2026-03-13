.. |br| raw:: html

   <br />

.. _drydep-guide:

##############
Dry deposition
##############

This Guide describes the GEOS-Chem dry deposition scheme.


.. _drydep-guide-overview:

========
Overview
========

Here is a description of the GEOS-Chem dry deposition scheme from
several journal articles:

From Section 4 of :cite:t:`Alexander_et_al._2005`:

   Dry deposition velocities for sea-salt aerosols (and sulfate
   formed in sea-salt aerosols) are computed with the size-dependent
   scheme of :cite:t:`Zhang_et_al._2001` integrated over each model
   size bin and accounting for hygroscopic growth as a function of
   relative humidity :cite:t:`Gerber_1985`. Dry deposition velocities
   for all other species are computed with a standard
   resistance-in-series scheme based on :cite:t:`Wesely_1989` as
   described by :cite:t:`Wang_et_al._1998`.

From Section 2.4 of :cite:t:`Bey_et_al._2001`

   Dry deposition of oxidants and water soluble species is computed
   using a resistance-in-series model based on the original
   formulation of :cite:t:`Wesely_1989` with a number of modifications
   (:cite:t:`Wang_et_al._1998`). The dry deposition velocities are
   calculated locally using GEOS data for surface values of momentum
   and sensible heat fluxes, temperature, and solar radiation.

From Section 6 of :cite:t:`Wang_et_al._1998`:

   We use a resistance-in-series model
   (:cite:t:`Wesely_and_Hicks_1977`) to compute dry deposition
   velocities of O\ :sub:`3`, NO\ :sub:`2`, HNO\ :sub:`3`, PANs and H\
   :sub:2`\ O\ :sub:`2`. The deposition velocity :math:`V_i` for
   species :math:`i` is computed as:

   .. math::

      V_i = \frac{1}{R_a + R_{b,i} + R_{c,i}}

   where :math:`R_a` is the aerodynamic resistance to transfer to the
   surface, :math:`R_{b,i}` is the boundary resistance, and
   :math:`R_{c,i}` is the canopy surface resistance. :math:`R_a` and
   :math:`R_{b,i}` are calculated from the GCM meteorological
   variables (:cite:t:`Jacob_et_al._1993`. Surface resistances
   :math:`R_{c,i}` are based largely on the canopy model of
   :cite:t:`Wesely_1989`] with some improvements, including explicit
   dependence of canopy stomatal resistances on LAI
   (:cite:t:`Gao_and_Wesely_1995` and on direct and diffuse PAR
   within the canopy (:cite:t:`Baldocchi_et_al._1987`). The same
   radiative transfer model for direct and diffuse PAR in the canopy
   is used as in the formulation of isoprene emissions. Surface
   resistances for deposition to tropical rain forest and tundra are
   taken from :cite:t:`Jacob_and_Wofsy_1990` and
   :cite:t:`Jacob_et_al._1992`, respectively. The surface resistance
   for deposition of NO \ :sub:`2` is taken to be the same as that of
   ozone (:cite:t:`Erisman_and_Pul_1994`; :cite:t:`Kramm_et_al._1995`;
   :cite:t:`Eugster_and_Hesterberg_1996`) and hence lower than
   specified by :cite:t:`Wesely_1989`. Dry deposition of CO and
   hydrocarbons is negligibly small and not included in the model
   (:cite:t:`Mueller_and_Brasseur_1995`).

.. _drydep-guide-overview-aerosol:

Aerosol Dry Deposition
----------------------

There are 3 dry deposition routines in :file:`GeosCore/drydep_mod.F90`
that use the :cite:t:`Zhang_et_al._2001`  scheme:

#. **AERO_SFCRSII**: Aerodynamic resistance for seasalt
   tracers. Hygroscopic growth is accounted for. |br|
   |br|


#. **DUST_SFCRSII**: Aerodynamic resistance of dust aerosol
   tracers. No hygroscopic growth. Used for dust species. |br|
   |br|


#. **ADUST_SFCRSII**: Aerodynamic resistance of non-size resolved
   aerosols. No hygroscopic growth. Based on DUST_SRFCRSII and
   activated by :cite:t:`Pye_et_al._2009`. Assumes particle diameter
   is 0.5\ :math:`\mu m`, density is 1.5 g cm\ :sup:`-3` (1500 kg m\
   :sup:`-3`). Used for all other aerosols.

.. _drydep-guide-input:

===============================
Input Values for Dry Deposition
===============================

.. _drydep-guide-input-land:

Land Cover Parameters
---------------------

.. list-table:: Land cover input variables for dry deposition
   :header-rows: 1
   :align: center

   * - Variable
     - Read from
     - Description
     - Values
   * - DRYCOEFF
     - ``Olson_1992_Drydep_inputs.nc``
     - Local dependence of stomatal resistance on light intensity [#A]_
     - -0.358, 3.02, 3.85, ...
   * - IDRYDEP
     - ``Olson_1992_Drydep_inputs.nc``
     - Indices for the 11 dry deposition land types. [#B]_
     - 1, 2, 3 ...  11
   * - IOLSON
     - ``Olson_1992_Drydep_inputs.nc``
     - Indices for the 72 Olson land types. [#B]_
     - 1, 2, 3, ..., 74

.. rubric:: References

.. [#A] :cite:t:`Baldocchi_et_al._1987`

.. [#B] :cite:t:`Wesely_1989`

.. _drydep-guide-input-res:

Aerodynamic Resistances
-----------------------

Aerodynamic resistances and maximum deposition velocity for aerosols
(:code:`IVSMAX`) for each land type are read from
:file:`Olson_1992_Drydep_inputs.nc`.

.. list-table::
   :widths: 30 35 35
   :header-rows: 1
   :stub-columns: 1
   :align: center

   * - Parameter
     - Type 1
     - Type 2
   * - **DD type**
     - 1
     - 2
   * - **Description**
     - Snow/Ice
     - Deciduous forest
   * - **IRI**
     - 9999
     - 200
   * - **IRLU**
     - 9999
     - 9000
   * - **IRAC**
     - 0
     - 2000
   * - **IRGSS**
     - 100
     - 500
   * - **IRGSO**
     - 3500
     - 200
   * - **IRCLS**
     - 9999
     - 2000
   * - **IRCLO**
     - 1000
     - 1000
   * - **IVSMAX**
     - 100
     - 100

.. _drydep-guide-input-ovoc:

Dry deposition of organic VOCs
------------------------------

Parameters defined in the :ref:`species_database.yml <spcguide>` file:

.. list-table:: OVOC Dry Deposition Parameters
   :widths: 25 25 25 25
   :header-rows: 1

   * - Species
     - H\* (moles L⁻¹ atm⁻¹)
     - :math:`f_0`
     - Reference
   * - NO2
     - 0.01
     - 0.1
     -
   * - Ox
     - 0.01
     - 1.0
     -
   * - PAN
     - 3.6
     - 0.1
     -
   * - HNO3
     - 1.0d+14
     - 0.0
     -
   * - H2O2
     - 1.0d+5
     - 1.0
     -
   * - PMN
     - as PAN
     -
     -
   * - PPN
     - as PAN
     -
     -
   * - PYPAN
     - as PAN
     -
     -
   * - ISN2
     - as HNO3
     -
     -
   * - R4N2
     - as PAN
     -
     -
   * - CH2O
     - 6.0e+3
     - 1.0 (formerly 0)
     -
   * - GLYX
     - 3.6d+5
     - 1.0 (formerly 0)
     -
   * - MGLY
     - 3.7d+3
     - 1.0 (formerly 0)
     -
   * - GLYC
     - 4.1d+4
     - 1.0 (formerly 0)
     -
   * - MPAN, GPAN, GLPAN
     - as PAN
     -
     -
   * - N2O5
     - as HNO3
     -
     -
   * - HCOOH
     - 1.67d+5
     - 1.0 (formerly 0)
     -
   * - ACTA
     - 1.14d+4
     - 1.0 (formerly 0)
     -
   * - ISOPND
     - 1.7d+4
     - 0.0
     -
   * - ISOPNB
     - 1.7d+4
     - 0.0
     -
   * - MVKN+MACRN
     - 1.7d+4
     - 0.0
     -
   * - PROPNN
     - 1.0d+3
     - 0.0
     - Nitrooxyacetone (Sander table)
   * - RIP
     - as H2O2
     -
     -
   * - IEPOX
     - as H2O2
     -
     -
   * - MAP
     - 8.4d+2
     - 1.0
     -
   * - MVK
     - 4.4d1
     - 1.0 (formerly 0)
     - R. Sander
   * - MACR
     - 6.5d0
     - 1.0 (formerly 0)
     - R. Sander
   * - SO2
     - 1.0d+5
     - 0.0
     -

.. _drydep-guide-updates:

======================================
Updates to the original implementation
======================================

We have made several updates to the original implementation of dry
deposition in GEOS-Chem.  Here are some of the more important updates.

.. _drydep-guide-updates-cold:

Cold-temperature Updates
------------------------

The following updates were added, following Viral Shah:

* Set |HNO3| bulk surface resistance to 1 s/m.
* Limit increases in Rc at low temperature to a factor of 2.

.. _drydep-guide-updates-ra:

Bug fix for Aerodynamic Resistance R\ :sub:`a`
----------------------------------------------

For the calculation of aerodynamic resistance R\ :sub:`a` under very
stable atmospheric conditions the integration of the stability
function :math:`\phi_h` (aka dimensionless vertical temperature
gradient) from the roughness length to grid box center doesn't take
into account the discontinuity occurring at :math:`z/L = 1` where
:math:`\phi_h` switches from :math:`1 + 5(z/L)` to :math:`5 + z/L`
(:cite:t:`Holtslag_and_Boville_1993`). The result is too high of a
value for the integral of :math:`\phi_h` and subsequently R\ :sub:`a`\ .

A straightforward solution is to calculate RA under stable conditions
(:math:`L > 0`) using an integral form of the stability function, such as
equation (12) of :cite:t:`Holtslag_and_De_Bruin_1988`.  This fix
(submitted by Brian Boys) was implemented this fix into GEOS-Chem
v10-01 and later versions.

.. _drydep-guide-updates-snow:

Aerosol dry deposition velocities over snow and ice
---------------------------------------------------

Modeled aerosol dry deposition velocities over snow and ice surfaces
in the Arctic are much higher than estimated from measured values
(e.g., :cite:t:`Ibrahim_et_al._1983`; :cite:t:`Duan_et_al._1988`;
:cite:t:`Nilsson_and_Rannik_2001`). In GEOS-Chem v9-01-02 and later
versions we have imposed a dry deposition velocity of 0.03 cm/s
for all aerosols over snow and ice surfaces.

.. _drydep-guide-updates-press:

Use local surface pressure instead of a constant value
------------------------------------------------------

Pressure comes into play in the mean free path calculation. For some
reason the pressure was set at a constant value of 1500hPa.  We have
since replaced this constant pressure with the sea level pressure
taken from the meteorology.  The overall effect is small.
