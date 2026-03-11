.. |br| raw:: html

   <br />

.. _apmguide:

####################################
Advanced Particle Microphysics (APM)
####################################

The Advanced Particle Microphysics (APM) package was developed for
implementation into GEOS-Chem at State University of New York (SUNY)
at Albany (:cite:t:`Yu_and_Luo_2009_apm`). The APM model is optimized
to accurately simulate secondary particle (SP, composed of sulfate,
nitrate, ammonium, and SOA) formation and their growth to CCN sizes,
with a higher size resolution for the size range of importance
(:math:`1.2 - 120 nm`): 30 bins, 10 additional bins for :math:`120
nm - 12 \mu m`). The present version of the APM employs 20 bins for
sea salt to cover the dry diameter size range of :math:`0.012 \mu m`
to :math:`12 \mu m`, and 15 bins for dust particles to cover size
range of :math:`0.03 \mu m` to :math:`50 \mu m`. Because of the large
differences in the median sizes of black carbon (BC) and primary
eorganic carbon (POC) from fossil fuel combustion and biomass burning,
we employ two log-normal modes (one for fossil fuel and another for
biomass burning) to represent hydrophobic BC and two other log-normal
modes for hydrophilic BC. Similarly, 4 log-normal modes are used to
represent hydrophobic and hydrophilic POC. The growth of nucleated
particles through the condensation of sulfuric acid vapor and
equilibrium uptake of nitrate, ammonium, and secondary organic aerosol
is explicitly simulated, along with the scavenging of secondary
particles by primary particles (dust, black carbon, organic carbon,
and sea salt). The amounts of secondary species coated on primary
particles (through condensation, coagulation, equilibrium uptake, and
aqueous chemistry) are tracked.

Implementations of the aerosol optical properties look up table
(:cite:t:`Yu_et_al._2012`) and radiation transfer (RF) model
(:cite:t:`Ma_et_al._2012`; :cite:t:`Yu_et_al._2013`) enable
GEOS-Chem/APM to derive aerosol direct radiative  forcing and first
indirect radiative forcing.

.. list-table:: APM Authors
   :header-rows: 1
   :align: center
   :widths: 50 50

   * - Author
     - Institution
   * - `Fangqun Yu <http://www.albany.edu/%7Eyfq>`_
     - SUNY Albany
   * - Gan Luo
     - SUNY Albany

.. _apmguide-comp-info:

=========================
Computational Information
=========================

The APM model contains a number of computationally efficient schemes:

#. Usage of pre-calculated lookup tables for nucleation rates and
   coagulation kernels;
#. Variable size ranges for particles of different types;
#. Variable bin resolution;
#. Variable and optimized time steps for the coagulation calculations;
#. The coating of primary particles by sulfate is tracked using one
   tracer (sulfate mass) for each type of primary particles;
#. Nitrate, ammonium, and SOAs associated with sulfate are calculated
   based on the equilibrium partition.

The above schemes enable the APM model to capture the main properties of
atmospheric particles important for their direct and indirect radiative
forcing while keeping the computational costs quite low.

In the study reported in :cite:t:`Yu_and_Luo_2009` all simulations are
running on 8-CPU Linux workstations with the 2.2 Ghz Dual Quad-Core
AMD Opteron Processor 2354. The model system was compiled using OpenMP
for running in parallel. The GEOS-Chem version used in the study
(v8-01-03) had 54 species, and took 24.23 hours for one year
full-chemistry simulations at 4x5 horizontal resolutions and 47 layers
(GEOS-5 data). GEOS-Chem v8-01-03 with APM incorporated had 127
species (73 additional species: 40 for sulfate, 20 for sea salt, one
for H2SO4 gas, 4 tracers for BC/OC from fossil fuel, 4 tracers for
BC/OC from biomass/bio-fuel, and 4 for sulfate attached to dust, BC,
primary OC, and sea salt  particles). With full size-resolved
microphysics (nucleation, condensation, coagulation, deposition, and
scavenging) and chemistry, it took the model (127 species) 52.35 hours
for the same year simulations on the same machine. In other words, the
efficient schemes allow the increase in the computing cost per 100%
increase in number of tracers (associated with particle size
information) to :math:`(52.35/24.23-1)/(127/54-1) = 86%`. Such a
relatively small increase in the computing cost associated with full
size-resolved microphysics is desirable and makes the future coupling
of APM model with global climate model feasible.

.. _apm-guide-details:

=============
Model Details
=============

.. _apm-guide-details-particles:

Particle Types and Representation
---------------------------------

.. list-table:: Particles (Mixing state: Semi-externally mixed)
   :header-rows: 1
   :align: center

   * - Particle
     - Type
   * - Sulfate
     - Secondary Particle (SP)
   * - Nitrate / Ammonium / SOA in equilibrium
     - Secondary Particle (SP)
   * - Black carbon (BC)
     - Primary Particle [#A]_
   * - Primary organic carbon (POC)
     - Primary Particle [#A]_
   * - Dust
     - Primary Particle [#A]_
   * - Sea salt
     - Primary Particle [#A]_

.. rubric:: Notes

.. [#A] APM also includes coated Secondary Particle (SP) species on
	each type of the Primary Particles

.. list-table:: Size structure
   :header-rows: 1
   :align: center

   * - Particle or type
     - Number of bins or modes
     - Bin range
   * - Secondary Particles (SP)
     - 40 bins
     - :math:`1.2 nm - 12 \mu m`
   * - Black carbon (BC)
     - 2 log-normal modes for hydrophobic BC |br|
       2 log-normal modes for hydrophilic BC
     - N/A
   * - Primary organic carbon (POC)
     - 2 log-normal modes for hydrophobic OC |br|
       2 log-normal modes for hydrophilic OC
     - N/A
   * - Dust:
     - 15 bins
     - :math:`30 nm - 50 \mu m`
   * - Sea salt
     - 20 bins
     - :math:`12 nm - 12 \mu m`

.. _apm-guide-details-mp:

Microphysics
------------

.. _apm-guide-details-particles-mp-nucl:

Nucleation
~~~~~~~~~~

Nucleation (or new particle formation) is one of the key processes
connecting gas-phase chemistry to aerosol microphysics and controlling
number concentrations (and size distributions) of atmospheric
particles. The APM model employs ion-mediated nucleation (IMN)
(:cite:t:`Yu_2010a`) and binary homogeneous nucleation (BHN)
(:cite:t:`Yu_2008`)
in term of look-up tables. Other nucleation schemes can also be
included.

Based on IMN mechanism, sulfuric acid vapor concentration ([H2SO4]),
temperature (T), relative humidity (RH), ionization rate (Q), and
surface area of pre-existing particles (S) have profound and
non-linear impacts on nucleation rates. The sensitivities of
nucleation rates to the changes in these key parameters may imply
important physical feedback mechanisms involving climate and emission
changes, chemistry, solar variations, nucleation, aerosol number
abundance, and aerosol indirect radiative forcing (:cite:t:`Yu_2010a`).

.. _apm-guide-details-particles-mp-growth:

Growth
~~~~~~

H2SO4 vapor concentration is a tracer and the condensation of H2SO4 on
all particles is explicitly simulated. Many field measurements
indicate significant contribution of secondary organic gases (SOGs)to
the growth of secondary particles. A scheme to consider the oxidation
aging of SOGs and explicit condensation of low volatile SOGs has been
developed (:cite:t:`Yu_2011a`).

.. _apm-guide-details-particles-mp-coag:

Coagulation
~~~~~~~~~~~

Coagulation is a process in which particles of various sizes and
compositions collide with each other and coalesce to form larger
particles. In the atmosphere, coagulation is an important process
scavenging small particles and turning externally mixed particles into
internally mixed particles. In the present model, the mass conserving
semi-implicit numerical scheme is employed to solve the self
coagulation of size-resolved sulfate and sea salt particles, as well
as the scavenging of sulfate particles by sea salt, dust, BC, and POC
particles.

Coagulation is the most time-consuming process among various
size-resolved microphysical processes (nucleation, growth,
coagulation, and deposition). The reason is that coagulation involves
particles of different sizes and thus adds two additional dimensions
(size of particle A and size of particle B) into 3-dimensional spatial
grid system. For example, for 40 bins of sulfate, coagulation among
sulfate particles is equivalent to solving 40x40 = 1600 reaction
equations. To reduce the computing cost of 3-D sectional aerosol
microphysics model, it is critical to optimize the number of bins and
coagulation calculation.

.. _apmguide-details-optics:

Aerosol optical properties
--------------------------

The key particle optical properties needed for radiative transfer
calculation include extinction efficiency (:math:`Q_{ext}`), single
scattering albedo (:math:`\omega`), and asymmetry parameter
(:math:`g`). The absorption extinction efficiency (:math:`Q_{abs}`)
can be calculated from :math:`Q_{ext}` and
:math:`\omega` as :math:`Q_{abs} = Q_{ext} \times (1 - \omega)`. The
values of :math:`Q_{ext}`, :math:`\omega`, and :math:`g` depend on
wavelength (:math:`\lambda`), core diameter (:math:`d_{core}`), shell
diameter (:math:`d_{shell}`), and real (:math:`k_r`) as well as
imaginary (:math:`k_i`) components of refractive index (:math:`k =
k_{r} - k_{i}i`) for both core and shell, and can be calculated with
widely used Mie theory. The core-shell model of *Ackerman and Toon*,
(1981), which can use either the volume averaged refractive indices or
the shell/core configuration, is employed in our study.

To reduce computation cost for 3-D online calculation, we have designed
and generated lookup tables so that :math:`Q_{ext}`, :math:`\omega`,
and :math:`g` values can be determined efficiently. According to the
properties of aerosols resolved by APM, three lookup tables have been
developed: the first for particles without solid absorbing cores
(i.e., secondary particles, coated sea salt, and coated POC), the
second for coated BC, and the third for coated dust. For coated BC and
dust particles, the core-shell model assumes that BC and dust have a
spherical core, surrounded by a spherical shell composed of all the
other non- or less- absorbing secondary species and water. For
hydrated (i.e., wet) secondary particles, coated sea salt particles,
and coated primary organic particles, we set the core size to zero and
use the volume-average of refractive index to calculate the optical
properties of particles of given wet sizes. For refractive index of BC
core, we use the value recommended by :cite:t:`Bond_et_al._2006` which
is :math:`1.85 – 0.71i`. For dust core, a wavelength dependent
parameterization of refractive index presented in
:cite:t:`Balkanski_et_al._2007` is adapted. The volume-averaged
refractive indices for species other than BC and dust are calculated
based on the composition predicted by APM.

Details can be found in :cite:t:`Yu_et_al._2012`.

..  _apmguide-details-radtran:

Radiative transfer (RF)
-----------------------

Radiative transfer model is needed for aerosol radiative forcing
calculation. :cite:t:`Ma_et_al._2012` integrated the radiation
transfer (RF) model of the Canadian Center for Climate Modeling and
Analysis (CCCma) with GEOS-Chem/APM and used the model to study
aerosol direct RF (DRF).

A more recent comparison of DRF values (clear sky vs. all sky) based on
GEOS-Chem/APM with those of other AeroCom models (discussion version of
:cite:t:`Myhre_et_al._2013` indicates that CCCMa RF code may have
underestimated the impacts of clouds on radiation. We found out that the
underestimation is likely associated with the cloud overlapping
assumption. The version of CCCMa RF code we integrated into GEOS-Chem
does not contain the widely used McICA (Monte-Carlo Independent Column
Approximation) scheme ─ a fast, flexible, approximate technique for
computing radiative transfer in an inhomogeneous cloud field.

To properly take into account the impacts of clouds on aerosol DRF and
more importantly to study aerosol first indirect RF (IRF),
:cite:t:`Yu_et_al._2013` integrated the widely used Rapid Radiative
Transfer Model for GCMs (RRTMG) (:cite:t:`Mlawer_et_al._1997`;
:cite:t:`Iacono_et_al._2003`, :cite:t:`Iacono_et_al._2008`) with
GEOS-Chem/APM.

RRTMG, which contains the McICA scheme, is a broadband k-distribution
radiation model (:cite:t:`Mlawer_et_al._1997`;
:cite:t:`Iacono_et_al._2003`; :cite:t:`Iacono_et_al._2008`) that has
been widely used in community models (such as WRF, CAM5, etc.). The
RRTMG for shortwave (SW) (used in this study) can calculate fluxes and
heating rates over 14 contiguous shortwave bands (:math:`820 - 50000
cm^{-1}`, or :math:`0.2 - 12.20 \mu m`). The individual band ranges
(in wavenumbers, :math:`cm^{-1}`)  are: 2600-3250, 3250-4000,
4000-4650, 4650-5150, 5150-6150, 6150-7700, 7700-8050, 8050-12850, 
12850-16000, 16000-22650, 22650-29000, 29000-38000, 38000-50000, and
820-2600, with the last band coded out of sequence to preserve
spectral continuity with the longwave bands.

GEOS-Chem/APM-RRTMG results have been included into the final manuscript
on radiative forcing of the direct aerosol effect from AeroCom Phase II
simulations (:cite:t:`Myhre_et_al._2013`) and another manuscript on
host model uncertainties in aerosol radiative forcing estimates
(:cite:t`Stier_et_al._2013`). GEOS-Chem/APM-RRTMG has also been
employed to study the first aerosol indirect radiative forcing
(:cite:t:`Yu_et_al._2013`).

.. _apmguide-dust-side:

Dust Particle Size Distribution
-------------------------------

`Model-simulated annual mean dust particle mass size distributions in
Beijing, normalized to total dust mass
concentrations. <https://drive.google.com/file/d/1WWoMI0B-2EbmB7QsjnfBz6BekkP0JgVf/view?usp=sharing>`_
The distribution
peaks in diameter of :math:`~3-5 \mu m`. Based on the size
distributions, about 90% of DST4 (diameter range :math:`6 - 12 \mu m`)
is in PM10, and 30% of DST2 (diameter range :math:`2 - 3.6 \mu m`) is
in PM2.5.

.. _apmguide-validation:

========================================
Validation, Application, and Development
========================================

The first application of GEOS-Chem + APM focuses on predicting the
number concentrations of particles in the troposphere. Significant
amount of efforts have been devoted to validate the simulated global
spatial distributions of particle number abundance, using a large amount
of land-, ship-, and aircraft- based measurements. See following
publications for details (full citations are given in the `in the
References section <#References>`__).

#. :cite:t:`Yu_and_Luo_2009`
#. :cite:t:`Yu_et_al._2010c`

The model has been applied in a number of other studies and results have
been reported in the following papers:

#. :cite:t:`Luo_and_Yu_2011a`
#. :cite:t:`Luo_and_Yu_2010`
#. :cite:t:`Yu_and_Luo_2009`
#. :cite:t:`Yu_et_al._2010c`

.. _apmguide-aerocom:

=======================
AEROCOM intercomparison
=======================

The `AEROCOM-project <http://dataipsl.ipsl.jussieu.fr/AEROCOM/>`__ is an
open international initiative of scientists interested in the
advancement of the understanding of the global aerosol and its impact on
climate. We have made a commitment to represent the GEOS-Chem community
to submit aerosol microphysics simulations based on GEOS-Chem + APM.

We submitted our size-resolved microphysics simulation results for
A2-CTRL-2006 (GEOS-5, 2x2.5) to AEROCOM in January, 2011. The submitted
results include OC and SOA mass concentrations which have been used in
AEROCOM OA intercomparison. We also managed to submit AOD, AAOD, aerosol
direct radiative forcing results to AeroCom in September 2011. The
results have been in the following papers:

- Aerosol direct radiative forcing (:cite:t:`Myhre_et_al._2013`, 2013)
- Host model uncertainties in aerosol radiative forcing estimates
  (:cite:t:`Stier_et_al._2013`)
- Aerosol microphysics (:cite:t:`Mann_et_al._2014`)
- Aerosol organics (:cite:t:`Tsigaridis_et_al._2014`)

.. _apmguide-community:

=============================
APM in other community models
=============================

The same APM model initially developed for GEOS-Chem
(:cite:t:`Yu_and_Luo_2009`) has been incorporated into WRF-Chem
(:cite:t:`Luo_and_Yu_2011a`). GEOS-Chem/APM results provide initial and
boundary conditions for WRF-Chem/APM simulations.
