.. |br| raw:: html

   <br />

.. _pblguide:

###############################
Planetary boundary layer mixing
###############################

There are two planetary boundary layer (PBL) mixing schemes in
GEOS-Chem:

.. list-table::
   :header-rows: 1

   * - Scheme
     - Author
     - Description
     - Source code module
   * - TURBDAY
     - Dale Allen [#A]_
     - Full PBL mixing
     - :code:`pbl_mix_mod.F90`
   * - VDIFF
     - Jintai Lin [#B]_  [#C]_ |br|
       Michael McElroy [#B]_
     - Non-local PBL mixing scheme
     - :code:`vdiff_mod.F90`

.. rubric:: Affiliation

.. [#A] University of Maryland, USA

.. [#B] Harvard University, Cambridge, MA, USA

.. [#C] Peking University, Beijing, China

.. _pblguide-vdiff:

============================
VDIFF (non-local PBL mixing)
============================

The VDIFF scheme is the default PBL mixing scheme in GEOS-Chem.  To
use VDIFF, make sure you have the following settings in your
:ref:`cfg-gc-yml` file.

.. code-block:: yaml

   pbl_mixing:
     activate: true
     use_non_local_pbl: true

Description
-----------

Jintai Lin implemented a non-local PBL mixing scheme into
GEOS-Chem. It is a non-local scheme formulated by
:cite:t:`Holtslag_and_Boville_1993`. Unlike the full mixing assumption
where emissions,dry depositions and concentrations of individual
species are evenly distributed in the PBL (the depth of which being
taken from meteorological datasets such as GEOS-FP), the non-local
scheme considers different states of mixing within the PBL as
determined by the static instability.

- In the case of a **stable PBL** (e.g., at night), the scheme reduces
  to the well-known local scheme based on K-theory, and the derived
  mixing is weak — much weaker than full-mixing. |br|
  |br|

- In the case of an **unstable PBL** (e.g., on a typical hot summer
  afternoon), a **non-local** term is introduced to account for
  PBL-wide mixing triggered by large eddies. In an extremely unstable
  PBL, the magnitude of mixing approaches full-mixing.

The non-local scheme has been shown to simulate mixing of
meteorological parameters and chemical tracers reasonably well under
various PBL conditions, and is more realistic than the assumption of a
fully mixed PBL. Analysis of the two schemes is conducted by
:cite:t:`Lin_et_al._2008` and :cite:t:`Lin_and_McElroy_2010`.

How the non-local scheme works: it first calculates the PBL depth,
then the eddy diffusivity (:math:`K`) for tracers. :math:`K` is used
to derive tracer mixing. In the current GEOS-Chem setup, however, the
PBL height is taken from the meteorological datasets rather than being
derived within the scheme, in order to maintain consistency with those
datasets. Nonetheless, the user may enable the online calculation of
PBL height; this option is available in the code
:code:`GeosCore/vdiff_mod.F90`.

Validation
----------

See :cite:t:`Lin_and_McElroy_2010`.

.. _pblguide-turbday:

=========================
TURBDAY (full PBL mixing)
=========================

The TURBDAY mixing scheme was formerly the default mixing scheme in
GEOS-Chem but is now optional.  It can be used with all of the
different types of meteorological fields (GEOS-FP, MERRA-2, GEOS-IT,
GISS/GCAP2).

To use TURBDAY, make sure you have the following settings in your
:ref:`cfg-gc-yml` file.

.. code-block:: yaml

   pbl_mixing:
     activate: true
     use_non_local_pbl: false


The updated concentrations for each tracer N at grid boxes (I,J,L)
underneath the PBL top are computed as:

.. code-block:: none

   SPECIES(I,J,L,N),new  = Species(N)%(I,J,L),old + ( DTC(I,J,L,N) / AD(I,J,L) )

   where

   DTC(I,J,L,N) = [ ALPHA * (mean mixing ratio below PBL) * AD(I,J,L) ]
                - [ ALPHA *    (I,J,L,N),old           * AD(I,J,L) ]

   AD(I,J,L)    = Air mass at grid box (I,J,L)

   ALPHA        = Day/night mixing coefficients.
                  These are always 1, for full mixing at all times of day.

   DTC          = Change in mass (kg) due to BL mixing, therefore:

   DTC/AD       = Change in (v/v) mixing ratio units.

Validation
----------

See :cite:t:`Bey_et_al._2001` and :cite:t:`Wu_et_al._2007`.

.. _pblguide-pblh:

========================================
Difference Between PBLH and Mixing Depth
========================================

The GEOS meteorological fields from NASA GMAO provide the depth of the
**mixed layer** (mixing depth), not the planetary boundary layer height
(PBLH), even though the variable in the files is labelled PBLH. The
planetary boundary layer (PBL) is the layer of the atmosphere that
interacts with the surface on a timescale of a day or less. The free
troposphere has a general slow sinking motion, balancing the few
locations where deep convection or frontal lifting injects PBL air to
high altitudes. The compressional heating from this sinking air
produces a semi-permanent subsidence inversion that caps the PBL and
sharply restricts mixing between the PBL and the free troposphere.

After sunrise, surface heating erodes the stable residual layer from
below, producing an **unstable mixed layer** that grows over the
morning hours to eventually reach the full depth of the PBL. See
:cite:t:`Jacob_and_Brasseur_2016` for this discussion and
illustrations. When discussing PBL height in terms of mixing, what is
actually meant is the mixed layer--- and that is what is calculated by
the online PBLH (mixed layer) computation in
:cite:t:`Holtslag_and_Boville_1993` or in the NASA GEOS model.
