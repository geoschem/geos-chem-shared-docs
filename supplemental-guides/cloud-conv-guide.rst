.. _cloud-conv-guide:

################
Cloud convection
################

This page describes the cloud convection (aka wet convection)
algorithms of GEOS-Chem. We also invite you to read our `Wet
deposition
<https://wiki.seas.harvard.edu/geos-chem/index.php?title=Wet_deposition>`_
wiki page, which contains information about the algorithms used for
scavenging of soluble tracers.

.. _cloud-conv-guide-gf:

=========================
Grell-Freitas (GF) scheme
=========================

GEOS-Chem uses the Grell-Freitas (GF) convection scheme when being
driven by `GEOS-IT
<https://wiki.seas.harvard.edu/geos-chem/index.php?title=GEOS-IT>`_
meteorology (for all dates) and `GEOS-FP
<https://wiki.seas.harvard.edu/geos-chem/index.php?title=GEOS-FP>`_
meteorology (for dates on or after 01 June 2020). The source code for
this scheme is located in the :code:`DO_GF_CLOUD_CONVECTION` routine in
:file:`GeosCore/convection_mod.F90` (see `GitHub source
<https://github.com/geoschem/geos-chem/blob/3671d504cab09196ee960447a361b36ec41fe926/GeosCore/convection_mod.F90#L1388-L2237>`_).

.. _cloud-conv-guide-ras:

=====================================
Relaxed Arakawa-Schubert (RAS) scheme
=====================================

The Relaxed Arakawa-Schubert (RAS) convection scheme is used in
GEOS-Chem when driven by `GEOS-FP
<https://wiki.seas.harvard.edu/geos-chem/index.php?title=GEOS-FP>`_
meteorology (for dates prior to 01 June 2020) and `MERRA-2
<https://wiki.seas.harvard.edu/geos-chem/index.php?title=MERRA-2>`_
meteorology (for all dates). The source code for this scheme is
located in the :code:`DO_RAS_CLOUD_CONVECTION` routine in
:file:`GeosCore/convection_mod.F90`
(see `GitHub source
<https://github.com/geoschem/geos-chem/blob/3671d504cab09196ee960447a361b36ec41fe926/GeosCore/convection_mod.F90#L408-L1387>`_).

From Section 2, paragraph 10 of `Wu et al., [2007]
<https://drive.google.com/file/d/1S6T40p8IjTAgS7YuZDjcBvGWtMst4PJY/view?usp=drive_link>`_:

   A major difference between the GEOS-3, GEOS-4, and GISS models is
   the treatment of wet convection. GEOS-3 [and now also GEOS-5,
   *ed.*] uses the Relaxed Arakawa-Schubert convection scheme
   [*Moorthi and Suarez*, 1992]. GEOS-4 has separate treatments of
   deep  and shallow convection following the schemes developed by
   *Zhang and McFarlane* [1995] and *Hack* [1994]. The convection
   scheme in the GISS GCM was described by *Del Genio and Yao*
   [1993]. Unlike the GEOS models, the GISS GCM allows for condensed
   water in the atmosphere (i.e., condensed water is not immediately
   precipitated), resulting in frequent nonprecipitating shallow
   convection. In the wet deposition scheme, we do not scavenge
   soluble species from shallow convective updrafts at altitudes lower
   than 700 hPa in the GISS-driven model, whereas we do in the
   GEOS-driven model `Liu et al., [2001]
   <https://drive.google.com/file/d/1aLluvIsa5ftkw0A47Gz1FUlyvanxUjDM/view?usp=drive_link>`_.

Updraft scavenging of soluble tracers (as applied to both the Relaxed
Arakawa and Hack/Zhang-McFarlane schemes in GEOS-Chem) is described in
Section 1 of   `Jacob et al [2000]
<http://acmg.seas.harvard.edu/geos/wiki_docs/deposition/wetdep.jacob_etal_2000.pdf>`_
and in Section 2.3.1 of Liu et al., [2001].

For information about validation of the RAS convective scheme in
GEOS-Chem, see `Bey et al., [2001]
<https://drive.google.com/file/d/1T8NnbiPE9VWXJxuUsNrKzZfNvu10yw7W/view?usp=drive_link>`_,
`Liu et al., [2001]
<https://drive.google.com/file/d/1aLluvIsa5ftkw0A47Gz1FUlyvanxUjDM/view?usp=drive_link>`_, and
`Wu et al., [2007]
<https://drive.google.com/file/d/1S6T40p8IjTAgS7YuZDjcBvGWtMst4PJY/view?usp=drive_link>`_.

.. _cloud-conv-guide-references:

==========
References
==========

#. Allen, D.J, R.B. Rood, A.M. Thompson, and R.D. Hidson, *Three
   dimensional* :sup:`222`\ *Rn calculations using assimilated data and a convective mixing algorithm*, J. Geophys. Res, 101, 6871-6881, 1986a.

#. Allen, D.J. et al, *Transport induced interannual variability of
   carbon monoxide using a chemistry and transport model*, J. Geophys. Res, 101, 28,655-28-670, 1986b.

#. Bey I., D. J. Jacob, R. M. Yantosca, J. A. Logan, B. Field, A. M. Fiore,
   Q. Li, H. Liu, L. J. Mickley, and M. Schultz, *Global modeling of
   tropospheric chemistry with assimilated meteorology: Model
   description and evaluation*, J. Geophys. Res., 106,
   23,073-23,096, 2001. (`PDF
   <https://drive.google.com/file/d/1T8NnbiPE9VWXJxuUsNrKzZfNvu10yw7W/view?usp=drive_link>`_)

#. Del Genio, A. D., and M. Yao, *Efficient cumulus parameterization
   for long-term climate studies: The GISS scheme, in The
   Representation of Cumulus Convection in Numerical Models*,
   Meteorol. Monogr., 46, 181–184, 1993.

#. `GEOS-FP Convection Change (June 2020-onward) can have large
   impacts on surface concentrations #1409
   <https://github.com/geoschem/geos-chem/issues/1409>`_ at the
   GEOS-Chem Github repository

#. Hack, J.J., *Parameterization of moist convection in the National
   Center for Atmospheric Research Community Climate Model (CCM2)*,
   J. Geophys. Res., 99, 5551–5568, 1994.

#. Jacob, D.J. H. Liu, C.Mari, and R.M. Yantosca, *Harvard wet
   deposition scheme for GMI*, Harvard University Atmospheric
   Chemistry Modeling Group, revised March 2000. (`PDF
   <https://drive.google.com/file/d/1Lb7S_RAO2hEzR9sU6M_r1XOYffiph10U/view>`_)

#. Liu, H., et al. (2001), *Constraints from* :sup:`210`\ *Pb and*
   :sup:`7`\ *Be on wet deposition and transport in a global
   three-dimensional chemical tracer model driven by assimilated
   meteorological fields*,
   J. Geophys. Res., 106, 12,109–12,128. (`PDF
   <https://drive.google.com/file/d/1aLluvIsa5ftkw0A47Gz1FUlyvanxUjDM/view?usp=drive_link>`_)

#. Moorthi, S., and M. J. Suarez, *Relaxed Arakawa-Schubert: A
   parameterization of moist convection for general circulation
   models*, Mon. Weather Rev., 120, 978–1002, 1992.

#. Wu, S., L.J. Mickley, D.J. Jacob, J.A. Logan, R.M. Yantosca,
   and D. Rind, *Why are there large differences between models in
   global budgets of tropospheric ozone?*, J. Geophys. Res.,
   112, D05302, doi:10.1029/2006JD007801, 2007.
   (`PDF <https://drive.google.com/file/d/1S6T40p8IjTAgS7YuZDjcBvGWtMst4PJY/view?usp=drive_link>`_)

#. Zhang, G. J., and N. A. McFarlane, *Sensitivity of climate
   simulations to the parameterization of cumulus  convection in the
   Canadian Climate Centre general circulation model*,
   Atmos. Ocean, 33 (3), 407–446, 1995.
