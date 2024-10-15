.. _cfg-hco-diagn:

##############
HEMCO_Diagn.rc
##############

In your run directory, you will find a copy of the `HEMCO diagnostic configuration file
<https://hemco.readthedocs.io/en/latest/hco-ref-guide/diagnostics.html#configuration-file-for-the-default-collection>`_
(named :file:`HEMCO_Diagn.rc`) corresponding to the
:ref:`HEMCO_Config.rc <cfg-hco-cfg>` file.  You will only need to edit
this file if you wish to change the default diagnostic output configuration.

.. _cfg-hco-diagn-gchp-gcc:

======
Format
======

A snippet of the :file:`HEMCO_Diagn.rc` for the :option:`fullchem`
simulation is shown below:

.. code-block:: kconfig

   ###############################################################################
   #####  ALD2 emissions                                                     #####
   ###############################################################################
   EmisALD2_Total      ALD2   -1     -1  -1   3   kg/m2/s  ALD2_emission_flux_from_all_sectors
   EmisALD2_Anthro     ALD2   0      1   -1   3   kg/m2/s  ALD2_emission_flux_from_anthropogenic
   EmisALD2_BioBurn    ALD2   111    -1  -1   2   kg/m2/s  ALD2_emission_flux_from_biomass_burning
   EmisALD2_Biogenic   ALD2   0      4   -1   2   kg/m2/s  ALD2_emission_flux_from_biogenic_sources
   EmisALD2_Ocean      ALD2   101    -1  -1   2   kg/m2/s  ALD2_emission_flux_from_ocean
   EmisALD2_PlantDecay ALD2   0      3   -1   2   kg/m2/s  ALD2_emission_flux_from_decaying_plants
   EmisALD2_Ship       ALD2   0      10  -1   2   kg/m2/s  ALD2_emission_flux_from_ships


Columns:

#. netCDF variable name for the requested diagnostic quantity
#. Species name
#. Extension number (:literal:`-1` means sum over all extensions)
#. Category  (:literal:`-1` means sum over all categories)
#. Hierarchy (:literal:`-1` means sum over all hierarchies)
#. Dimension of data (:literal:`1`: scalar, :literal:`2`: lon-lat,
   :literal:`3`: lon-lat-lev)
#. Units
#. Value for the :literal:`long_name` netCDF variable attribute

The prefix (e.g. :literal:`OutputDir/HEMCO_diagnostics`) for HEMCO diagnostics
output files are specified in the
`Settings section of the HEMCO_Config.rc file
<https://hemco.readthedocs.io/en/latest/hco-ref-guide/hemco-config.html#section-settings>`_.

.. _cfg-hco-diagn-gchp-gcc:

====================================================
Usage differences between GCHP and GEOS-Chem Classic
====================================================

Emissions diagnostics (beginning with :literal:`Emis` or
:literal:`Inv`) listed in :file:`HEMCO_Diagn.rc` will not be archived
to disk unless they are also included in the
:literal:`Emissions` collection of the `GCHP HISTORY.rc file
<https://gchp.readthedocs.io/en/latest/user-guide/config-files/HISTORY_rc.html>`_.
This is because GCHP relies on the MAPL :program:`HISTORY` component
for diagnostic archival, and thus all diagnostic ouptuts must be
listed in the GCHP :file:`HISTORY.rc` file.

Also note that the GCHP :literal:`Emissions` collection is archived
such that level 1 corresponds to the top-of-atmosphere and level 72
corresponds to the surface (aka :literal:`lev:positive = 'down'`).
This is in contrast to all other GCHP diagnostic collections, where
level 1 is the surface and level 72 is top-of-atmopshere (aka
:literal:`lev:positive = 'up'`).
