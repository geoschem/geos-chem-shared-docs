.. _photolysis-guide:

#####################
Photolysis mechanisms
#####################

This Guide describes photolysis mechansims used by GEOS-Chem.

.. _photolysis-guide-cloudj:

=======
Cloud-J
=======

:program:`Cloud-J` is a multi-scattering eight-stream radiative
transfer model for solar radiation based on the older Fast-J scheme.
It was introduced into GEOS-Chem in 14.3.0.

From `History of the Fast-J and FAST-JX photolysis codes
<https://github.com/geoschem/cloud-j/blob/main/docs/History_of_Fast-J_photolysis_code.md>`_:

   Cloud-J version 7.3c (Prather 2015) now supersedes
   :ref:`photolysis-guide-fastjx`, and all new development for
   photolysis rates, whether in the Fast-JX core modules or the
   Cloud-J wrapper, will follow this notation....Cloud-J developed a
   new algorithm for cloud overlap based on the vertical decorrelation
   observed: Blocks of clouds in the altitude ranges 0-1.5 km,
   1.5–3.5, 3.5–6, 6–9, 9–13, and >13 km are maximally overlapped
   while each block is de-correlated from the one below. Cloud-J also
   developed a fast code to completely define all independent column
   atmospheres (ICAs) for a range of cloud overlap rules. Other minor
   changes included the shift of the end of bin 18 from 850 nm to 778
   nm to match the infrared bands of the solar heating code
   RRTMG-SW. Also, solar fluxes and cross-sections changed slightly
   with the updated solar spectrum reference data and improved
   averaging over wavelength, but changes in J's and any of the
   Cloud-J stats are <1% and usually <<1%.

Cloud-J is the photolyis scheme used by all GEOS-Chem :ref:`fullchem
simulations <fullchem-sim>`.

**Authors**

- Michael Prather (UC Irvine)
- Lizzie Lundgren (Harvard)

.. _photolysis-guide-fastjx:

=======
Fast-JX
=======

Sebastian Eastham implemented FAST-JX v7.0a into GEOS-Chem v10-01.  As
described in :cite:t:`Eastham_et_al._2014`:

   GEOS-Chem uses a customized version of the FAST-JX v6.2 photolysis
   mechanism (:cite:t:`Wild_et_al._2000`), which efficiently estimates
   tropospheric photolysis. The customized version uses the wavelength
   bands from the older Fast-J tropospheric photolysis scheme and does
   not consider wavelengths shorter than 289 nm, assuming they are
   attenuated above the tropopause. However, these high-energy photons
   are responsible for the release of ozone-depleting agents in the
   stratosphere. The standard Fast-JX model (Prather, 2012) addresses
   this limitation by expanding the spectrum analyzed to 18 wavelength
   bins covering 177–850 nm, extending the upper altitude limit to
   approximately 60 km. We therefore incorporate Fast-JX v7.0a into
   GEOS-Chem UCX. Fast-JX includes cross-section data for many species
   relevant to the troposphere and stratosphere. However, accurately
   representing sulfur requires calculation of gaseous H2SO4
   photolysis, a reaction which is not present in Fast-JX but which
   acts as a source of sulfur dioxide in the upper stratosphere. Based
   on a study by Mills (2005), the mean cross-section between 412.5
   and 850 nm is estimated at 2.542 × 10−25 cm2. We also add
   photolysis of ClOO and ClNO2, given their importance in catalytic
   ozone destruction, using data from JPL 10-06 (Sander et al.,
   2011). Fast-JX v7.0a includes a correction to calculated acetone
   cross sections. Accordingly, where hydroxyacetone cross-sections
   were previously estimated based on one branch of the acetone
   decomposition, a distinct set of cross sections from JPL 10-06 are
   used.

   The base version of GEOS-Chem uses satellite observations of total
   ozone columns when determining ozone-related scattering and
   extinction. The UCX allows either this approach, as was used for
   the production of the results shown, or can employ calculated ozone
   mixing ratios instead, allowing photolysis rates to respond to
   changes in the stratospheric ozone layer.

FAST-JX is still used by the GEOS-Chem :ref:`hg-sim`.

**Authors and collaborators**

- Michael Prather (UC Irvine)
- Oliver Wild (formerly UC Irvine)
- Sebastian Eashtam (formerly MIT)
