.. |br| raw:: html

   <br />

.. _apmguide:

####################################
Advanced Particle Microphysics (APM)
####################################

The Advanced Particle Microphysics (APM) package was developed for
implementation into GEOS-Chem at State University of New York (SUNY)
at Albany (*Yu and Luo*, 2009). The APM model is optimized to accurately
simulate secondary particle (SP, composed of sulfate, nitrate,
ammonium, and SOA) formation and their growth to CCN sizes, with a
higher size resolution for the size range of importance (:math:`1.2 - 120 nm`):
30 bins, 10 additional bins for :math:`120 nm - 12 \mu m`). The
present version of the APM employs 20 bins for sea salt to cover the
dry diameter size range of :math:`0.012 \mu m` to :math:`12 \mu m`,
and 15 bins for dust particles to cover size range of :math:`0.03 \mu
m` to :math:`50 \mu m`. Because of the large differences in
the median sizes of black carbon (BC) and primary organic carbon (POC)
from fossil fuel combustion and biomass burning, we employ two
log-normal modes (one for fossil fuel and another for biomass burning)
to represent hydrophobic BC and two other log-normal modes for
hydrophilic BC. Similarly, 4 log-normal modes are used to represent
hydrophobic and hydrophilic POC. The growth of nucleated particles
through the condensation of sulfuric acid vapor and equilibrium uptake
of nitrate, ammonium, and secondary organic aerosol is explicitly
simulated, along with the scavenging of secondary particles by primary
particles (dust, black carbon, organic carbon, and sea salt). The
amounts of secondary species coated on primary particles (through
condensation, coagulation, equilibrium uptake, and aqueous chemistry)
are tracked.

Implementations of the aerosol optical properties look up table
(*Yu et al.*, 2012) and radiation transfer (RF) model (*Ma et al.*,
2012; *Yu et al.*, 2013) enable GEOS-Chem/APM to derive aerosol direct
radiative  forcing and first indirect radiative forcing.

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

In the study reported in `Yu and Luo
[2009] <https://acp.copernicus.org/articles/9/7691/2009/>`__,
all simulations are running on 8-CPU Linux workstations with the 2.2
Ghz Dual Quad-Core AMD Opteron Processor 2354. The model system was
compiled using OpenMP for running in parallel. The GEOS-Chem version
used in the study (v8-01-03) had 54 species, and took 24.23 hours for
one year full-chemistry simulations at 4x5 horizontal resolutions and
47 layers (GEOS-5 data). GEOS-Chem v8-01-03 with APM incorporated had
127 species (73 additional species: 40 for sulfate, 20 for sea salt,
one for H2SO4 gas, 4 tracers for BC/OC from fossil fuel, 4 tracers for
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
particles. The APM model employs ion-mediated nucleation (IMN) (*Yu*,
JGR [2010]) and binary homogeneous nucleation (BHN) (*Yu*, JGR [2008]),
in term of look-up tables. Other nucleation schemes can also be
included.

Based on IMN mechanism, sulfuric acid vapor concentration ([H2SO4]),
temperature (T), relative humidity (RH), ionization rate (Q), and
surface area of pre-existing particles (S) have profound and
non-linear impacts on nucleation rates. The sensitivities of
nucleation rates to the changes in these key parameters may imply
important physical feedback mechanisms involving climate and emission
changes, chemistry, solar variations, nucleation, aerosol number
abundance, and aerosol indirect radiative forcing (*Yu*, JGR [2010]).

.. _apm-guide-details-particles-mp-growth:

Growth
~~~~~~

H2SO4 vapor concentration is a tracer and the condensation of H2SO4 on
all particles is explicitly simulated. Many field measurements
indicate significant contribution of secondary organic gases (SOGs)to
the growth of secondary particles. A scheme to consider the oxidation
aging of SOGs and explicit condensation of low volatile SOGs has been
developed (*Yu*, ACP [2011]).

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
core, we use the value recommended by *Bond et al.*, (2006) which is
:math:`1.85 – 0.71i`. For dust core,
a wavelength dependent parameterization of refractive index presented in
Balkanski et al. (2007) is adapted. The volume-averaged refractive
indices for species other than BC and dust are calculated based on the
composition predicted by APM.

Details can be found in *Yu et al*, (ACP, 2012).

..  _apmguide-details-radtran:

Radiative transfer (RF)
-----------------------

Radiative transfer model is needed for aerosol radiative forcing
calculation. *Ma et al.*, (2012) integrated the radiation transfer (RF)
model of the Canadian Center for Climate Modeling and Analysis (CCCma)
with GEOS-Chem/APM and used the model to study aerosol direct RF (DRF).

A more recent comparison of DRF values (clear sky vs. all sky) based on
GEOS-Chem/APM with those of other AeroCom models (discussion version of
*Myhre et al.*, 2013) indicates that CCCMa RF code may have underestimated
the impacts of clouds on radiation. We found out that the
underestimation is likely associated with the cloud overlapping
assumption. The version of CCCMa RF code we integrated into GEOS-Chem
does not contain the widely used McICA (Monte-Carlo Independent Column
Approximation) scheme ─ a fast, flexible, approximate technique for
computing radiative transfer in an inhomogeneous cloud field.

To properly take into account the impacts of clouds on aerosol DRF and
more importantly to study aerosol first indirect RF (IRF), *Yu et al*,
(2013) integrated the widely used Rapid Radiative Transfer Model for
GCMs (RRTMG) (*Mlawer et al.*, 1997; *Iacono et al.*, 2003, 2008) with
GEOS-Chem/APM.

RRTMG, which contains the McICA scheme, is a broadband k-distribution
radiation model (*Mlawer et al.*, 1997; *Iacono et al.*, 2003, 2008) that
has been widely used in community models (such as WRF, CAM5, etc.). The
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
simulations (*Myhre et al.*, 2013) and another manuscript on host model
uncertainties in aerosol radiative forcing estimates (*Stier et al.*,
2013). GEOS-Chem/APM-RRTMG has also been employed to study the first
aerosol indirect radiative forcing (*Yu et al.*, 2013).

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

#. *Yu and Luo*, ACP [2009]
#. *Yu et. al.*, JGR [2010]

The model has been applied in a number of other studies and results have
been reported in the following papers:

#. *Luo and Yu*, ACP [2011]
#. *Luo and Yu*, ACP [2010]
#. *Yu and Luo*, Atmosphere [2010]
#. *Yu et al.*, ACPD, [2011]

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

- Aerosol direct radiative forcing (*Myhre et al.*, 2013)
- Host model uncertainties in aerosol radiative forcing estimates
  (*Stier et al.*, 2013)
- Aerosol microphysics (*Mann et al.*, 2013)
- Aerosol organics (*Tsigaridis et al.*, 2014)

.. _apmguide-community:

=============================
APM in other community models
=============================

The same APM model initially developed for GEOS-Chem (*Yu and Luo*, 2009)
has been incorporated into WRF-Chem (*Luo and Yu, 2011*). GEOS-Chem/APM
results provide initial and boundary conditions for WRF-Chem/APM
simulations.

.. _apmguide-references:

==========
References
==========

#. Iacono, M.J., J.S. Delamere, E.J. Mlawer, S.A. Clough: *Evaluation of
   upper tropospheric water vapor in the NCAR community climate model
   (CCM3) using modeled and observed HIRS radiances*, J. Geophys. Res.,
   108(D2), 4037, `doi:10.1029/2002JD002539
   <https://doi.org/10.1029/2002JD002539>`_, 2003.
#. Iacono, M. J., Delamere, J. S., Mlawer, E. J., Shephard, M.W.,
   Clough, S. A., and Collins,W. D.: *Radiative forcing by long-lived
   greenhouse gases: calculations with the AER radiative transfer
   models*, J. Geophys. Res.-Atmos., 113(D13), D13103,
   `doi:10.1029/2008jd009944
   <https://doi.org/10.1029/2008jd009944>`_, 2008.
#. Luo, G., and F. Yu, *A numerical evaluation of global oceanic
   emissions of alpha-pinene and isoprene*, Atmos. Chem. Phys., **10**,
   2007-2015, 2010.
#. Luo, G., and F. Yu, *Sensitivity of global cloud condensation nuclei
   concentrations to primary sulfate emissions parameterizations*,
   Atmos. Chem. Phys., 11, 1949-1959, `doi:10.5194/acp-11-1949-2011,
   <https://doi.org/10.5194/acp-11-1949-2011>`_, 2011.
#. Luo, G., and F. Yu, *Simulation of particle formation and number
   concentration over the Eastern United States with the WRF-Chem + APM
   model*, Atmos. Chem. Phys. Discuss., 11, 11281-11309, 2011.
#. Ma, X., F. Yu, and G. Luo, Aerosol direct radiative forcing based on
   GEOS-Chem/APM and uncertainties, Atmos. Chem. Phys., 12, 5563-5581,
   `doi:10.5194/acp-12-5563-2012,
   <https://doi.org/10.5194/acp-12-5563-2012>`_, 2012.
#. Mann, G. W., Carslaw, K. S., Reddington, C. L., Pringle, K. J.,
   Schulz, M., Asmi, A., Spracklen, D. V., Ridley, D. A., Woodhouse, M.
   T., Lee, L. A., Zhang, K., Ghan, S. J., Easter, R. C., Liu, X.,
   Stier, P., Lee, Y. H., Adams, P. J., Tost, H., Lelieveld, J., Bauer,
   S. E., Tsigaridis, K., van Noije, T. P. C., Strunk, A., Vignati, E.,
   Bellouin, N., Dalvi, M., Johnson, C. E., Bergman, T., Kokkola, H.,
   von Salzen, K., Yu, F., Luo, G., Petzold, A., Heintzenberg, J.,
   Clarke, A., Ogren, J. A., Gras, J., Baltensperger, U., Kaminski, U.,
   Jennings, S. G., O'Dowd, C. D., Harrison, R. M., Beddows, D. C. S.,
   Kulmala, M., Viisanen, Y., Ulevicius, V., Mihalopoulos, N., Zdimal,
   V., Fiebig, M., Hansson, H.-C., Swietlicki, E., and Henzing, J. S.:
   *Intercomparison and evaluation of global aerosol microphysical
   properties among AeroCom models of a range of complexity*, Atmos.
   Chem. Phys., 14, 4679-4713, `doi:10.5194/acp-14-4679-2014
   <https://doi.org/10.5194/acp-14-4679-2014>`_, 2014.
#. Mlawer, E.J., S.J. Taubman, P.D. Brown, M.J. Iacono and S.A. Clough:
   *RRTM, a validated correlated-k model for the longwave*. J. Geophys.
   Res., 102, 16,663-16,682, 1997.
#. Myhre, G., Samset, B. H., Schulz, M., Balkanski, Y., Bauer, S.,
   Berntsen, T. K., Bian, H., Bellouin, N., Chin, M., Diehl, T., Easter,
   R. C., Feichter, J., Ghan, S. J., Hauglustaine, D., Iversen, T.,
   Kinne, S., Kirkevag, A., Lamarque, J.-F., Lin, G., Liu, X., Lund, M.
   T., Luo, G., Ma, X., van Noije, T., Penner, J. E., Rasch, P. J.,
   Ruiz, A., Seland, O., Skeie, R. B., Stier, P., Takemura, T.,
   Tsigaridis, K., Wang, P., Wang, Z., Xu, L., Yu, H., Yu, F., Yoon,
   J.-H., Zhang, K., Zhang, H., and Zhou, C.: *Radiative forcing of the
   direct aerosol effect from AeroCom Phase II simulations*, Atmos. Chem.
   Phys., 13, 1853-1877, `doi:10.5194/acp-13-1853-2013
   <https://doi.org/10.5194/acp-13-1853-2013>`_, 2013.
#. Stier, P., Schutgens, N. A. J., Bellouin, N., Bian, H., Boucher, O.,
   Chin, M., Ghan, S., Huneeus, N., Kinne, S., Lin, G., Ma, X., Myhre,
   G., Penner, J. E., Randles, C. A., Samset, B., Schulz, M., Takemura,
   T., Yu, F., Yu, H., and Zhou, C.: *Host model uncertainties in aerosol
   radiative forcing estimates: results from the AeroCom Prescribed
   intercomparison study*, Atmos. Chem. Phys., 13, 3245-3270,
   `doi:10.5194/acp-13-3245-2013
   <https://doi.org/10.5194/acp-13-3245-2013>`_, 2013.
#. Tsigaridis, K., Daskalakis, N., Kanakidou, M., Adams, P. J., Artaxo,
   P., Bahadur, R., Balkanski, Y., Bauer, S. E., Bellouin, N.,
   Benedetti, A., Bergman, T., Berntsen, T. K., Beukes, J. P., Bian, H.,
   Carslaw, K. S., Chin, M., Curci, G., Diehl, T., Easter, R. C., Ghan,
   S. J., Gong, S. L., Hodzic, A., Hoyle, C. R., Iversen, T., Jathar,
   S., Jimenez, J. L., Kaiser, J. W., Kirkevåg, A., Koch, D., Kokkola,
   H., Lee, Y. H, Lin, G., Liu, X., Luo, G., Ma, X., Mann, G. W.,
   Mihalopoulos, N., Morcrette, J.-J., Müller, J.-F., Myhre, G.,
   Myriokefalitakis, S., Ng, N. L., O'Donnell, D., Penner, J. E.,
   Pozzoli, L., Pringle, K. J., Russell, L. M., Schulz, M., Sciare, J.,
   Seland, Ø., Shindell, D. T., Sillman, S., Skeie, R. B., Spracklen,
   D., Stavrakou, T., Steenrod, S. D., Takemura, T., Tiitta, P., Tilmes,
   S., Tost, H., van Noije, T., van Zyl, P. G., von Salzen, K., Yu, F.,
   Wang, Z., Wang, Z., Zaveri, R. A., Zhang, H., Zhang, K., Zhang, Q.,
   and Zhang, X.: *The AeroCom evaluation and intercomparison of organic
   aerosol in global models*, Atmos. Chem. Phys., 14, 10845-10895,
   `doi:10.5194/acp-14-10845-2014
   <https://doi.org10.5194/acp-14-10845-2014>`_, 2014.
#. Yu, F., *Updated H2SO4-H2O binary homogeneous nucleation rate look-up
   tables*, J. Geophy. Res., 113, D24201,
   `doi:10.1029/2008JD010527
   <https://doi.org/10.1029/2008JD010527>`_, 2008.
#. Yu, F., *Ion-mediated nucleation in the atmosphere: Key controlling
   parameters, implications, and look-up table*, J. Geophys. Res., 115,
   D03206, `doi:10.1029/2009JD012630
   <https://doi.org/10.1029/2009JD012630>`_, 2010.
#. Yu, F., *A secondary organic aerosol formation model considering
   successive oxidation aging and kinetic condensation of organic
   compounds: global scale implications*, Atmos. Chem. Phys., 11,
   1083-1099, `doi:10.5194/acp-11-1083-2011
   <https://doi.org/10.5194/acp-11-1083-2011>`_, 2011.
#. Yu, F., *Diurnal and seasonal variations of ultrafine particle
   formation in anthropogenic SO2 plumes*, Environmental Science &
   Technology, 44 (6), 2011-2015, `doi:10.1021/es903228a
   <https://doi.org/10.1021/es903228>`_, 2010.
   #. Yu, F., and G. Luo, *Simulation of particle size distribution with a
   global aerosol model: Contribution of nucleation to aerosol and CCN
   number concentrations*, Atmos. Chem. Phys., **9**, 7691-7710, 2009.
#. Yu, F., and G. Luo, *Oceanic dimethyl sulfide emission and new
   particle formation around the coast of Antarctica: A modeling study
   of seasonal variations and comparison with measurements*, Atmosphere,
#. Yu, F., G. Luo, T. Bates, B. Anderson, A. Clarke, V. Kapustin, R.
   Yantosca, Y. Wang, S. Wu, *Spatial distributions of particle number
   concentrations in the global troposphere: Simulations, observations,
   and implications for nucleation mechanisms*, J. Geophys. Res., 115,
   D17205, `doi:10.1029/2009JD013473
   <https://doi.org/:10.1029/2009JD013473>`_, 2010.
#. Yu, F., G. Luo , R. P. Turco , J. Ogren , R. Yantosca, *Decreasing
   particle number concentrations in a warming atmosphere and
   implications*, Atmos. Chem. Phys. Discuss., 11, 27913-27936,
   `doi:10.5194/acpd-11-27913-2011
   <https://doi.org/10.5194/acpd-11-27913-2011>`_, 2011.
#. Yu, F., G. Luo, and X. Ma, *Regional and global modelling of aerosol
   optical properties with a size, composition, and mixing state
   resolved particle microphysics model*, Atmos. Chem. Phys., 12,
   5719-5736, `doi:10.5194/acp-12-5719-2012
   <https://doi.org/10.5194/acp-12-5719-2012>`_, 2012.
#. Yu, F.,X. Ma, and G. Luo, *Anthropogenic contribution to cloud
   condensation nuclei and the first aerosol indirect climate effect,
   Environ. Res. Lett.*, 8 024029 doi:10.1088/1748-9326/8/2/024029,
   `doi:10.1088/1748-9326/8/2/024029
   <https://doi.org/10.5194/acp-13-1853-2013>`_ 2013.
#. Yu, F. and Luo, G.: *Modeling of gaseous methylamines in the global
   atmosphere: impacts of oxidation and aerosol uptake*, Atmos. Chem.
   Phys., 14, 12455-12464, `doi:10.5194/acp-14-12455-2014
   <https://doi.org/10.5194/acp-14-12455-2014>`_, 2014.
