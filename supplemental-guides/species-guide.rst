.. _spcguide:

#################################
View GEOS-Chem species properties
#################################

Properties for GEOS-Chem species are stored in the **GEOS-Chem
Species Database**, which is a `YAML <https://yaml.org>`_ file
(:file:`species_database.yml`) that is placed into each GEOS-Chem run
directory.

View species properties from the current stable GEOS-Chem version:

- `View properties for most GEOS-Chem species <https://github.com/geoschem/geos-chem/blob/main/run/shared/species_database.yml>`_
- `View properties for APM microphysics species <https://github.com/geoschem/geos-chem/blob/main/run/shared/species_database_apm.yml>`_
- `View properties for TOMAS microphysics species <https://github.com/geoschem/geos-chem/blob/main/run/shared/species_database_tomas.yml>`_
- `View properties for Hg simulation species <https://github.com/geoschem/geos-chem/blob/main/run/shared/species_database_hg.yml>`_

Read more about species properties contained in the GEOS-Chem Species Database:

.. _spcguide-defaults:

=========================
Requried default settings
=========================

All GEOS-Chem species should have these properties defined:

.. code-block:: YAML

         Name:
           FullName: full name of the species
           Formula: chemical formula of the species
           MW_g: molecular weight of the species
   EITHER  Is_Gas: true
   OR      Is_Aerosol: true

All other properties are species-dependent.  You may omit properties
that do not apply to a given species. GEOS-Chem will assign a "missing
value" (e.g. :code:`false`, :code:`-999`, :code:`-999.0`, or,
:code:`UNKNOWN`) to these properties when it reads the
:file:`species_database.yml` file from disk.

.. _spcguide-id:

==============
Identification
==============

.. option:: Name

   Species short name (e.g. :literal:`ISOP`).

.. option:: Formula

   Species chemical formula (e.g. :literal:`CH2=C(CH3)CH=CH2`).  This
   is used to define the species' :literal:`formula` attribute, which
   gets written to GEOS-Chem diagnostic files and restart files.

.. option:: FullName

   Species long name (e.g. :literal:`Isoprene`).  This is used to
   define the species' :literal:`long_name` attribute, which gets
   written to GEOS-Chem diagnostic files and restart files.

.. option:: Is_Aerosol

   Indicates that the species is an aerosol (:literal:`true`), or isn't
   (:literal:`false`).

.. option:: Is_Advected

   Indicates that the species is advected (:literal:`true`), or isn't
   (:literal:`false`).

.. option:: Is_DryAlt

   Indicates that dry deposition diagnostic quantities for the species can
   be archived at a specified altitude above the surface
   (:literal:`true`), or can't (:literal:`false`).

   .. note::

      The :code:`Is_DryAlt` flag only applies to species
      :literal:`O3` and :literal:`HNO3`.

.. option:: Is_DryDep

   Indicates that the species is dry deposited (:literal:`true`), or
   isn't (:literal:`false`).

.. option:: Is_HygroGrowth

   Indicates that the species is an aerosol that is capable of
   hygroscopic growth (:literal:`true`), or isn't (:literal:`false`).

.. option:: Is_Gas

   Indicates that the species is a gas (:literal:`true`), or isn't
   (:literal:`false`).

.. option:: Is_Hg0

   Indicates that the species is elemental mercury (:literal:`true`),
   or isn't (:literal:`false`).

.. option:: Is_Hg2

   Indicates that the species is a mercury compound with oxidation
   state +2 (:literal:`true`), or isn't (:literal:`false`).

.. option:: Is_HgP

   Indicates that the species is a particulate mercury compound
   (:literal:`true`), or isn't (:literal:`false`).

.. option:: Is_Photolysis

   Indicates that the species is photolyzed (:literal:`true`), or isn't
   (:literal:`false`).

.. option:: Is_RadioNuclide

   Indicates that the species is a radionuclide (:literal:`true`), or
   isn't (:literal:`false`).

.. _spcguide-physprop:

===================
Physical properties
===================

.. option:: Density

   Density (:math:`kg\ m^{-3}`) of the species.  Typically defined
   only for aerosols.

.. option:: Henry_K0

   Henry's law solubility constant (:math:`M\ atm^{-1}`), used by the
   default wet depositon scheme.

.. option:: Henry_K0_Luo

   Henry's law solubility constant (:math:`M\ atm^{-1}`) used by the
   :cite:t:`Luo_et_al._2020` wet deposition scheme.

.. option:: Henry_CR

   Henry's law volatility constant (:math:`K`) used by the default
   wet deposition scheme.

.. option:: Henry_CR_Luo

   Henry's law volatility constant (:math:`K`) used by the
   :cite:t:`Luo_et_al._2020` wet deposition scheme.

.. option:: Henry_pKa

   Henry's Law pH correction factor.

.. option:: MW_g

   Molecular weight (:math:`g\ mol^{-1}`) of the species.

.. option:: Radius

   Radius (:math:`m`) of the species.  Typically defined only for
   aerosols.

.. _spcguide-drydep:

=========================
Dry deposition properties
=========================

.. option:: DD_AeroDryDep

   Indicates that dry deposition should consider hygroscopic growth
   for this species (:literal:`true`), or shouldn't
   (:literal:`false`).

   .. note::

     :code:`DD_AeroDryDep` is only defined for sea salt aerosols.

.. option:: DD_DustDryDep

   Indicates that dry deposition should exclude hygroscopic growth for
   this species (:literal:`true`), or shouldn't (:literal:`false`).

   .. note::

     :code:`DD_DustDryDep` is only defined for mineral dust
     aerosols.

.. option:: DD_DvzAerSnow

   Specifies the dry deposition velocity (:math:`cm\ s^-{1}`) over
   ice and snow for certain aerosol species.  Typically,
   :code:`DD_DvzAerSnow = 0.03`.

.. option:: DD_DvzAerSnow_Luo

   Specifies the dry deposition velocity (:math:`cm s^-{1}`) over
   ice and snow for certain aerosol species.

   .. note::

      :code:`DD_DvzAerSnow_Luo` is only used when the
      :cite:t:`Luo_et_al._2020` wet scavenging scheme is activated.

.. option:: DD_DvzMinVal

   Specfies minimum dry deposition velocities (:math:`cm s^-{1}`) for
   sulfate  species (:literal:`SO2`, :literal:`SO4`, :literal:`MSA`,
   :literal:`NH3`, :literal:`NH4`, :literal:`NIT`).  This follows the
   methodology of the GOCART model.

   :code:`DD_DvzMinVal` is defined as a two-element vector:

   - :code:`DD_DvzMinVal(1)` sets a minimum dry deposition velocity
     onto snow and ice.
   - :code:`DD_DvzMinVal(2)` sets a minimum dry deposition velocity
     over land.

.. option:: DD_Hstar_Old

   Specifies the Henry's law constant (:math:`K_0`) that is used in
   dry deposition.  This will be used to assign the :code:`HSTAR`
   variable in the GEOS-Chem dry deposition module.

   .. note::

      The value of the :code:`DD_Hstar_old` parameter was tuned for
      each species so that the dry deposition velocity would match
      observations.

.. option:: DD_F0

   Specifies the reactivity factor for oxidation of biological
   substances in dry deposition.

.. option:: DD_KOA

   Specifies the octanal-air partition coefficient, used for the dry
   deposition of species :math:`POPG`.

   .. note::

      :code:`DD_KOA` is only used in the `POPs simulation
      <https://wiki.geos-chem.org/POPs_simulation>`_.

.. _spcguide-wetdep:

=========================
Wet deposition properties
=========================

.. option:: WD_Is_H2SO4

   Indicates that the species is :literal:`H2SO4` (:literal:`true`),
   or isn't (:literal:`false)`.  This allows the wet deposition code
   to perform special calculations when computing  :literal:`H2SO4`
   rainout and washout.

.. option:: WD_Is_HNO3

   Indicates that the species is :literal:`HNO3` (:literal:`true`),
   or isn't (:literal:`false)`.  This allows the wet deposition code
   to perform special calculations when computing  :literal:`HNO3`.
   rainout and washout.

.. option:: WD_Is_SO2

   Indicates that the species is :literal:`SO2` (:literal:`true`),
   or isn't (:literal:`false)`.  This allows the wet deposition code
   to perform special calculations when computing :literal:`SO2`
   rainout and washout.

.. option:: WD_CoarseAer

   Indicates that the species is a coarse aerosol (:literal:`true`),
   or isn't (:literal:`false`).  For wet deposition purposes, the
   definition of coarse aerosol is radius > 1 :math:`\mu m`.

.. option:: WD_LiqAndGas

   Indicates that the the ice-to-gas ratio can be computed for
   this species by co-condensation (:literal:`true`), or can't
   (:literal:`false`).

.. option:: WD_ConvFacI2G

   Specifies the conversion factor (i.e. ratio of sticking
   coefficients on the ice surface) for computing the ice-to-gas ratio
   by co-condensation, as used in the default wet deposition scheme.

   .. note::

      :code:`WD_ConvFacI2G` only needs to be defined for those species
      for which :code:`WD_LiqAndGas` is :literal:`true`.

.. option:: WD_ConvFacI2G_Luo

   Specifies the conversion factor (i.e. ratio of sticking
   coefficients on the ice surface) for computing the ice-to-gas ratio
   by co-condensation, as used in the :cite:t:`Luo_et_al._2020` wet
   deposition scheme.

   .. note::

      :code:`WD_ConvFacI2G_Luo` only needs to be defined for those species
      for which :code:`WD_LiqAndGas` is :literal:`true`, and is only
      used when the :cite:t:`Luo_et_al._2020` wet deposition scheme is
      activated.

.. option:: WD_RetFactor

   Specifies the retention efficiency :math:`R_i` of species in the
   liquid cloud condensate as it is converted to precipitation.
   :math:`R_i` < 1 accounts for volatization during riming.

.. option:: WD_AerScavEff

   Specifies the aerosol scavenging efficiency. This factor multiplies
   :math:`F`, the fraction of aerosol species that is lost to
   convective updraft scavenging.

   - :code:`WD_AerScavEff = 1.0` for most aerosols.
   - :code:`WD_AerScavEff = 0.8` for secondary organic aerosols.
   - :code:`WD_AerScavEff = 0.0` for hydrophobic aerosols.

.. option:: WD_KcScaleFac

   Specifies a temperature-dependent scale factor that is used to
   multiply :math:`K` (aka :math:`K_c`), the rate constant for
   conversion of cloud condensate to precipitation.

   :code:`WD_KcScaleFac` is defined as a 3-element vector:

   - :code:`WD_KcScaleFac(1)` multiplies :math:`K` when
     :math:`T < 237` kelvin.
   - :code:`WD_KcScaleFac(2)` multiplies :math:`K` when
     :math:`237 \le T < 258` kelvin
   - :code:`WD_KcScaleFac(3)` multiplies :math:`K` when
     :math:`T \ge 258` kelvin.

.. option:: WD_KcScaleFac_Luo

   Specifies a temperature-dependent scale factor that is used to
   multiply :math:`K`, aka :math:`K_c`, the rate constant for
   conversion of cloud condensate to precipitation.

   Used only in the :cite:t:`Luo_et_al._2020` wet deposition scheme.

   :code:`WD_KcScaleFac_Luo` is defined as a 3-element vector:

   - :code:`WD_KcScaleFac_Luo(1)` multiplies :math:`K` when
     :math:`T < 237` kelvin.
   - :code:`WD_KcScaleFac_Luo(2)` multiplies :math:`K` when
     :math:`237 \le T < 258` kelvin.
   - :code:`WD_KcScaleFac_Luo(3)` multiplies :math:`K` when
     :math:`T \ge 258` kelvin.

.. option:: WD_RainoutEff

   Specifies a temperature-dependent scale factor that is used to
   multiply :math:`F_i` (aka :literal:`RAINFRAC`), the fraction of
   species scavenged by rainout.

   :code:`WD_RainoutEff` is defined as a 3-element vector:

   - :code:`WD_RainoutEff(1)` multiplies :math:`F_i` when
     :math:`T < 237` kelvin.
   - :code:`WD_RainoutEff(2)` multiplies :math:`F_i` when
     :math:`237 \le T < 258` kelvin.
   - :code:`RainoutEff(3)` multiplies :math:`F_i` when
     :math:`T \ge 258` kelvin.

   This allows us to better simulate scavenging by snow and impaction
   scavenging of BC.  For most species, we need to be able to turn off
   rainout  when :math:`237 \le T <  258` kelvin. This can be easily
   done by setting :code:`RainoutEff(2) = 0`.

   .. note::

      For SOA species, the maximum value of :code:`WD_RainoutEff` will
      be 0.8 instead of 1.0.

.. option:: WD_RainoutEff_Luo

   Specifies a temperature-dependent scale factor that is used to
   multiply :math:`F_i` (aka :literal:`RAINFRAC`), the fraction of
   species scavenged by rainout. (Used only in the
   :cite:`Luo_et_al._2020` wet deposition scheme).

   :code:`WD_RainoutEff_Luo` is defined as a 3-element vector:

   - :code:`WD_RainoutEff_Luo(1)` multiplies :math:`F_i` when
     :math:`T < 237` kelvin.
   - :code:`WD_RainoutEff_Luo(2)` multiplies :math:`F_i` when
     :math:`237 \le T < 258` kelvin.
   - :code:`RainoutEff_Luo(3)` multiplies :math:`F_i` when
     :math:`T \ge 258` kelvin.

   This allows us to better simulate scavenging by snow and impaction
   scavenging of BC.  For most species, we need to be able to turn off
   rainout when :math:`237 \le T <  258` kelvin. This can be easily
   done by setting :code:`RainoutEff(2) = 0`.

   .. note::

      For SOA species, the maximum value of :code:`WD_RainoutEff_Luo`
      will  be 0.8 instead of 1.0.

.. _spcguide-microphys:

=======================
Microphysics parameters
=======================

.. option:: MP_SizeResAer

   Indicates that the species is a size-resolved aerosol species
   (:literal:`true`), or isn't (:literal:`false`).  Used only by
   simulations using either `APM
   <http://wiki.geos-chem.org/APM_aerosol_microphysics>`_
   or `TOMAS <http://wiki.geos-chem.org/TOMAS_aerosol_microphysics>`_
   microphysics packages.

.. option:: MP_SizeResNum

   Indicates that the species is a size-resolved aerosol number
   (:literal:`true`), or isn't (:literal:`false`).  Used only by
   simulations using either `APM
   <http://wiki.geos-chem.org/APM_aerosol_microphysics>`_
   or `TOMAS <http://wiki.geos-chem.org/TOMAS_aerosol_microphysics>`_
   microphysics packages.

.. _spcguide-metadata-other:

================
Other parameters
================

.. option:: BackgroundVV

   If a restart file does not contain an global initial concentration
   field for a species, GEOS-Chem will attempt to set the initial
   concentration (in :math:`vol\ vol^{-1}` dry air) to the value
   specified in :code:`BackgroundVV` globally.   But if
   :code:`BackgroundVV` has not been specified, GEOS-Chem will set
   the initial concentration for the species to :math:`10^{-20}
   vol\ vol^{-1}` dry air instead.

   .. note::

      Recent versions of GCHP may require that all initial conditions
      for all species to be used in a simulation be present in the
      restart file.  See `gchp.readthedocs.io
      <https://gchp.readthedocs.io>`_ for more information.
