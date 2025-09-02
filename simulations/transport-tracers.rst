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

.. _transport-sim-references:

==========
References
==========

#. Liu, H., D. Jacob, I. Bey, and R.M. Yantosca, *Constraints from
   210Pb and 7Be on wet deposition and transport in a global
   three-dimensional chemical tracer model driven by assimilated
   meteorological fields*, J. Geophys. Res, 106, D11,
   12109-12128, 2001.
#. Jacob et al., *Evaluation and intercomparison of global atmospheric
   transport models using 222Rn and other short-lived
   tracers*, J. Geophys. Res, 102, 5953-5970, 1997.
#. Koch, D.M., D.J. Jacob, and W.C. Graustein, *Vertical transport of
   tropospheric aerosols as indicated by 7Be and 210Pb in a chemical
   tracer model*, J. Geophys. Res, 101, D13, 18651-18666, 1996.
#. Koch, D., and D. Rind, *Beryllium 10/beryllium 7 as a tracer of
   stratospheric transport*, J. Geophys. Res., 103, D4,
   3907-3917, 1998.
#. Lal, D., and B. Peters, *Cosmic ray produced radioactivity on the
   Earth*. Handbuch der Physik, 46/2, 551-612, edited by K. Sitte,
   Springer-Verlag, New York, 1967.

.. _transport-sim-species:

===============
List of species
===============

.. _transport-species-rn222:

Rn222
-----

- **Description:** Radon-222 isotope
- **Purpose:** Used to evaluate convection over land and strat-trop exchange.
- **Formula:** Rn222
- **Molecular weight (g):** 222.0
- **Source:** Emitted naturally from soils based on `Zhang et al., 2021 <https://acp.copernicus.org/articles/21/1861/2021/>`_.
- **Sinks:** Radioactive decay into :ref:`transport-species-pb210` with a
  half-life of 3.83 days (cf
  `Liu et al., 2021 <https://agupubs.onlinelibrary.wiley.com/doi/10.1029/2000JD900839>`_).

.. _transport-species-pb210:

Pb210
-----

- **Description:** Lead-210 isotope
- **Purpose:** Used to evaluate wet scavenging and transport.
- **Formula:** Pb210
- **Molecular weight (g):** 210.0
- **Source:** Radioactive decay from :ref:`transport-species-rn222`.
- **Sinks:**

  #. Radioactive decay with a half-life of 22.3 years (Liu et al., 2001).
  #. Wet deposition
  #. Dry deposition

.. _transport-species-pb210s:

Pb210s
------

- **Description:** Lead-210 isotope stratospheric-source tracer
- **Purpose:** Used to evaluate strat-trop exchange.
- All other properties are identical to :ref:`transport-species-pb210`.

.. _transport-species-be7:

Be7
---

- **Description:** Beryllium-7 isotope
- **Purpose:** Used to evaluate wet scavenging and strat-trop exchange.
- **Formula:** Be7
- **Molecular weight (g):** 7.0
- **Source:**  Produced by cosmic rays as described in `Lal and B. Peters, 1967
  <https://link.springer.com/chapter/10.1007/978-3-642-46079-1_7>`_,
  with the following modifications (cf. Liu et al, 2001):

  #. Use 1900 disintegrations / g air / s (instead of 3000) at 0 hPa
     altitude and 70°S latitude.
  #. The original Lal & Peters data ended at 70°S.  The values at 70°S
     were copied to 80°S and 90°S at all levels.

- **Sinks:**

  #. Radioactive decay with a half-life of 53.3 days (Liu et al., 2001).
  #. Wet deposition
  #. Dry deposition

.. _transport-species-be7s:

Be7s
----

- **Description:** Beryllium-7 isotope stratospheric-source tracer
- **Purpose:** Used to evaluate wet scavenging and strat-trop exchange.
- All other properties are the same as for :ref:`transport-species-be7`.

.. _transport-species-be10:

Be10
----

- **Description:** Beryllium-10 isotope
- **Purpose:** Used to evaluate wet scavenging and strat-trop exchange.
- **Formula:** Be7
- **Molecular weight (g):** 7.0
- **Source:** Identical to :ref:`transport-species-be7`.
- **Sinks:**

  #. Radioactive decay with a half-life of :math:`5.84 \times 10^{8}` days (Koch
     and Rind 1998).
  #. Wet deposition
  #. Dry deposition

.. _transport-species-be10s:

Be10s
-----

- **Description:** Beryllium-10 isotope stratospheric-source tracer
- **Purpose:** Used to evaluate wet scavenging and strat-trop exchange.
- All other properties are the same as for :ref:`transport-species-be10`.

.. _transport-species-aoa:

aoa
---

- **Description:** Age of air uniform source tracer
- **Purpose:** Used for evaluating residual circulation and mixing.
- **Molecular weight (g):** 1.0
- **Source:** Increases by a value of 1 each emissions timestep.
- **Sink:** At the surface.

.. _transport-species-aoabl:

aoa_bl
------

- **Description:** Age of air uniform source tracer with sink
  restricted to the boundary layer
- **Purpose:** Used for evaluating residual circulation and mixing.
- **Molecular weight (g):** 1.0
- **Source:** Increases by a value of 1 each emissions timestep.
- **Sink:** In the boundary layer.

.. _transport-species-aoanh:

aoa_nh
------

- **Description:** Age of air uniform source tracer with sink
  restricted to a zone in the Northern Hemisphere
- **Purpose:** Used for evaluating residual circulation and mixing.
- **Molecular weight (g):** 1.0
- **Source:** Increases by a value of 1 each emissions timestep.
- **Sink:** At 30°N - 50°N.

.. _transport-species-ch3i:

CH3I
----

- **Description:** Methyl iodide
- **Purpose:** Used to evaluate marine convection
- **Formula:** CH3I
- **Molecular weight (g):** 141.94
- **Source:** Emissions over the oceans of 1 molec/cm2/s
- **Sink:** 5-day e-folding lifetime

.. _transport-species-co25:

CO_25
-----

- **Description:** Anthropogenic CO 25 day tracer
- **Formula:** CO
- **Molecular weight (g):** 28.01
- **Source:** Anthropogenic emissions
- **Sink:** 25-day e-folding lifetime

.. _transport-species-co50:

CO_50
-----

- **Description:** Anthropogenic CO 50 day tracer
- **Formula:** CO
- **Molecular weight (g):** 28.01
- **Source:** Anthropogenic emissions
- **Sink:** 50-day e-folding lifetime

.. _transport-species-e90:

e90
---

- **Description:** Constant burden 90 day tracer
- **Molecular weight (g):** 1.0
- **Source:** Emitted globally at the surface such that the mixing ratio is maintained at 100 ppbv
- **Sink:** 90-day e-folding lifetime

.. _transport-species-e90n:

e90_n
-----

- **Description:** Constant burden Northern Hemisphere 90 day tracer
- **Molecular weight (g):** 1.0
- **Source:** Emitted at the surface such that the mixing ratio is maintained at 100 ppbv. Emissions source is restricted to 40°N - 90°N.
- **Sink:** 90-day e-folding lifetime

  .. _transport-species-e90s:

e90_s
-----

- **Description:** Constant burden Southern Hemisphere 90 day tracer
- **Molecular weight (g):** 1.0
- **Source:** Emitted at the surface such that the mixing ratio is maintained at 100 ppbv. Emissions source is restricted to 90°S - 40°S.
- **Sink:** 90-day e-folding lifetime

.. _transport-species-nh5:

nh_5
----

- **Description:** Northern Hemisphere 5 day tracer
- **Molecular weight (g):** 1.0
- **Source:** Constant source of 100 ppbv at latitudes 30°N - 50°N
- **Sink:** 5-day e-folding lifetime

.. _transport-species-nh50:

nh_50
-----

- **Description:** Northern Hemisphere 50 day tracer
- **Molecular weight (g):** 1.0
- **Source:** Constant source of 100 ppbv at latitudes 30°N - 50°N
- **Sink:** 50-day e-folding lifetime

.. _transport-species-pasv:

PassiveTracer
-------------

- **Description:** Passive tracer for mass conservation evaluation
- **Purpose:** Used to evaluate mass conservation in transport
- **Molecular weight (g):** 1.0
- **Source:** None
- **Sink:** None

.. _transport-species-sf6:

SF6
---

- **Description:** Sulfur hexafluoride
- **Purpose**: Used to evaluate inter-hemispheric transport of anthropogenic emissions
- **Formula:** SF6
- **Molecular weight (g):** 146.06
- **Source:** Anthropogenic emissions
- **Sink:** None

.. _transport-species-st8025:


st80_25
-------
- **Description:** Stratosphere source 25 day tracer
- **Molecular weight (g):** 1.0
- **Source:** Constant source of 200 ppbv above 80 hPa
- **Sink:** 25-day e-folding lifetime
