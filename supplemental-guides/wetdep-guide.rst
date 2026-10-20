.. _wetdep-guide:

##############
Wet deposition
##############

GEOS-Chem has two wet deposition schemes:

#. :cite:t:`Luo_and_Yu_2023` **(the default option)**
#. :cite:t:`Jacob_et_al._2000`

.. _wetdep-guide-luo:

=======================================
Luo and Yu [2023] wet deposition scheme
=======================================

The :cite:t:`Luo_and_Yu_2023` wet deposition scheme---which builds
upon the previous :cite:t:`Luo_et_al._2020` study---is now activated
by default in GEOS-Chem 14.9.0 and later versions.

From the abstract to :cite:t:`Luo_and_Yu_2023`:

   The impacts of cloud mixing and uptake on wet scavenging are not
   adequately resolved in global models which can lead to an
   overestimation of the removal of water-soluble gases and aerosols
   from the atmosphere. To address this issue, we develop and
   implement novel parameterizations to consider the impacts of these
   processes. Our analysis of vertical profiles of nitric acid,
   inorganic nitrate, ammonium, and sulfate concentrations during the
   Atmospheric Tomography Mission periods indicates that air
   refreshing limitation has a significant impact above 800 hPa, while
   cloud ice uptake limitation plays an important role above 500
   hPa. Incorporating these two processes resulted in a reduction of
   wet depositions of these species across source regions and a slight
   increase in their downwind regions. Wet depositions of nitrate,
   ammonium, and sulfate were reduced in source regions by 22.7%,
   8.4%, and 8.3%, respectively and increased in downwind regions by
   10.1%, 7.0%, and 4.3%, respectively.

.. attention::

   :ref:`tomas-guide` is not yet compatible with the
   :cite:t:`Luo_and_Yu_2023` scheme.  When TOMAS is activated,
   :literal:`LUO_WETDEP` is automatically set to :literal:`OFF`, and
   the :cite:t:`Jacob_et_al._2000` scheme is used instead.  The TOMAS
   developers are working on a fix for this, which will be available
   in the near future.

.. _wetdep-guide-jacob:

==========================================
Jacob et. al. [2000] wet deposition scheme
==========================================

The :cite:t:`Jacob_et_al._2000` wet deposition scheme, which was
originally developed for the NASA GMI model, was the default option in
GEOS-Chem 14.8.0 and older versions.  Its implementation into
GEOS-Chem is described fully in :cite:t:`Liu_et_al._2001`.

.. note::

   To activate the :cite:t:`Jacob_et_al._2000` wet deposition scheme,
   use :code:`-DLUO_WETDEP=n` option at configuration time:

   .. code-block:: console

      $ cmake -DLUO_WETDEP=n ... etc. other CMake options
      $ make -j
      $ make install

Several updates have been made to the original implementation.  These
are described in the sections below.

.. _wetdep-guide-rainout-washout:

Allow both washout and rainout when precipitation forms
-------------------------------------------------------

From :cite:t:`Wang_et_al._2011`:

   When there is new formation of precipitation in lower layer
   :math:`k`, rainout will be applied to the whole precipitation area:
   :math:`max(F_k, F_{k+1})`, considering the contribution of
   precipitation formation overhead. This will overestimate rainout
   effect when :math:`F_{k+1}` is much larger than
   :math:`F_k`. Therefore, we now apply rainout effect to
   precipitation area :math:`F_k` and washout effect to the area:
   :math:`max(0, F_{k+1}-F_k`) in the same grid box.

Modifications for aerosol scavenging efficiency
-----------------------------------------------

From :cite:t:`Wang_et_al._2011`:

   The bulk below-cloud scavenging parameterization of
   :cite:t:`Dana_and_Hales_1976`  (:math:`k = 0.1P`, where :math:`P`
   is the precipitation rate mm h\ :sup:`-1`) used in the standard
   GEOS-Chem model integrates scavenging efficiencies over typical
   aerosol size distributions. This overestimates scavenging as it
   does not account for the preferential removal of the very fine and
   coarse particles over the course of the precipitation event,
   shifting the aerosol size distribution toward the more
   scavenging-resistant accumulation mode that accounts for most of
   aerosol mass.  Now we use the below-cloud scavenging coefficients
   (:math:`k = a P^{b}`) constructed by Feng (2007, 2009) integrated
   over accumulation mode for most aerosols and over coarse mode for
   coarse dust and sea salt.

Obtain precipitation fields directly from meteorology
-----------------------------------------------------

In GEOS-Chem v9-01-01 and later versions, we use an improved `wet deposition
scheme
<https://drive.google.com/file/d/1oVNX0JM_3Qrvdg9Y1FomFbs_FfjpCC7s/view?usp=sharing>`__
which uses the precipitation fields directly from the meteorology.
At the time, the MERRA meteorology product was used, but this same
methodology extends to other met field products as well.


Add scavenging by snow
----------------------

From :cite:t:`Wang_et_al._2011`:

   For in-cloud scavenging by rain droplets, we assume 100% of
   water-soluble aerosols are rained out. But in the case of snow,
   only dust and hydrophobic BC are considered to be IN and then could
   be rained out. Note that HNO3 is also assumed to be rained out by
   snow as it forms a monolayer in ice crystal. The below-cloud
   scavenging coefficients are also higher for snow than for rain droplets.


Impaction scavenging for hydrophobic BC and homogeneous IN removal
------------------------------------------------------------------

From :cite:t:`Wang_et_al._2014`:

   We modify the scheme by (1) scavenging hydrophobic aerosol
   (hydrophobic BC and OC) in convective updrafts, since this would
   take place by impaction (:cite:t:`Ekman_et_al._2004`) and (2)
   scavenging water-soluble aerosol from cold clouds by homogeneous
   freezing of solution droplets at T < 237 K
   (:cite:t:`Friedman_et_al._2011`). We conducted :sup:`222`\ Rn -
   :sup:`210`\ Pb simulations to test the general model representation
   of aerosol deposition and found a lifetime of tropospheric
   :sup:`210`\ Pb aerosol against deposition of 8.6 days.
