.. _ate-guide:

###################################
Aerosol Thermodynamical Equilibrium
###################################

This guide describes the aerosol thermodynamical equilbrium codes that
are implemented into GEOS-Chem.

.. _ate-guide-hetp:

===========================================
HETrogenerous-Vectorized-or-Parallel (HetP)
===========================================

:program:`HetP` is an aerosol thermodynamic equilibrium solver written
in modern Fortran based on ISORROPIA II (which is written in FORTRAN
77). HetP solves only the "forward" metastable state of the
NH4+/Na+/Ca2+/K+/Mg2+/SO42–/NO3–/Cl–/H2O system.

HetP replaced :ref:`ISORRPOPIA II <ate-guide-isorropia>` in GEOS-Chem
14.4.0.

From the abstract to Miller et al, [2024]:

   We describe a new Fortran computer program to solve the system of
   equations for the NH4–Na+–Ca2+–K+–Mg2+–SO–NO–Cl−–H2O system, based
   on the algorithms of ISORROPIA II. Specifically, the code solves
   the system of equations describing the “forward” (gas + aerosol
   input) metastable state but with algorithm improvements and
   corrections. These algorithm changes allow the code to deliver more
   accurate solution results in formal evaluations of accuracy of the
   roots of the systems of equations, while reducing processing time
   in practical applications by about 50%. The improved solution
   performance results from several implementation improvements
   relative to the original ISORROPIA algorithms. These improvements
   include (i) the use of the “interpolate, truncate and project”
   (ITP) root-finding approach rather than bisection, (ii) the
   allowance of search interval endpoints as valid roots at the onset
   of a search, (iii) the use of a more accurate method to solve
   polynomial subsystems of equations, (iv) the elimination of
   negative concentrations during iterative solutions, (v) corrections
   for mass conservation enforcement, and (vi) several code structure
   improvements. The new code may be run in either a “vectorization”
   mode wherein a global convergence criterion is used across multiple
   tests within the same chemical subspace or a “by case-by-case” mode
   wherein individual test cases are solved with the same convergence
   criteria. The latter approach was found to be more efficient on the
   compiler tested here, but users of the code are recommended to test
   both options on their own systems. The new code has been
   constructed to explicitly conserve the input mass for all species
   considered in the solver and is provided as open-source Fortran
   shareware.

**Reference**

- Miller, S.J, P.A. Makar, and C.J. Lee, *HETerogeneous vectorized or
  Parallel (HETPv1.0): an updated inorganic heterogeneous chemistry
  solver for the metastable-state
  NH4+–Na+–Ca2+–K+–Mg2+–SO42−–NO3−–Cl−–H2O system based on ISORROPIA
  II*, Geosci. Model Dev., 17, 6, 2197-2219,
  `doi:10.5194/gmd-17-2197-2024
  <https://gmd.copernicus.org/preprints/gmd-2023-159/>`_ , 2024.

.. _ate-guide-isorropia:

============
ISORROPIA II
============

:program:`ISORROPIAII` is an updated version of the original ISORROPIA
scheme [Fountoukis and Nenes, 2007], whose implementation into
GEOS-Chem is described by Pye et al., [2009].

ISORROPIA II was retired in GEOS-Chem 14.4.0, when it was replaced by
:ref:`HetP <ate-guide-hetp>`.

**References**

- Fountoukis, C., and A. Nenes *ISORROPIA II: A computationally
  efficient thermodynamic equilibrium model for
  K+-Ca2+-Mg2+-NH4+-Na+-SO42-NO3-Cl-H2O aerosols*, Atmos. Chem. Phys.,
  7(17), 4639-4659., 2007.
- Pye, H. O. T., H. Liao, S. Wu, L. J. Mickley, D. J.Jacob, D. K. Henze,
  and J. H. Seinfeld, *Effect of changes in climate and emissions on
  future sulfate-nitrate-ammonium aerosol levels in the United
  States*, J. Geophys. Res., 114, D01205, `doi:10.1029/2008JD010701
  <https://drive.google.com/file/d/1xB0xfZ20fD-i9tMSZPALmlKdocXdmDk7/view>`_,
  2009.
