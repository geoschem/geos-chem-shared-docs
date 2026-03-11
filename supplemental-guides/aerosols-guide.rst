.. _aerguide:

#############
Aerosol types
#############

.. _aerguide-carbon:

=====================
Carbonaceous aerosols
=====================

`Park et al,
[2003]
<http://acmg.seas.harvard.edu/publications/2002/2002JD003190.pdf>`_
describes the original formulation of carbonaceous aerosols in GEOS-Chem:

   The simulation of carbonaceous aerosols in GEOS-Chem follows that of
   the Georgia Tech/Goddard Global Ozone Chemistry Aerosol Radiation and
   Transport (GOCART) model :cite:t:`Chin_et_al._2002`, with a number of
   modifications described below. The model resolves EC and OC, with a
   hydrophobic and a hydrophilic fraction for each (i.e., four aerosol
   types). Combustion sources emit hydrophobic aerosols that then become
   hydrophilic with an e-folding time of 1.2 days following
   :cite:t:`Cooke_et_al._1999`  and :cite:t:`Chin_et_al._2002`. We
   assume that 80% of EC and 50% of OC emitted from all primary
   sources are hydrophobic [:cite:t:`Cooke_et_al._1999`;
   :cite:t:`Chin_et_al._2002`, :cite:t:`Chung_and_Seinfeld_2002`. All
   secondary OC is assumed to be hydrophilic. The four aerosol types in
   the model are further resolved into contributions from fossil fuel,
   biofuel, and biomass burning, plus an OC component of biogenic
   origin, resulting in a total of 13 tracers transported by the model.

   Simulation of aerosol wet and dry deposition follows the schemes used
   by :cite:t:`Liu_et_al._2001` in previous GEOS-Chem simulations of
   :math:`^{210}Pb` and :math:`^{7}Be` aerosol tracers. Wet deposition
   includes contributions from scavenging in convective updrafts,
   rainout from convective anvils, and rainout and washout from
   large-scale precipitation. Wet deposition is applied only to the
   hydrophilic component of the aerosol. Dry deposition of aerosols
   uses a resistance-in-series model :cite:t:`Walcek_et_al._1986`
   dependent on local surface type and meteorological conditions; it
   is small compared to wet deposition. :cite:t:`Liu_et_al._2001`
   found no systematic biases in their simulations  of 210Pb and 7Be
   with GEOS-Chem.

   We use global anthropogenic emissions of EC (6.4 Tg year1) and OC
   (10.5 Tg year1) from the gridded :cite:t:`Cooke_et_al._1999`
   inventory for 1984.... :cite:t:`Cooke_et_al._1999` do not resolve
   the contributions to EC and OC emissions from heating fuel. We
   assume these contributions to represent 8% (EC) and 35% (OC) of
   total anthropogenic emissions, based on data for the Pittsburgh
   area from :cite:t:`Cabada_et_al._2002` and apply local seasonal
   variations of emissions using the heating degree
   days approach :cite:t:`EIA_2001`; :cite:t:`Cabada_et_al._2002`. In
   this manner we find that anthropogenic EC emission in the United
   States in winter is 15% higher than in summer. For OC the
   anthropogenic winter emission is twice that in summer.

   Biomass burning emissions of EC and OC are calculated using the
   global biomass burning inventory of :cite:t:`Duncan_et_al._2003`.

   Secondary formation of OC from oxidation of large hydrocarbons is an
   important source but uncertainties are large
   :cite:t:`Griffin_et_al._1999`; :cite:t:`Kanakidou_et_al._2000`;
   :cite:t:`Chung_and_Seinfeld_2002`. :cite:t:`Chung_and_Seinfeld_2002`
   find that biogenic terpenes are the main source of secondary OC
   aerosols. We assume a 10% carbon yield of OC from terpenes
   :cite:t:`Chin_et_al._2002`, and apply this yield to a global
   terpene emission inventory dependent on vegetation type, monthly
   adjusted leaf area index, and temperature
   :cite:t:`Guenther_et_al._1995`.

Since :cite:t:`Park_et_al._2003`, there have been several notable updates,
such as:

#. EC and OC biomass burning emissions now are taken from GFED instead
   of :cite:t:`Duncan_et_al._2003`.
#. GEOS-Chem now also has the option of adding several
   secondary organic aerosol species to the simulation.
#. Anthropogenic emissions of EC and OC now come from the CEDS
   inventory.

.. _aerguide-seasalt:

=================
Sea salt aerosols
=================

The treatment of sea salt aerosols in GEOS-Chem has had two major stages
of development:

#. Original formulation: :cite:t:`Alexander_et_al._2005`
#. Revised formulation: :cite:t:`Jaegle_et_al._2011`

There have also been several additional modifications as described in
the sections below.

.. _aerguide-seasalt-mw:

Updated molecular weight of sea salt tracers
--------------------------------------------

Molecular weights of sea salt species are 31.4 g/mol, which is
consistent with the actual average composition of sea salt, and
international guidelines from the `IAPWS
<http://www.iapws.org/relguide/seawater.pdf>`_.

.. _aerguide-seasalt-sst:

SST dependent sea salt emissions
--------------------------------

Sea salt emissions now include both a wind speed and sea surface
temperature (SST) dependence. The sea salt source function is based on
:cite:t:`Gong_2003`, which is based on :cite:t:`Monahan_et_al._1986`.
The :cite:t:`Gong_2003` formulation expresses the density function
:math:`dF/dr_{80}` (in units of particles :math:`m^{-2} s^{-1} \mu
m^{-1}` as follows:

.. figure:: ../_static/seasalt_gong.png
   :alt: gong.png

A and B are parameters depending on :math:`r_{80}`, the particle
radius at RH = 80% (with :math:`r_{80}` being close to twice the dry
radius of sea salt particles).

Based on a comparison of GEOS-Chem sea salt simulation with coarse mode
sea salt mass concentration observations obtained on 6 PMEL cruises, a
new SST dependent source function was derived
(:cite:t:`Jaegle_et_al._2011`):

.. figure:: ../_static/seasalt_sst_gong.png
   :alt: SST_gong.png

where :math:`T` is the SST expressed in degrees Celsius (valid
temperature range: 0 - 30C).

This new empirical source function leads to improved agreement of
GEOS-Chem relative to sea salt mass concentration observations from
cruises and ground-based stations, as well as AOD observations from
MODIS and AERONET.

Recommended size range for sea salt:

- Accumulation mode: :math:`0.01 - 0.5\,\mu m`
- Coarse mode: :math:`0.5 - 8\,\mu m`

Note that in :cite:t:`Jaegle_et_al._2011` we used 1 accumulation bin
(:math:`0.01 - 0.5\mu m`) and 2 coarse mode bins (:math:`0.5 - 4\,\mu
m`; :math:`4 - 10\,\mu m`). Due to the non-linearity of dry
deposition, using a single coarse bin :math:`0.5 - 10\,\mu m` leads to
an overestimate of the sea salt burden, hence we recommend using
:math:`0.5 - 8 \mu\,m`.

.. _aerguide-seasalt-drydep:

Updates to sea salt dry deposition
----------------------------------

Over land, sea salt dry deposition velocities are calculated using the
:cite:t:`Zhang_et_al._2001` scheme, which is based on the
:cite:t:`Slinn_1982` model for vegetated canopies. Over the oceans,
we have implemented the :cite:t:`Slinn_and_Slinn_1980` deposition
model for natural waters. Following the recommendation of
:cite:t:`Lewis_and_Schwartz_2004` we assume RH = 98% in the viscous
sublayer (0.1-1mm thick layer above the ocean surface). We integrate
the dry deposition velocity over each size bin using a bimodal size
distribution for sea salt (see below), which includes growth as a
function of local RH (see below).
Overall these changes lead to a factor of 3 increase in dry deposition
velocity for coarse mode sea salt and a factor of 2 decrease for
accumulation mode sea salt.

.. _aerguide-seasalt-hyg:

Updates to hygroscopic growth
-----------------------------

The hygroscopic growth of sea salt aerosols is based on Equation (5) in
:cite:t:`Lewis_and_Schwartz_2006`, which yields more accurate results
at RH > 98% :cite:t:`Gerber_1985` formulation previously used in
GEOS-Chem.

.. _aerguide-seasalt-optics:

Updates to optical properties
-----------------------------

The size distribution of accumulation mode sea salt aerosols assumes a
dry geometric radius :math:`r_{dg} = 0.085 \mu m` with a geometric
standard deviation :math:`2.03 \mu m`. This is based on cruises in the
remote Pacific Ocean (Quinn et al., 1996). For coarse mode sea salt
aerosols we use :math:`r_{dg} = 0.4 \mu m` with a geometric standard
deviation of :math:`1.8 \mu m` based on :cite:t:`Reid_et_al._2006`.

These size distributions are used in the Mie theory calculation of
extinction efficiency. They are also used in calculating the size
integrated dry deposition velocity of sea salt aerosols.

.. _aerguide-seasalt-impact:

Overall impact on distribution of sea salt
------------------------------------------

Implementing these changes leads to small changes in the mean global
burdens of accumulation mode (20% decrease) and coarse model (25%
increase) sea salt aerosols. However, the spatial changes are much
larger, with a 30-50% decrease at high latitudes and a factor of ~2
increase over tropical regions. See `this presentation
<https://drive.google.com/file/d/1F18TjBYpeJIY1sDtGlM1KBkZ9Neb2svM/view?usp=sharing>`_
for more information.

.. _aerguide-pm25:

====================================================
Computing PM2.5 concentrations from GEOS-Chem output
====================================================

For information on how to compute particulate matter (PM2.5) from
GEOS-Chem diagnostic outputs, please see our :ref:`pmguide` Guide.
