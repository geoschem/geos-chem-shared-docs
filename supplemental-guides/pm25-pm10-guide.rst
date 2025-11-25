.. _pmguide:

###############################
Particulate matter in GEOS-Chem
###############################

In this Guide we explain how particulate matter is computed in GEOS-Chem.

.. _pmguide-pm25:

================
PM2.5 definition
================

PM2.5 refers to particulate matter having a diameter of :math:`2.5 \ \mu
m` or less.  We use the following PM2.5 definition in GEOS-Chem, as
approved by the `Aerosols Working Group
<http://wiki.seas.harvard.edu/geos-chem/index.php/Aerosols_Working_Group>`_.

.. list-table:: Hygroscopic growth factors for PM2.5 constituent
		species
   :header-rows: 1
   :widths: 25 25 25 25

   * - Growth factor
     - Multiplies these species
     - Value at 35% RH
     - Value at 50% RH
   * - SIA_GROWTH
     - SO4, NIT, NH4, HMS
     - 1.10
     - 1.35
   * - ORG_GROWTH
     - OCPI, SOA
     - 1.05
     - 1.07
   * - SSA_GROWTH
     - SALA
     - 1.86
     - 1.86

The OA changes at both RH, and the SIA change at 50% RH are
straightforward changes to yield consistency between with the current
Kappa-Kohler hygroscopicity parameterization in GEOS-Chem based on
`Latimer and Martin
[2019] <https://acp.copernicus.org/articles/19/2635/2019/>`_.

The SIA recommendation at 35% RH is less certain since it depends on
the efflorescence RH of the SIA in the aerosol mixture under the
variable conditions of the instruments, collection media, and
laboratories involved. Given knowledge gaps about the aerosol phase at
low RH, the proposed growth factor of 1.1 assumes that half of the
particles are aqueous (growth factor of 1.19 for Kappa-Kohler) and the
other half are crystalline (growth factor of unity).

These growth factors are calculated using the change in radius between
different RH.  Essentially, the change in radius between the dry
(i.e. 0% RH) and wet (35% or 50% RH) aerosol is treated as a shell of
water for the purposes of calculating the additional mass associated
with the wet particle.  Under this condition, it can be shown that:

.. math::

   GrowthFactor = 1 + \left[\left(\left(\frac{R_{w}} {R_{d}} \right)
   ^3 - 1 \right) \times \frac{\rho_{H_2O}} {\rho_{d}}\right]

where:

- :math:`R_{w}` is the wet aerosol radius
- :math:`R_{d}` is the dry aerosol radius
- :math:`\rho_{H_2O}` is the density of water
- :math:`\rho_{d}` is the dry aerosol density

Emissions from the Anthropogenic Fugitive, Combustion and Industrial
Dust (AFCID) (cf :cite:t:`Philip_et_al._2017`) are automatically added
to DSTbin1, DSTbin2, DSTbin3, and DSTbin4 in most GEOS-Chem
simulations. AFCID is activated by default but can be disabled by the
user if so desired.

The DSTbin4 size bin includes mineral dust aerosols with diameter both
smaller and larger than :math:`2.5 \ \mu m`.  Dandan Zhang has
determined that 54.6% of DSTbin4 should be included in PM2.5
(cf. :cite:t:`Zhang_et_al._2025`).

In summary, PM2.5 in GEOS-Chem is computed as:

.. code-block:: text

   PM2.5 = ( NH4 + NIT + SO4 + HMS ) * SIA_GROWTH
         + BCPI
         + BCPO
         + ( OCPO + ( OCPI * ORG_GROWTH ) ) * ( OM/OC ratio )
         + DSTbin1
	 + DSTbin2
	 + DSTbin3
         + ( DSTbin4 * 0.546      )
         + ( SALA    * SSA_GROWTH )
         + ( SOA     * ORG_GROWTH )

.. note::

   Some modifications to this basic definition are necessary,
   depending on the SOA species that are used in a given GEOS-Chem
   simulation.  See :ref:`pmguide-pmdiags` below for details.

For 35% RH this evaluates to:

.. code-block:: text

   PM2.5 = ( NH4 + NIT + SO4 + HMS ) * 1.10
         + BCPI
         + BCPO
         + ( OCPO + ( OCPI * 1.05 ) ) * ( OM/OC ratio )
         + DSTbin1
         + DSTbin2
	 + DSTbin3
	 + ( DSTbin4 * 0.546 )
         + ( SALA    * 1.86  )
         + ( SOA     * 1.05  )

For 50% RH, this evaluates to:

.. code-block:: text

   PM2.5 = ( NH4 + NIT + SO4 + HMS ) * 1.35
         + BCPI
         + BCPO
         + ( OCPO + ( OCPI * 1.07 ) ) * ( OM/OC ratio )
         + DSTbin1
         + DSTbin2
	 + DSTbin3
	 + ( DSTbin4 * 0.546 )
         + ( SALA    * 1.86  )
         + ( SOA     * 1.07  )

By default, the OM/OC ratio is set to a constant value of 1.4. For
users who seek more information on the seasonal and spatial variation
of OM/OC in the lower troposphere, we provide the option to use the
seasonal gridded dataset developed by :cite:t:`Philip_et_al._2014`.
This dataset has some uncertainty, but offers more information than a
global-mean OM/OC ratio in regions where primary organic aerosols have
a large fossil fuel source.

Lastly, PM2.5 is converted to STP for diagnostic archival in
GEOS-Chem according to the ideal gas law:

.. math::

   PM2.5 = PM2.5 \times \frac{1013.25} {P} \times \frac{T} {298}

.. _pmguide-pm10:

===============
PM10 definition
===============

PM10 refers to particulate matter with a diameter of :math:`10 \ \mu m` or
less.  We compute PM10 in GEOS-Chem as follows:

.. code-block:: text

   PM10 = PM2.5
        + ( DSTbin4 * 0.454      )
        + DSTbin5
        + DSTbin6
        + ( DSTbin7 * 0.156      )
        + ( SALC    * SSA_GROWTH )

For 35% RH, this evaluates to:

.. code-block:: text

   PM10 = PM2.5
        + ( DSTbin4 * 0.454 )
        + DSTbin5
	+ DSTbin6
	+ ( DSTbin7 * 0.156 )
        + ( SALC    * 1.86  )

For 50% RH, this evaluates to:

.. code-block:: text

   PM10 = PM2.5
        + ( DSTbin4 * 0.454 )
        + DSTbin5
	+ DSTbin6
        + ( DSTbin7 * 0.156 )
        + ( SALC    * 1.86  )

DSTbin4 cont

The constant scale factors for DSTbin4 (0.454) and DSTbin7 (0.156) are
taken from :cite:t:`Zhang_et_al._2025`.  They represent the fraction
of aerosols in DSTbin4 with :math:`D \gt 2.5 \ \mu m`, and the
fraction of aerosols in DSTbin7 with :math:`D \le 10.0 \ \mu m`.

Lastly, PM10 is converted to STP for diagnostic archival in
GEOS-Chem according to the ideal gas law:

.. math::

   PM10 = PM10 \times \frac{1013.25} {P} \times \frac{T} {298}

.. _pmguide-pmdiags:

==========================
PM2.5 and PM10 diagnostics
==========================

The PM2.5 and PM10 diagnostics belong to the
:ref:`AerosolMass collection <histguide-aerosolmass>` in the GEOS-Chem
History diagnostics). They are computed according to the code below,
which may be found in `aerosol_mod.F90
<https://github.com/geoschem/geos-chem/blob/3671d504cab09196ee960447a361b36ec41fe926/GeosCore/aerosol_mod.F90#L809-L987>`_.

Avoid double-counting of ISOAAQ species
---------------------------------------

It was determined that the PM2.5 diagnostic was erroneously including
the ISOAAQ species in the accounting of PM2.5 when the Simple SOA
option was used.  After discussion with the Aerosols Working Group,
the PM2.5 and PM10 diagnostic computations were modified accordingly:

.. list-table::
   :header-rows: 1
   :widths: 40 60

   * - SOA option
     - Add this to PM2.5 and AOD diagnostics
   * - When Complex SOA is selected
     - TSOA + ASOA + ISOAAQ
   * - Otherwise
     - SOAS (simple SOA species)

The GEOS-Chem benchmark simulations carry both Simple SOA and Complex
SOA species, but only the Simple SOA species (SOAS) is included in
diagnostic output.
