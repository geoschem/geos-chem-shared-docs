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

From Section 2, paragraph 10 of :cite:t:`Wu_et_al._2007`.

   A major difference between the GEOS-3, GEOS-4, and GISS models is
   the treatment of wet convection. GEOS-3 [and now also GEOS-5,
   *ed.*] uses the Relaxed Arakawa-Schubert convection scheme
   (:cite:t:`Moorthi_and_Suarez_1992`). GEOS-4 has separate treatments
   of deep  and shallow convection following the schemes developed by
   :cite:t:`Zhang_and_MacFarlane_1995` and :cite:t:`Hack_1994`. The
   convection scheme in the GISS GCM was described by
   :cite:t:`Del_Genio_and_Yao_1993`. Unlike the GEOS models, the GISS
   GCM allows for condensed water in the atmosphere (i.e., condensed
   water is not immediately precipitated), resulting in frequent
   nonprecipitating shallow convection. In the wet deposition scheme,
   we do not scavenge soluble species from shallow convective updrafts
   at altitudes lower than 700 hPa in the GISS-driven model, whereas
   we do in the GEOS-driven model (:cite:t:`Liu_et_al._2001`).

Updraft scavenging of soluble tracers (as applied to both the Relaxed
Arakawa and Hack/Zhang-McFarlane schemes in GEOS-Chem) is described in
Section 1 of :cite:t:`Jacob_et_al._2000` and in Section 2.3.1 of
:cite:t:`Liu_et_al._2001`.

For information about validation of the RAS convective scheme in
GEOS-Chem, see :cite:t:`Bey_et_al._2001`, :cite:t:`Liu_et_al._2001`,
and :cite:t:`Wu_et_al._2007`.
