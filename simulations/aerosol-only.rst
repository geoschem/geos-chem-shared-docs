.. _aerosol-sim:

#######################
Aerosol-only simulation
#######################

.. _aerosol-sim-overview:

========
Overview
========

The :program:`GEOS-Chem aerosol-only simulation` is an offline
simulation for aerosol tracers only. It uses archived monthly mean OH,
NO3, O3 and total nitrate concentrations archived from a previous
full-chemistry simulation (more on that below), as well as production
and loss rates for H2O2. This simulation does not provide "tagged"
capabilities, but reduces the suite of tracers from full chemistry.

NOTES:

#. The aerosol-only simuation is currently functional in GEOS-Chem Classic.
#. There is currently no aerosol-only run directory for
   GCHP. Interested users are encouraged to make this modification on
   their own.
#. OH concentrations are archived from the most recent 10-year
   benchmark simulation.
#. O3, NO3 and total nitrate (HNO3+NIT) concentrations, as well as
   production rates and photolysis rates for H2O2 are archived from
   the most recent 10-year benchmark simulation.

.. _aerosol-sim-species:

===============
List of species
===============

.. list-table:: Transported species
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - Formula
     - MW (g)
   * - BCPI
     - Hydrophilic black carbon aerosol
     - C
     - 12.01
   * - BCPO
     - Hydrophobic black carbon aerosol
     - C
     - 12.01
   * - DMS
     - Dimethyl sulfide
     - (CH3)2S
     - 62.13
   * - DST1
     - Dust aerosol, Reff = 0.7 microns
     - Dust
     - 29.0
   * - DST2
     - Dust aerosol, Reff = 1.4 microns
     - Dust
     - 29.0
   * - DST3
     - Dust aerosol, Reff = 2.4 microns
     - Dust
     - 29.0
   * - DST4
     - Dust aerosol, Reff = 4.5 microns
     - Dust
     - 29.0
   * - H2O2
     - Hydrogen peroxide
     - H2O2
     - 34.02
   * - MSA
     - Methyl sulfonic acid
     - CH4SO3
     - 96.1
   * - NH3
     - Ammonia
     - NH3
     - 17.04
   * - NH4
     - Ammonium
     - NH4
     - 18.05
   * - NIT
     - Inorganic nitrates
     - not listed
     - 62.01
   * - NITs
     - Inorganic nitrates on surface of seasalt aerosol
     - not listed
     - 31.4
   * - OCPI
     - Hydrophilic organic carbon aerosol
     - not listed
     - 12.01
   * - OCPO
     - Hydrophobic organic carbon aerosol
     - not listed
     - 12.01
   * - pFe
     - Anthropogenic iron
     - Fe
     - 55.85
   * - SALA
     - Fine (0.01-0.05 microns) sea salt aerosol
     - not listed
     - 31.4
   * - SALAAL
     - Accumulation mode sea salt alkalinity
     - not listed
     - 31.4
   * - SALACL
     - Chloride in Accumulation mode sea salt aerosol
     - not listed
     - 35.45
   * - SALC
     - Coarse (0.5-8 microns) sea salt aerosol
     - not listed
     - 31.4
   * - SALCAL
     - Coarse mode sea salt alkalinity
     - not listed
     - 31.4
   * - SALCCL
     - Chloride in Coarse mode sea salt aerosol
     - not listed
     - 35.45
   * - SO2
     - Sulfur dioxide
     - SO2
     - 64.04
   * - SO4
     - Sulfate
     - SO4
     - 96.06
   * - SO4s
     - Sulfate on surface of seasalt aerosol
     - not listed
     - 31.4
   * - SOAP
     - SOA Precursor - lumped species for simplified SOA parameterization
     - not listed
     - 150.0
   * - SOAS
     - SOA Simple - simplified non-volatile SOA parameterization
     - not listed
     - 150.0
