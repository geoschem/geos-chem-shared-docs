.. |br| raw:: html

   <br />

.. _gcid-special-portals:

#######################################
Additional portals for meteorology data
#######################################

As discussed in the previous chapter, the :ref:`GEOS-Chem Input
Data <gcid>` portal is the main source of input data for
:program:`GEOS-Chem Classic`, :program:`GCHP`, and the :program:`HEMCO
standalone model`.  This portal contains the entire catalog
of emissions inventories, chemical inputs, initial conditions, and
most years of `GEOS-FP <http://wiki.geos-chem.org/GEOS_FP>`__,
`MERRA-2 <http://wiki.geos-chem.org/GEOS_FP>`__, and GEOS-IT meteorology.

We also maintain two additional data portals for special data sets.

.. _gcid-special-portals-nested:

===========================
GEOS-Chem Nested Input Data
===========================

The `GEOS-Chem Nested Input data
<https://registry.opendata.aws/geoschem-nested-input-data/>`__
portal (aka :file:`s3://gcgrid`) stores GEOS-FP and MERRA-2
meteorology fields that have been cropped to specific `nested-grid
<https://geos-chem.readthedocs.io/en/latest/supplemental-guides/nested-grid-guide.html>`__
domains. These data can be used to perform high-resolution inversions
with the `Integrated Methane Inversion (IMI)
<https://imi.readthedocs.io>`__ workflow.

Data stored at the GEOS-Chem Nested Input Data portal is covered
by the `AWS Open Data Sponsorship Program
<https://aws.amazon.com/opendata/open-data-sponsorship-program/>`__. and
may be downloaded without incurring any data egress fees.

.. list-table:: Available nested-grid meteorology (2018 to present day)
   :header-rows: 1
   :widths: 25 20 55
   :align: center

   * - Nested domain
     - Meteorology
     - Grid
   * - Africa
     - GEOS-FP
     - `0.125° x 0.15625° AF nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-15625-af-nested-grid>`__ [#A]_
   * - Africa
     - GEOS-FP
     - `0.25° x 0.3125° AF nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-af-nested-grid>`__
   * - Asia
     - GEOS-FP
     - `0.125° x 0.15625° AS nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-15625-as-nested-grid>`__ [#B]_
   * - Asia
     - GEOS-FP
     - `0.25° x 0.3125° AS nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-as-nested-grid>`__
   * - Asia
     - MERRA-2
     - `0.5° x 0625° AS nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-625-as-nested-grid>`__
   * - Europe
     - GEOS-FP
     - `0.25° x 0.3125° EU nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-eu-nested-grid>`__
   * - Europe
     - MERRA-2
     - `0.5° x 0625° EU nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-625-eu-nested-grid>`__
   * - Middle East
     - GEOS-FP
     - `0.25° x 0.3125° ME nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-me-nested-grid>`__
   * - North America
     - GEOS-FP
     - `0.125° x 0.15625° NA nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-15625-na-nested-grid>`__ [#C]_
   * - North America
     - GEOS-FP
     - `0.25° x 0.3125° NA nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-na-nested-grid>`__
   * - North America
     - MERRA-2
     - `0.5° x 0625° NA nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-625-na-nested-grid>`__
   * - Oceania
     - GEOS-FP
     - `0.25° x 0.3125° OC nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-oc-nested-grid>`__
   * - Russia
     - GEOS-FP
     - `0.25° x 0.3125° RU nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-ru-nested-grid>`__
   * - South America
     - GEOS-FP
     - `0.125° x 0.15625° SA nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-15625-sa-nested-grid>`__ [#D]_
   * - South America
     - GEOS-FP
     - `0.25° x 0.3125° SA nested grid <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-sa-nested-grid>`__

.. rubric:: Notes

.. [#A] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested Africa domain.  Other met fields are
        taken from the GEOS-FP  `0.25° x 0.3125° AF nested grid
	<https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-af-nested-grid>`__
	archive.

.. [#B] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested Asia domain.  Other met fields are
        taken from the GEOS-FP `0.25° x 0.3125° AS nested grid
	<https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-as-nested-grid>`__
	archive.

.. [#C] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested North America domain.  Other met
        fields are taken from the GEOS-FP
        `0.25° x 0.3125° NA nested grid
	<https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-na-nested-grid>`__
	archive.

.. [#D] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested South America domain.  Other met
        fields are taken from the GEOS-FP
        `0.25° x 0.3125° SA nested grid
	<https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#x-0-3125-sa-nested-grid>`__
	archive.

The data can be accessed by:

- AWS S3 Explorer (https://gcgrid.s3.amazonaws.com/index.html)
- Direct HTTP or wget download
- `GEOS-Chem Classic dry-run simulation <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/dry-run.htmldry-run>`__

.. _gcid-special-portals-gcap2:

===========================================
GCAP 2.0 meteorology hosted at U. Rochester
===========================================

The `atmos.earth.rochester.edu
<http://atmos.earth.rochester.edu/input/gc/ExtData/>`__ portal
(curated by Lee Murray at the University of Rochester) contains the
GCAP 2.0 meteorological data inputs for use with GEOS-Chem
simulations.

The data can be accessed by:

- Direct HTTP or wget download (http://atmos.earth.rochester.edu/input/gc/ExtData/)
- `GEOS-Chem Classic dry-run simulation <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/dry-run.htmldry-run>`__
