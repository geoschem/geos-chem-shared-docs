.. _hg-sim:

#######################
Hg (mercury) simulation
#######################

.. _hg-sim-overview:

========
Overview
========

From the abstract to Shah et al. 2021:

   We present a new chemical mechanism for HgO, HgI/HgII atmospheric
   cycling, including recent laboratory and computational data, and
   implement it in the GEOS-Chem global atmospheric chemistry model
   for comparison to observations. Our mechanism includes the
   oxidation of Hg0 by Br and OH, subsequent oxidation of HgI by ozone
   and radicals, respeciation of HgII in aerosols and cloud droplets,
   and speciated HgII photolysis in the gas and aqueous phases. The
   tropospheric Hg lifetime against deposition in the model is 5.5
   months, consistent with observational constraints. The model
   reproduces the observed global surface Hg0 concentrations and HgII
   wet deposition fluxes. Br and OH make comparable contributions to
   global net oxidation of Hg0 to HgII. Ozone is the principal HgI
   oxidant, enabling the efficient oxidation of Hg0 to HgII by
   OH. BrHgIIOH and HgII(OH)2, the initial HgII products of Hg0
   oxidation, respeciate in aerosols and clouds to organic and
   inorganic complexes, and volatilize to photostable forms. Reduction
   of HgII to Hg0 takes place largely through photolysis of aqueous
   HgII–organic complexes. 71% of model HgII deposition is to the
   oceans. Major uncertainties for atmospheric Hg chemistry modeling
   include Br concentrations, stability and reactions of HgI, and
   speciation and photoreduction of HgII in aerosols and clouds.

**Reference**: Shah, V., D.J. Jacob, C.P. Thackray, X. Wang, E.M. Sunderland,
T.S. Dibble, A. Saiz-Lopez, I. Cernusak, V. Kello, P.J. Castro, R. Wu,
and C. Wang, `Improved mechanistic model of the atmospheric redox
chemistry of mercury`, Environ. Sci. Technol., 55, 14445-14456, 2021,
DOI: `10.1021/acs.est.1c03160
<https://doi.org/10.1021/acs.est.1c03160>`_

.. _hg-sim-species:

===============
List of species
===============

NOTE: Mercury species are given the molecular weight of elemental mercury
(200.59 g/mol).

.. list-table:: Transported species
   :header-rows: 1
   :align: center
   :widths: 20 50 15 15

   * - Species
     - Description (IUPAC name)
     - Formula
     - MW (g)
   * - Hg0
     - Elemental mercury
     - Hg
     - 200.59
   * - HgBr
     - Bromidomercury(I)
     - HgBr
     - 200.59
   * - HgBrNO2
     - syn-Bromido(nitrito-O)mercury(II)
     - BrHgONO
     - 200.59
   * - HgBrHO2
     - Bromido(hydroperoxo)mercury(II)
     - BrHgOOH
     - 200.59
   * - HgBrBrO
     - Bromido(hypobromito-O)mercury(II)
     - BrHgOBr
     - 200.59
   * - HgBrClO
     - Bromido(hypochlorito-O)mercury(II)
     - BrHgOCl
     - 200.59
   * - HgBrOH
     - Bromido(hydroxido)mercury(II)
     - BrHgOH
     - 200.59
   * - HgBr2
     - Dibromidomercury(II)
     - HgBr2
     - 200.59
   * - HgCl
     - Chloridomercury(I)
     - HgCl
     - 200.59
   * - HgClNO2
     - syn-Chlorido(nitrito-kO)mercury(II)
     - ClHgONO
     - 200.59
   * - HgClHO2
     - Chlorido(hydroperoxido)mercury(II)
     - ClHgOOH
     - 200.59
   * - HgClClO
     - Chlorido(hypochlorito-kO)mercury(II)
     - ClHgOCl
     - 200.59
   * - HgClBrO
     - Chlorido(hypobromito-kO)mercury(II)
     - ClHgOBr
     - 200.59
   * - HgClBr
     - Bromidochloridomercury(II)
     - HgBrCl
     - 200.59
   * - HgClOH
     - Chlorido(hydroxido)mercury(II)
     - ClHgOH
     - 200.59
   * - HgOH
     - Hydroxidomercury(I)
     - HgOH
     - 200.59
   * - HgOHNO2
     - syn-Hydroperoxido(hydroxido)mercury(II)
     - HOHgONO
     - 200.59
   * - HgOHHO2
     - Hydroperoxo(hydroxido)mercury(II)
     - HOHgOOH
     - 200.59
   * - HgOHClO
     - Hydroxido(hypochlorito-O)mercury(II)
     - HOHgOCl
     - 200.59
   * - HgOHBrO
     - Hydroxido(hypobromito-O)mercury(II)
     - HOHgOBr
     - 200.59
   * - HgOHOH
     - Dihydroxidomercury(II)
     - HOHgOH
     - 200.59
   * - HgCl2
     - Dichloridomercury(II)
     - HgCl2
     - 200.59
   * - Hg2ClP
     - Hg(II) chloride salts on sea-salt aerosols
     - HgCln
     - 200.59
   * - Hg2ORGP
     - Hg(II) organic complex in aerosols
     - R-Hg
     - 200.59
   * - Hg2STRP
     - Hg(II) in stratospheric aerosols
     - Hg2+
     - 200.59

.. list-table:: Non-transported species
   :header-rows: 1
   :align: left
   :widths: 20 40 20 20

   * - Species
     - Description (IUPAC name)
     - Formula
     - MW (g)
   * - HgBrO
     - Hypobromitomercury(I)
     - HgBrO
     - 200.59
   * - HgClO
     - Hypochloritomercury(I)
     - ClHgO
     - 200.59
   * - HgOHO
     - Hydroperoxomercury(I)
     - HgOHO
     - 200.59

.. list-table:: Fixed species (external inputs)
   :header-rows: 1
   :align: left
   :widths: 20 40 20 20

   * - Species
     - Description
     - Formula
     - MW (g)
   * - Br
     - Atomic bromine
     - Br
     - 79.9
   * - Cl
     - Atomic chlorine
     - Cl
     - 35.45
   * - OH
     - Hydroxyl radical
     - OH
     - 17.01
   * - NO2
     - Nitrogen dioxide
     - NO2
     - 46.01
   * - NO
     - Nitrogen oxide
     - NO
     - 30.01
   * - O3
     - Ozone
     - O3
     - 48.0
   * - HO2
     - Hydroperoxyl radical
     - HO2
     - 33.01
   * - BrO
     - Bromine monoxide
     - BrO
     - 95.9
   * - ClO
     - Chlorine monoxide
     - ClO
     - 51.45
   * - CO
     - Carbon monoxide
     - CO
     - 28.01
   * - CH4
     - Methane
     - CH4
     - 16.04
