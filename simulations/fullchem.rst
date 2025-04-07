.. _fullchem-sim:

###################
Fullchem simulation
###################

========
Overview
========

The GEOS-Chem  :program:`fullchem` (aka "full-chemistry") simulation
uses a detailed NOx-Ox-hydrocarbons-aerosols-halogens mechanism.

You may choose one of several fullchem simulation options at :ref:`run directory
creation time <rundir>`.

.. list-table::
   :header-rows: 1
   :align: left

   * - Option
     - Description
   * - :ref:`Standard <fullchem-sim-standard>`
     - The default full chemistry simulation.  Uses a simple SOA
       species and precursor.
   * - :ref:`Benchmark <fullchem-sim-benchmark>`
     - The configuration used for performing GEOS-Chem benchmark
       simulations.  Uses all of the species in the standard
       simulation, but also carries complex SOA species for diagnostic
       purposes.
   * - :ref:`Complex SOA <fullchem-sim-csoa>`
     - Replaces the simple SOA species and precursor with the Pye et
       al. 2010 complex SOA scheme.
   * - :ref:`Complex SOA with semi-volatile POA <fullchem-sim-svpoa>`
     - Replaces the simple SOA species and precursor with the Pye et
       al. 2010 complex SOA scheme, plus semi-volatile POA.
   * - :ref:`Marine POA <fullchem-sim-mpoa>`
     - Adds species MOPI (hydrophilic marine POA) and MOPO
       (hydrophobic marine POA).
   * - :ref:`Acid uptake on dust <fullchem-sim-aciduptake>`
     - Adds several species to track how acids are taken up by dust
       and other aerosols.
   * - :ref:`TOMAS <fullchem-sim-tomas>`
     - Replaces the default bulk aerosol scheme with TOMAS Aerosol
       Microphysics.
   * - :ref:`APM <fullchem-sim-apm>`
     - Replaces the default bulk aerosol scheme with the APM Aerosol
       Microphysics.
   * - :ref:`RRTMG <fullchem-sim-rrtmg>`
     - Enables the RRTMG radiative transfer model, which can be used
       to compute radiative forcings for certain species.

Most GEOS-Chem users typically run the :program:`Standard` simulation.

.. _fullchem-sim-species:

==========================
List of species, by option
==========================

.. _fullchem-sim-standard:

Standard
--------

The :program:`Standard` fullchem option uses the following species:

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - Formula
     - MW (g)
   * - A3O2
     - Primary peroxy radical from C3H8
     - CH3CH2CH2OO
     - 75.1
   * - ACR
     - Acrolein
     - C3H4O
     - 56.06
   * - ACRO2
     - Peroxy radical from ACR
     - C3H5O4
     - 105.07
   * - ACO3
     - Peroxyacetyl radical for APAN
     - C3H3O3
     - 87.054
   * - ACET
     - Acetone
     - CH3C(O)CH3
     - 58.09
   * - ACTA
     - Acetic acid
     - CH3C(O)OH
     - 60.06
   * - AERI
     - Iodine on aerosol
     - I
     - 126.9
   * - ALD2
     - Acetaldehyde
     - CH3CHO
     - 44.06
   * - ALK4
     - Lumped C4+C5 Alkanes
     - not listed
     - 58.12
   * - ALK4N1
     - Peroxy radical from ALK4N2
     - C4H8NO5
     - 150.13
   * - ALK4N2
     - Lumped alkyl nitrate from ALK4
     - RO2NO
     - 119.1
   * - ALK4O2
     - Peroxy radical from ALK4
     - C4H9O2
     - 89.13
   * - ALK4P
     - Peroxide from ALK4O2
     - CH3CH2CH2CH2OOH
     - 90.14
   * - ALK6
     - Lumped >= C6 Alkanes
     - C7H16
     - 100.2
   * - AONITA
     - Aerosol-phase organonitrates from aromatics
     - C6H6O6N
     - 189.12
   * - APAN
     - Peroxyacryloyl nitrate
     - C3H3NO5
     - 133.06
   * - AROMCHO
     - ACCOMECHO from MCM
     - C5H6O4
     - 130.1
   * - AROMRO2
     - hydroxy-peroxy radical from aromatics
     - C6H7O3
     - 127.0
   * - AROMP4
     - Generic C4 product of aromatics
     - C4H4O2
     - 68.08
   * - AROMP5
     - C5 unsaturated dicarbonyl
     - C5H6O2
     - 98.1
   * - AROMPN
     - Lumped PN from aromatics
     - C5H5NO8
     - 207.1
   * - AROMCO3
     - Lumped aromatic peroxyacetyl radical
     - C5H5O6
     - 161.09
   * - ATO2
     - Peroxy radical from acetone
     - CH3C(O)CH2O2
     - 89.08
   * - ATOOH
     - ATO2 peroxide
     - CH3C(O)CH2OOH
     - 90.09
   * - B3O2
     - B3O2
     - CH3CH(OO)CH3
     - 75.1
   * - BALD
     - Benzaldehyde
     - C7H6O
     - 106.12
   * - BENZ
     - Benzene
     - C6H6
     - 78.12
   * - BENZO
     - alkoxy radical from aromatics
     - C6H5O
     - 93.0
   * - BENZO2
     - peroxy radical from aromatics
     - C6H5O2
     - 109.0
   * - BENZP
     - Phenyl hydroperoxide
     - C6H6O2
     - 110.11
   * - Br
     - Atomic bromine
     - Br
     - 79.9
   * - Br2
     - Molecular Bromine
     - Br2
     - 159.8
   * - BrCl
     - Bromine chloride
     - BrCl
     - 115.45
   * - BrNO2
     - Nitryl bromide
     - BrNO2
     - 125.91
   * - BrNO3
     - Bromine nitrate
     - BrNO3
     - 141.91
   * - BrO
     - Bromine monoxide
     - BrO
     - 95.9
   * - BRO2
     - Peroxy radical from BENZ oxidation
     - C6H7O5
     - 159.13
   * - BrSALA
     - Fine sea salt bromine
     - Br
     - 79.9
   * - BrSALC
     - Coarse sea salt bromine
     - Br
     - 79.9
   * - BUTDI
     - Butenedial
     - C4H4O2
     - 84.07
   * - BUTN
     - C4H6 alkyl nitrate
     - C4H7NO4
     - 133.1
   * - BUTO2
     - peroxy radical from C4H6
     - C4H7O3
     - 103.097
   * - BZCO3
     - Acyl peroxy radical from benzaldehyde
     - C7H5O3
     - 137.0
   * - BZCO3H
     - Perbenzoic acid
     - C6H5CO3H
     - 138.12
   * - BZPAN
     - Peroxybenzoylnitrate
     - C7H5O5N
     - 183.12
   * - C2H2
     - Acetylene (aka Ethyne)
     - C2H2
     - 26.05
   * - C2H4
     - Ethylene
     - C2H4
     - 28.05
   * - C2H6
     - Ethane
     - C2H6
     - 30.08
   * - C3H8
     - Propane
     - C3H8
     - 44.11
   * - C4H6
     - 1,3-butadiene
     - C4H6
     - 54.09
   * - C4HVP1
     - C4 hydroxy-vinyl peroxy radicals from HPALDS
     - C4H7O3
     - 103.11
   * - C4HVP2
     - C4 hydroxy-vinyl peroxy radicals from HPALDS
     - C4H7O3
     - 103.11
   * - CCl4
     - Carbon tetrachloride
     - CCl4
     - 153.82
   * - CFC11
     - CFC-11
     - CCl3F
     - 137.37
   * - CFC12
     - CFC-12
     - CCl2F2
     - 120.91
   * - CFC113
     - CFC-113
     - C2Cl3F3
     - 187.38
   * - CFC114
     - CFC-114
     - C2Cl2F4
     - 170.92
   * - CFC115
     - CFC-115
     - C2ClF5
     - 154.47
   * - CH2Br2
     - Dibromomethane
     - CH2Br2
     - 173.83
   * - CH2Cl2
     - Dichloromethane
     - CH2Cl2
     - 84.93
   * - CH2I2
     - Diiodomethane
     - CH2I2
     - 267.84
   * - CH2IBr
     - Bromoiodomethane
     - CH2IBr
     - 220.84
   * - CH2ICl
     - Chloroiodomethane
     - CH2ICl
     - 176.38
   * - CH2O
     - Formaldehyde
     - CH2O
     - 30.03
   * - CH2OO
     - Criegee intermediate
     - CH2OO
     - 46.03
   * - CH3Br
     - Methyl bromide
     - CH3Br
     - 94.94
   * - CH3CCl3
     - Methyl chloroform
     - CH3CCl3
     - 133.35
   * - CH3CHOO
     - Criegee intermediate
     - CH3CHOO
     - 60.06
   * - CH3Cl
     - Chloromethane
     - CH3Cl
     - 50.45
   * - CH3I
     - Methyl iodide
     - CH3I
     - 141.94
   * - CH4
     - not listed
     - CH4
     - 16.04
   * - CHBr3
     - Bromoform
     - CHBr3
     - 252.73
   * - CHCl3
     - Chloroform
     - CHCl3
     - 119.35
   * - Cl
     - Atomic chlorine
     - Cl
     - 35.45
   * - Cl2
     - Molecular chlorine
     - Cl2
     - 70.9
   * - Cl2O2
     - Dichlorine dioxide
     - Cl2O2
     - 102.91
   * - ClNO2
     - Nitryl chloride
     - ClNO2
     - 81.45
   * - ClNO3
     - Chlorine nitrate
     - ClNO3
     - 97.45
   * - ClO
     - Chlorine monoxide
     - ClO
     - 51.45
   * - ClOO
     - Chlorine dioxide
     - ClOO
     - 67.45
   * - CO
     - not listed
     - CO
     - 28.01
   * - CO2
     - Carbon dioxide
     - CO2
     - 44.01
   * - CSL
     - Cresols
     - C7H8O
     - 108.14
   * - DMS
     - Dimethyl sulfide
     - (CH3)2S
     - 62.13
   * - EBZ
     - Ethylbenzene
     - C8H10
     - 106.167
   * - EOH
     - Ethanol
     - C2H5OH
     - 46.07
   * - ETHLN
     - Ethanol nitrate
     - CHOCH2ONO2
     - 105.06
   * - ETHN
     - hydroxy-nitrooxy-ethane
     - HOCH2CH2ONO2
     - 107.07
   * - ETHP
     - hydroxy-hydroperoxy-ethane
     - HOCH2CH2OOH
     - 78.07
   * - ETNO3
     - Ethyl nitrate
     - C2H5ONO2
     - 91.08
   * - ETO
     - alkoxy radical from ETOO
     - HOCH2CH2O
     - 61.06
   * - ETOO
     - peroxy radical from ethene
     - HOCH2CH2OO
     - 77.06
   * - ETO2
     - ETO2
     - CH3CH2OO
     - 61.07
   * - ETP
     - Ethylhydroperoxide
     - CH3CH2OOH
     - 62.08
   * - FURA
     - Furan
     - C4H4O
     - 68.07
   * - GCO3
     - Peroxyacetyl radical for PHAN
     - HOCH2CO3
     - 91.0428
   * - GLYC
     - Glycoaldehyde
     - HOCH2CHO
     - 60.06
   * - GLYX
     - Glyoxal
     - CHOCHO
     - 58.04
   * - H
     - Atomic hydrogen
     - H
     - 1.01
   * - H1211
     - Halon 1211, Freon 12B1
     - CBrClF2
     - 165.36
   * - H1301
     - Halon 1301, Freon 13B1
     - CBrF3
     - 148.91
   * - H2402
     - Halon 2402
     - C2Br2F4
     - 259.82
   * - H2O
     - Water vapor
     - H2O
     - 18.02
   * - H2O2
     - Hydrogen peroxide
     - H2O2
     - 34.02
   * - HAC
     - Hydroxyacetone
     - HOCH2C(O)CH3
     - 74.08
   * - HACTA
     - Hydroxyacetic/glycolic acid
     - HOCH2CO2H
     - 76.0514
   * - HBr
     - Hypobromic acid
     - HBr
     - 80.91
   * - HC5A
     - isoprene-4,1-hydroxyaldehyde
     - C5H8O2
     - 100.13
   * - HCFC123
     - HCFC-123, Freon 123
     - C2HCl2F3
     - 152.93
   * - HCFC141b
     - HCFC-141b, Freon 141b
     - C(CH3)Cl2F
     - 116.94
   * - HCFC142b
     - HCFC-142b, Freon 142b
     - C(CH3)ClF2
     - 100.5
   * - HCFC22
     - HCFC-22, Freon 22
     - CHClF2
     - 86.47
   * - HCl
     - Hydrochloric acid
     - HCl
     - 36.45
   * - HCOOH
     - Formic acid
     - HCOOH
     - 46.03
   * - HI
     - Hydrogen iodide
     - HI
     - 127.91
   * - HMHP
     - Hydroxymethyl hydroperoxide
     - HOCH2OOH
     - 64.05
   * - HMML
     - hydroxymethyl-methyl-a-lactone
     - C4H6O3
     - 102.1
   * - HMS
     - Hydroxymethanesulfonate
     - HOCH2SO3−
     - 111.1
   * - HNO2
     - Nitrous acid
     - HNO2
     - 47.01
   * - HNO3
     - Nitric acid
     - HNO3
     - 63.01
   * - HNO4
     - Peroxynitric acid
     - HNO4
     - 79.01
   * - HO2
     - Hydroperoxyl radical
     - HO2
     - 33.01
   * - HOBr
     - Hypobromous acid
     - HOBr
     - 96.91
   * - HOCl
     - Hypochlorous acid
     - HOCl
     - 52.45
   * - HOI
     - Hypoiodous acid
     - HOI
     - 143.89
   * - HONIT
     - 2nd gen monoterpene organic nitrate
     - not listed
     - 215.0
   * - HPALD1
     - d-4,1-C5-hydroperoxyaldehyde
     - C5H8O3
     - 116.13
   * - HPALD1OO
     - HPALD1OO
     - C5H7O5
     - 147.12
   * - HPALD2
     - d-1,4-C5-hydroperoxyaldehyde
     - C5H8O3
     - 116.13
   * - HPALD2OO
     - HPALD2OO
     - C5H7O5
     - 147.12
   * - HPALD3
     - b-2,1-C5-hydroperoxyaldehyde
     - C5H8O3
     - 116.13
   * - HPALD4
     - b-3,4-C5-hydroperoxyaldehyde
     - C5H8O3
     - 116.13
   * - HPETHNL
     - Hydroperoxy ethanal
     - HOOCH2CHO
     - 76.06
   * - I
     - Atomic iodine
     - I
     - 126.9
   * - I2
     - Molecular iodine
     - I2
     - 253.8
   * - I2O2
     - Diiodine dioxide
     - I2O2
     - 285.8
   * - I2O3
     - Diiodine trioxide
     - I2O3
     - 301.8
   * - I2O4
     - Diiodine tetraoxide
     - I2O4
     - 317.8
   * - IBr
     - Iodine monobromide
     - IBr
     - 206.9
   * - ICHE
     - Isoprene hydroxy-carbonyl-epoxides
     - C5H8O3
     - 116.13
   * - ICHOO
     - Peroxy radical from IEPOXD
     - C5H9O5
     - 149.14
   * - ICl
     - Iodine monochloride
     - ICl
     - 162.45
   * - ICN
     - Lumped isoprene carbonyl-nitrates
     - C5H7NO4
     - 145.13
   * - ICNOO
     - Peroxy radicals from ICN
     - C5H8NO7
     - 194.14
   * - ICPDH
     - Isoprene dihydroxy hydroperoxycarbonyl
     - C5H10O5
     - 150.15
   * - IDC
     - Lumped isoprene dicarbonyls
     - C5H6O2
     - 98.11
   * - IDCHP
     - Isoprene dicarbonyl hydroxy dihydroperoxide
     - C5H8O5
     - 148.13
   * - IDHDP
     - Isoprene dihydroxy dihydroperoxide
     - C5H12O6
     - 168.17
   * - IDHNBOO
     - Peroxy radicals from INPB
     - C5H10NO7
     - 196.16
   * - IDHNDOO1
     - Peroxy radicals from INPD
     - C5H10NO7
     - 196.16
   * - IDHNDOO2
     - Peroxy radicals from INPD
     - C5H10NO7
     - 196.16
   * - IDHPE
     - Isoprene dihydroxy hydroperoxy epoxide
     - C5H10O5
     - 150.15
   * - IDN
     - Lumped isoprene dinitrates
     - C5H8N2O6
     - 192.15
   * - IDNOO
     - IDNOO
     - C5H9N2O6
     - 241.14
   * - IEPOXA
     - trans-Beta isoprene epoxydiol
     - C4H10O3
     - 106.14
   * - IEPOXAOO
     - Peroxy radical from trans-Beta isoprene epoxydiol
     - C5H8O5
     - 149.14
   * - IEPOXB
     - cis-Beta isoprene epoxydiol
     - C4H10O3
     - 106.14
   * - IEPOXBOO
     - peroxy radical from cis-Beta isoprene epoxydiol
     - C5H9O5
     - 149.14
   * - IEPOXD
     - Delta isoprene epoxydiol
     - C4H10O3
     - 106.14
   * - IHN1
     - Isoprene-d-4,1-hydroxynitrate
     - C5H9NO4
     - 147.15
   * - IHN2
     - Isoprene-b-1,2-hydroxynitrate
     - C5H9NO4
     - 147.15
   * - IHN3
     - Isoprene-b-4,3-hydroxynitrate
     - C5H9NO4
     - 147.15
   * - IHN4
     - Isoprene-d-4,1-hydroxynitrate
     - C5H9NO4
     - 147.15
   * - IHOO1
     - Peroxy radical from OH addition to isoprene at C1
     - C5H9O3
     - 117.14
   * - IHOO4
     - Peroxy radical from OH addition to isoprene at C4
     - C5H9O3
     - 117.14
   * - IHPNBOO
     - Peroxy radicals from INPB
     - C5H10NO8
     - 212.16
   * - IHPNDOO
     - Peroxy radicals from INPD
     - C5H10NO8
     - 212.16
   * - IHPOO1
     - Peroxy radical from ISOPOOH
     - C5H11O6
     - 167.16
   * - IHPOO2
     - Peroxy radical from ISOPOOH
     - C5H11O6
     - 167.16
   * - IHPOO3
     - Peroxy radical from ISOPOOH
     - C5H11O6
     - 167.16
   * - INA
     - Alkoxy radical from INO2D
     - C5H8NO4
     - 146.14
   * - INDIOL
     - Generic aerosol-phase organonitrate hydrolysis product
     - not listed
     - 102.0
   * - INO
     - Nitrosyl iodide
     - INO
     - 156.91
   * - INO2B
     - beta-peroxy radicals from isoprene + NO3
     - C5H8NO5
     - 162.14
   * - INO2D
     - delta-peroxy radicals from isoprene + NO3
     - C5H8NO5
     - 162.14
   * - INPB
     - Lumped b-hydroperoxy isoprene nitrates
     - C5H9NO5
     - 163.15
   * - INPD
     - Lumped d-hydroperoxy isoprene nitrates
     - C5H9NO5
     - 163.15
   * - IO
     - Iodine monoxide
     - IO
     - 142.9
   * - IONITA
     - Aer-phase organic nitrate from isoprene precursors
     - not listed
     - 14.01
   * - IONO
     - Nitryl iodide
     - IONO
     - 172.91
   * - IONO2
     - Iodine nitrate
     - IONO2
     - 188.91
   * - IPRNO3
     - Isopropyl nitrate
     - C3H7ONO2
     - 105.11
   * - ISALA
     - Fine sea salt iodine
     - I
     - 126.9
   * - ISALC
     - Coarse sea salt iodine
     - I
     - 126.9
   * - ISOP
     - Isoprene
     - CH2=C(CH3)CH=CH2
     - 68.13
   * - ISOPNOO1
     - Peroxy radicals from IHN2
     - C5H10NO7
     - 196.16
   * - ISOPNOO2
     - Peroxy radicals from IHN3
     - C5H10NO7
     - 196.16
   * - ITCN
     - lumped isoprene tetrafunctional carbonylnitrates
     - C5H9NO7
     - 195.15
   * - ITHN
     - Lumped isoprene tetrafunctional hydroxynitrates
     - C5H11NO7
     - 197.17
   * - KO2
     - Peroxy radical from >3 ketones
     - C4H5O3
     - 101.09
   * - LBRO2H
     - Dummy species to track oxidation of BRO2 by HO2
     - not listed
     - 159.13
   * - LBRO2N
     - Dummy species to track oxidation of BRO2 by NO
     - not listed
     - 159.13
   * - LIMO
     - Limonene
     - C10H16
     - 136.26
   * - LIMO2
     - Peroxy radical from LIMO
     - C10H17O3
     - 185.27
   * - APINP
     - Hydroperoxide from APIN
     - C10H18O3
     - 186.28
   * - APINN
     - 1st gen organic nitrate from APIN
     - C10H17NO4
     - 215.28
   * - PINAL
     - Pinonaldehyde
     - C10H16O2
     - 186.28
   * - PINPAN
     - PAN from pinonaldehyde
     - C10H17NO4
     - 215.28
   * - PINONIC
     - Pinonic acid
     - C10H18O3
     - 186.28
   * - PINO3H
     - Pinonic peracid
     - C10H18O4
     - 186.28
   * - C96O2H
     - Peroxide from APIN 2nd gen
     - C9H16O3
     - 186.28
   * - C96N
     - Saturated 2nd gen monoterpene organic nitrate
     - C9H15NO4
     - 215.28
   * - BPINO
     - Ketone from BPIN
     - C9H14O
     - 186.28
   * - BPINN
     - Saturated 1st gen BPIN organic nitrate
     - C10H17NO4
     - 215.28
   * - BPINP
     - Peroxide from BPIN
     - C10H18O3
     - 186.28
   * - BPINOOH
     - 2nd-gen peroxide from BPIN
     - C9H14O3
     - 186.28
   * - BPINON
     - Saturated 2nd gen BPIN organic nitrate
     - C9H13NO4
     - 215.28
   * - LIMAL
     - Aldehyde from limonene
     - C10H16O2
     - 186.28
   * - LIMN
     - Saturated 1st gen limonene organic nitrate
     - C10H17NO4
     - 215.28
   * - LIMKET
     - Ketone from limonene
     - C10H16O2
     - 186.28
   * - LIMKB
     - 2nd gen ketone from limonene
     - C10H16O3
     - 186.28
   * - LIMNB
     - Saturated 1st gen LIMO organic nitrate
     - C10H15NO4
     - 215.28
   * - LIMPAN
     - PAN from LIMO
     - C10H17NO4
     - 215.28
   * - LIMO2H
     - Acid from LIMO
     - C10H18O3
     - 186.28
   * - LIMO3H
     - Peracid from LIMO
     - C10H18O4
     - 186.28
   * - MYRCO
     - Aldehyde or ketone from myrcene
     - C10H18O3
     - 186.28
   * - PIN
     - Saturated 1st gen monoterpene organic nitrate
     - C10H17NO4
     - 215.28
   * - APINO2
     - Peroxy radical from APIN
     - C10H17O3
     - 185.27
   * - PINO3
     - Acylperoxy radical from APIN
     - C10H17O3
     - 185.27
   * - C96O2
     - 2nd-gen peroxy radical from APIN
     - C10H17O3
     - 185.27
   * - BPINO2
     - Peroxy radical from BPIN
     - C10H17O3
     - 185.27
   * - BPINOO2
     - 2nd-gen peroxy radical from BPIN
     - C10H17O3
     - 185.27
   * - LIMKO2
     - 2nd-gen peroxy radical from LIMO
     - C10H17O3
     - 185.27
   * - LIMO3
     - Acylperoxy radical from LIMO
     - C10H17O3
     - 185.27
   * - LISOPOH
     - Dummy species to track oxidation of ISOP by OH
     - not listed
     - 68.13
   * - LISOPNO3
     - Dummy species to track oxidation of ISOP by NO3
     - not listed
     - 68.13
   * - LNRO2H
     - Dummy species to track oxidation of NRO2 by HO2
     - not listed
     - 159.17
   * - LNRO2N
     - Dummy species to track oxidation of NRO2 by NO
     - not listed
     - 159.17
   * - LTRO2H
     - Dummy species to track oxidation of TRO2 by HO2
     - not listed
     - 173.16
   * - LTRO2N
     - Dummy species to track oxidation of TRO2 by NO
     - not listed
     - 173.16
   * - LVOC
     - Gas-phase low-volatility non-IEPOX product of RIP ox
     - C5H14O5
     - 154.19
   * - LVOCOA
     - Aer-phase low-volatility non-IEPOX product of RIP ox
     - C5H14O5
     - 154.19
   * - LXRO2H
     - Dummy species to track oxidation of XRO2 by HO2
     - not listed
     - 187.19
   * - LXRO2N
     - Dummy species to track oxidation of XRO2 by NO
     - not listed
     - 187.19
   * - MACR
     - Methacrolein
     - CH2=C(CH3)CHO
     - 70.1
   * - MACR1OO
     - Peroxyacyl radical from MACR + OH
     - CH2=C(CH3)C(O)OO
     - 101.09
   * - MACR1OOH
     - Peracid from MACR
     - CH2=C(CH3)C(O)OOH
     - 102.1
   * - MACRNO2
     - Product of MCRHN + OH
     - C4H6NO7
     - 180.1
   * - MAP
     - Peroxyacetic acid
     - CH3C(O)OOH
     - 76.06
   * - MCO3
     - Peroxyacetyl radical
     - CH3C(O)OO
     - 75.05
   * - MCRDH
     - Dihydroxy-methacrolein
     - C4H8O3
     - 104.12
   * - MCRENOL
     - Lumped enols from MVK/MACR
     - C4H6O2
     - 86.1
   * - MCRHN
     - Nitrate from MACR
     - HOCH2C(ONO2)(CH3)CHO
     - 149.11
   * - MCRHNB
     - Nitrate from MACR
     - O2NOCH2C(OH)(CH3)CHO
     - 149.11
   * - MCRHP
     - Hydroxy-hydroperoxy-methacrolein
     - HOCH2C(OOH)(CH3)CHO
     - 120.12
   * - MCROHOO
     - Peroxy radical from MACR + OH
     - C4H7O4
     - 119.11
   * - MCT
     - Catechols and methyl catechols
     - C7H8O2
     - 124.0
   * - MEKCO3
     - not listed
     - C3H5O4
     - 105.07
   * - MEK
     - Methyl Ethyl Ketone
     - RC(O)R
     - 72.11
   * - MEKPN
     - MEK peroxyacetyl nitrate
     - C3H5NO6
     - 151.07
   * - MENO3
     - Methyl nitrate
     - CH3ONO2
     - 77.05
   * - MGLY
     - Methylglyoxal
     - CH3COCHO
     - 72.07
   * - MO2
     - Methylperoxy radical
     - CH3O2
     - 47.04
   * - MOH
     - Methanol
     - CH3OH
     - 32.05
   * - MONITA
     - Aer-phase organic nitrate from monoterpene precursors
     - not listed
     - 14.01
   * - MONITS
     - Saturated 1st gen monoterpene organic nitrate
     - C10H17NO4
     - 215.28
   * - MONITU
     - Unsaturated 1st gen monoterpene organic nitrate
     - C10H17NO4
     - 215.28
   * - MP
     - Methyl hydro peroxide
     - CH3OOH
     - 48.05
   * - MPAN
     - Peroxymethacroyl nitrate (PMN)
     - CH2=C(CH3)C(O)OONO2
     - 147.1
   * - MPN
     - Methyl peroxy nitrate
     - CH3O2NO2
     - 93.05
   * - MSA
     - Methyl sulfonic acid
     - CH4SO3
     - 96.1
   * - MTPA
     - a-pinene, b-pinene, sabinene, carene
     - not listed
     - 136.26
   * - MTPO
     - Terpinene, terpinolene, myrcene, ocimene, other monoterpenes
     - not listed
     - 136.26
   * - MVK
     - Methyl vinyl ketone
     - CH2=CHC(=O)CH3
     - 70.09
   * - MVKDH
     - dihydroxy-MVK
     - HOCH2CH2OHC(O)CH3
     - 105.13
   * - MVKHC
     - MVK hydroxy-carbonyl
     - C4H6O3
     - 102.1
   * - MVKHCB
     - MVK hydroxy-carbonyl
     - C4H6O3
     - 102.1
   * - MVKHP
     - MVK hydroxy-hydroperoxide
     - C4H8O4
     - 120.12
   * - MVKN
     - Nitrate from MVK
     - HOCH2CH(ONO2)C(=O)CH3
     - 149.12
   * - MVKOHOO
     - Peroxy radical from MVK + OH
     - C4H7O4
     - 119.11
   * - MVKPC
     - MVK hydroperoxy-carbonyl
     - OCHCH(OOH)C(O)CH3
     - 118.1
   * - N
     - Atomic nitrogen
     - N
     - 14.01
   * - N2O
     - Nitrous oxide
     - N2O
     - 44.02
   * - N2O5
     - Dinitrogen pentoxide
     - N2O5
     - 108.02
   * - NAP
     - Naphtalene/IVOC surrogate
     - C10H8
     - 128.18
   * - NIT
     - Inorganic nitrates
     - not listed
     - 62.01
   * - NITs
     - Inorganic nitrates on surface of seasalt aerosol
     - not listed
     - 31.4
   * - NO
     - Nitrogen oxide
     - NO
     - 30.01
   * - NO2
     - Nitrogen dioxide
     - NO2
     - 46.01
   * - NO3
     - Nitrate radical
     - NO3
     - 62.01
   * - NPHEN
     - Nitrophenols
     - C6H5NO3
     - 139.11
   * - NPRNO3
     - n-propyl nitrate
     - C3H7ONO2
     - 105.11
   * - NRO2
     - Peroxy radical from NAP oxidation
     - C10H7O2
     - 159.17
   * - O
     - Ground state atomic oxygen
     - O(3P)
     - 16.0
   * - O1D
     - Excited atomic oxygen (1D)
     - O(1D)
     - 16.0
   * - O3
     - Ozone
     - O3
     - 48.0
   * - OClO
     - Chlorine dioxide
     - OClO
     - 67.45
   * - OCS
     - Carbonyl sulfide
     - COS
     - 60.07
   * - OH
     - Hydroxyl radical
     - OH
     - 17.01
   * - OIO
     - Iodine dioxide
     - OIO
     - 158.9
   * - OLND
     - Monoterpene-derived NO3-alkene adduct
     - C10H16NO5
     - 230.27
   * - OLNN
     - Monoterpene-derived NO3 adduct
     - C10H16NO5
     - 230.27
   * - OTHRO2
     - Other C2 RO2 not from C2H6 oxidation
     - CH3CH2OO
     - 61.07
   * - PAN
     - Peroxyacetyl nitrate
     - CH3C(O)OONO2
     - 121.06
   * - PHAN
     - Peroxyhydroxyacetic nitric anhydride
     - C2H3NO6
     - 137.0483
   * - PHEN
     - Phenol
     - C6H6O
     - 94.11
   * - PIO2
     - Peroxy radical from MTPA
     - C10H17O3
     - 185.27
   * - PIP
     - Peroxide from MTPA
     - C10H18O3
     - 186.28
   * - PO2
     - Peroxy radical from propene
     - HOCH2CH(OO)CH3
     - 91.1
   * - PP
     - Peroxide from PO2
     - HOCH2CH(OOH)CH3
     - 92.11
   * - PPN
     - Lumped peroxypropionyl nitrate
     - CH3CH2C(O)OONO2
     - 135.08
   * - PRN1
     - Peroxy radical from propene + NO3
     - O2NOCH2CH(OO)CH3
     - 136.09
   * - PROPNN
     - Propanone nitrate
     - CH3C(=O)CH2ONO2
     - 119.08
   * - PRPE
     - Lumped >= C3 alkenes
     - C3H6
     - 42.09
   * - PRPN
     - Peroxide from PRN1
     - O2NOCH2CH(OOH)CH3
     - 137.11
   * - PYAC
     - Pyruvic acid
     - C3H4O3
     - 88.07
   * - R4N1
     - Peroxy radical from R4N2
     - C4H8NO5
     - 150.13
   * - R4N2
     - Lumped alkyl nitrate
     - RO2NO
     - 119.1
   * - R4O2
     - Peroxy radical from isoprene and MTPA alkyl generation
     - C4H9O2
     - 89.13
   * - R4P
     - Peroxide from R4O2
     - CH3CH2CH2CH2OOH
     - 90.14
   * - R7O2
     - Peroxy radical from ALK6
     - C7H15O2
     - 131.19
   * - R7N1
     - Peroxy radical from R7N2
     - C7H15NO5
     - 161.2
   * - R7P
     - Peroxide from R7O2
     - C7H16O2
     - 132.2
   * - R7N2
     - C7 Lumped alkyl nitrate
     - RO2NO
     - 161.2
   * - RA3P
     - Peroxide from A3O2
     - CH3CH2CH2OOH
     - 76.11
   * - RB3P
     - Peroxide from B3O2
     - CH3CH(OOH)CH3
     - 76.11
   * - RCHO
     - Lumped aldehyde >= C3
     - CH3CH2CHO
     - 58.09
   * - RCO3
     - Peroxypropionyl radical
     - CH3CH2C(O)OO
     - 89.08
   * - RCOOH
     - > C2 organic acids
     - C2H5C(O)OH
     - 74.09
   * - RIPA
     - 1,2-ISOPOOH
     - C5H10O3
     - 118.15
   * - RIPB
     - 4,3-ISOPOOH
     - C5H10O3
     - 118.15
   * - RIPC
     - d-1,4-ISOPOOH
     - C5H10O3
     - 118.15
   * - RIPD
     - d-4,1-ISOPOOH
     - C5H10O3
     - 118.15
   * - RNO3
     - Lumped aromatic nitrate
     - RO2NO
     - 203.15
   * - ROH
     - > C2 alcohols
     - C3H7OH
     - 60.11
   * - RP
     - Peroxide from RCO3
     - CH3CH2C(O)OOH
     - 90.09
   * - SALAAL
     - Accumulation mode sea salt alkalinity
     - not listed
     - 31.4
   * - SALCAL
     - Coarse mode sea salt alkalinity
     - not listed
     - 31.4
   * - SALACL
     - Chloride in Accumulation mode sea salt aerosol
     - not listed
     - 35.45
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
   * - SOAGX
     - Aerosol-phase glyoxal
     - C2H2O2
     - 58.04
   * - SOAIE
     - Aerosol-phase IEPOX
     - C5H10O3
     - 118.15
   * - STYR
     - Styrene
     - C8H8
     - 104.1491
   * - TLFUONE
     - Aromatic furanones
     - C5H6O2
     - 98.1
   * - TLFUO2
     - not listed
     - C5H7O5
     - 147.1
   * - TMB
     - Trimethylbenzenes
     - C8H10
     - 106.167
   * - TOLU
     - Toluene
     - C7H8
     - 92.15
   * - TRO2
     - Peroxy radical from TOLU oxidation
     - C7H9O5
     - 173.16
   * - XYLE
     - Xylene
     - C8H10
     - 106.18
   * - XRO2
     - Peroxy radical from TOLU oxidation
     - C8H11O5
     - 187.19
   * - PH2SO4
     - SO4 from gas-phase chemistry
     - not listed
     - 96.06
   * - PSO4AQ
     - SO4 from cloud chemistry
     - not listed
     - 96.06
   * - ZRO2
     - RO2 for making lumped aromatic nitrate
     - C7H9O5
     - 173.16
   * - H2
     - Molecular hydrogen
     - H2
     - 2.02
   * - N2
     - Molecular nitrogen
     - N2
     - 28.02
   * - O2
     - Molecular oxygen
     - O2
     - 32.0

.. _fullchem-sim-benchmark:

Benchmark
---------

The :program:`Benchmark` fullchem simulation option allows the
GEOS-Chem Support Team to perform benchmark simulations, which
document the performance and evolution of GEOS-Chem over time.

Benchmark simulations use all of the :ref:`Standard species
<fullchem-sim-standard>`, as well as the :ref:`Complex SOA species
<fullchem-sim-csoa>` listed below.  However, the complex
SOA species are carried solely for diagnostic purposes.

.. _fullchem-sim-csoa:

Complex SOA
-----------

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - MW (g)
   * - ASOA1
     - Lumped non-volatile aerosol products of light aromatics + IVOCs
     - 150.0
   * - ASOA2
     - Lumped non-volatile aerosol products of light aromatics + IVOCs
     - 150.0
   * - ASOA3
     - Lumped non-volatile aerosol products of light aromatics + IVOCs
     - 150.0
   * - ASOAN
     - Lumped non-volatile aerosol products of light aromatics + IVOCs
     - 150.0
   * - ASOG1
     - Lumped non-volatile gas products of light aromatics + IVOCs
     - 150.0
   * - ASOG2
     - Lumped non-volatile gas products of light aromatics + IVOCs
     - 150.0
   * - ASOG3
     - Lumped non-volatile gas products of light aromatics + IVOCs
     - 150.0
   * - TSOA0
     - Lumped semivolatile aerosol products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOA1
     - Lumped semivolatile aerosol products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOA2
     - Lumped semivolatile aerosol products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOA3
     - Lumped semivolatile aerosol products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOG0
     - Lumped semivolatile gas products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOG1
     - Lumped semivolatile gas products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOG2
     - Lumped semivolatile gas products of monoterpene +
       sesquiterpene oxidation
     - 150.0
   * - TSOG3
     - Lumped semivolatile gas products of monoterpene +
       sesquiterpene oxidation
     - 150.0

.. _fullchem-sim-svpoa:

Complex SOA with semi-volatile POA
----------------------------------

Fullchem simulations with the :program:`Complex SOA with semi-volatile
POA option` use all of the :ref:`Standard species
<fullchem-sim-standard>` and  :ref:`Complex SOA species
<fullchem-sim-csoa>`, plus these additional primary organic aerosol
species:

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - MW (g)
   * - OPOA1
     - Lumped aerosol product of SVOC oxidation
     - 12.01
   * - OPOA2
     - Lumped aerosol product of SVOC oxidation
     - 12.01
   * - OPOG1
     - Lumped gas product of SVOC oxidation
     - 12.01
   * - OPOG2
     - Lumped gas product of SVOC oxidation
     - 12.01
   * - POA1
     - Lumped aerosol primary SVOCs
     - 12.01
   * - POA2
     - Lumped aerosol primary SVOCs
     - 12.01
   * - POG1
     - Lumped gas primary SVOCs
     - 12.01
   * - POG2
     - Lumped gas primary SVOCs
     - 12.01

.. _fullchem-sim-mpoa:

Marine POA
----------

Fullchem simulations with the :program:`Marine POA` use all of the
:ref:`Standard species <fullchem-sim-standard>`, plus two additional
species.

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - Formula
     - MW (g)
   * - MOPI
     - Hydrophilic marine organic carbon aerosol
     - C
     - 12.01
   * - MOPO
     - Hydrophobic marine organic carbon aerosol
     - C
     - 12.01

.. _fullchem-sim-aciduptake:

Acid uptake on dust
-------------------

The :program:`Acid uptake on dust` simulation uses all of the
:ref:`Standard species <fullchem-sim-standard>` species, plus 12
additional transported species:

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - MW (g)
   * - DSTAL1
     - Dust alkalinity, Reff = 0.7 :math:`\mu\text{m}`
     - 29.0
   * - DSTAL2
     - Dust alkalinity, Reff = 1.4 :math:`\mu\text{m}`
     - 29.0
   * - DSTAL3
     - Dust alkalinity, Reff = 2.4 :math:`\mu\text{m}`
     - 29.0
   * - DSTAL4
     - Dust alkalinity, Reff = 4.5 :math:`\mu\text{m}`
     - 29.0
   * - NITD1
     - Nitrate on dust, Reff = 0.7 :math:`\mu\text{m}`
     - 29.0
   * - NITD2
     - Nitrate on dust, Reff = 1.4 :math:`\mu\text{m}`
     - 29.0
   * - NITD3
     - Nitrate on dust, Reff = 2.4 :math:`\mu\text{m}`
     - 29.0
   * - NITD4
     - Nitrate on dust, Reff = 4.5 :math:`\mu\text{m}`
     - 29.0
   * - SO4D1
     - Sulfate on dust, Reff = 0.7 :math:`\mu\text{m}`
     - 29.0
   * - SO4D2
     - Sulfate on dust, Reff = 1.4 :math:`\mu\text{m}`
     - 29.0
   * - SO4D3
     - Sulfate on dust, Reff = 2.4 :math:`\mu\text{m}`
     - 29.0
   * - SO4D4
     - Sulfate on dust, Reff = 4.5 :math:`\mu\text{m}`
     - 29.0

.. _fullchem-sim-tomas:

TOMAS aerosol microphysics
--------------------------

Fullchem simulations with :program:`TOMAS aerosol microphysics` use all
species listed in the :ref:`fullchem-sim-standard`, plus the species
listed below.  Note that the bulk dust species (DST1, DST2, DST3,
DST4) species are replaced with size-resolved dust species (DUST1 .. DUST40).

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - MW (g)
   * - AW01 .. AW40
     - Aerosol water, size bins 1 .. 40
     - 18.0
   * - DUST01 .. DUST40
     - Mineral dust, size bins 1 .. 40
     - 100.0
   * - ECIL01 .. ECIL40
     - Hydrophilic elemental carbon, size bins 1 .. 40
     - 12.01
   * - ECOB01 .. ECOB40
     - Hydrophobic elemental carbon, size bins 1 .. 40
     - 12.01
   * - H2SO4:
     - Sulfuric acid
     - 98.0
   * - NK01 .. NK40
     - Aerosol number, size bins 1..40
     - 1.0
   * - OCIL01 .. OCIL40
     - Hydrophilic organic carbon, size bins 1 .. 40
     - 12.01
   * - OCOB01 .. OCOB40
     - Hydrophilic organic carbon, size bins 1 .. 40
     - 12.01
   * - SF01 .. SF40
     - Sulfate aerosol, size bins 1 .. 40
     - 96.0
   * - SS01 .. SS40
     - Sea salt aerosol, size bins 1 .. 40
     - 58.5

You must request TOMAS aerosol microphysics at configuration time.
You may select either TOMAS with 15 size-resolved bins or with 40
size-resolved bins.  Please see :ref:`this section
<customguide-aer-mp>` for configuration and compilation instructions.

.. _fullchem-sim-apm:

APM aerosol microphysics
------------------------

Fullchem simulations with :program:`APM aerosol microphysics` use all
species listed in the :ref:`fullchem-sim-standard`, as well as the
following species:

.. list-table::
   :header-rows: 1
   :align: left

   * - Species
     - Description
     - MW (g)
   * - APMAMINE1
     - APM amines 1
     - 31.0
   * - APMAMINE2
     - APM amines 2
     - 45.0
   * - APMAMINE3
     - APM amines 3
     - 59.0
   * - APMBCBIN01 .. APMBCBIN15
     - APM black carbon, size bins 01 .. 15
     - 12.01
   * - APMCTBC1
     - APM CTSO4
     - 96.0
   * - APMCTBC2
     - APM CTSLVSOA
     - 181.0
   * - APMCTDST1
     - APM CTSO4
     - 96.0
   * - APMCTDST2
     - APM CTLVSOA
     - 181.0
   * - APMCTOC1
     - APM CTSO4
     - 96.0
   * - APMCTOC2
     - APM CTLVSOA
     - 181.0
   * - APMCTSEA1
     - APM CTSO4
     - 96.0
   * - APMCTSEA2
     - APM CTLVSOA
     - 181.0
   * - APMDSTBIN01 .. APMDSTBIN15
     - APM Dust
     - 29.0
   * - APMH2SO4
     - APM sulfuric acid
     - 98.0
   * - APMLVSOA
     - APM LVSOA
     - 181.0
   * - APMLVSOG
     - APM LV secondary organic gas
     - 181.0
   * - APMOCBIN01 .. APMOCBIN15
     - APM organic carbon, size bins 01 .. 15
     - 12.01
   * - APMSEABIN01 .. APMSEABIN20
     - APM sea salt, size bins 01 .. 20
     - 12.01
   * - APMSPBIN01 .. APMSPBIN40
     - APM sulfate, size bins 01 .. 40
     - 96.0

You must request APM aerosol microphysics at configuration time.
Please see :ref:`this section <customguide-aer-mp>` for configuration
and compilation instructions.

.. _fullchem-sim-rrtmg:

RRTMG radiative transfer model
------------------------------

Fullchem simulations with the :program:`RRTMG radiative transfer
model` use the :ref:`Standard species <fullchem-sim-standard>`.  No
additional species are added.  Radiative forcing diagnostics can be
archived to the:ref:`RRTMG History collection <histguide-rrtmg>`.
