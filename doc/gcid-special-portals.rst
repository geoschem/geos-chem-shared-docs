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
most years of `GEOS-FP <http://wiki.geos-chem.org/GEOS_FP>`_,
`MERRA-2 <http://wiki.geos-chem.org/GEOS_FP>`_, and GEOS-IT meteorology.

We also maintain two additional data portals for special data sets.

.. _gcid-special-portals-nested:

===========================
GEOS-Chem Nested Input Data
===========================

The `GEOS-Chem Nested Input data
<https://registry.opendata.aws/geoschem-nested-input-data/>`_
portal (aka :file:`s3://gcgrid`) stores GEOS-FP and MERRA-2
meteorology fields that have been cropped to specific `nested-grid
<https://geos-chem.readthedocs.io/en/latest/supplemental-guides/nested-grid-guide.html>`_
domains. These data can be used to perform high-resolution inversions
with the `Integrated Methane Inversion (IMI)
<https://imi.readthedocs.io>`_ workflow.

Data stored at the GEOS-Chem Nested Input Data portal is covered
by the `AWS Open Data Sponsorship Program
<https://aws.amazon.com/opendata/open-data-sponsorship-program/>`_. and
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
     - :ref:`gcc-hgrids-nested-0125-af` [#A]_
   * - Africa
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-af`
   * - Asia
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-0125-as` [#B]_
   * - Asia
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-as`
   * - Asia
     - MERRA-2
     - :ref:`gcc-hgrids-nested-05-as`
   * - Europe
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-eu`
   * - Europe
     - MERRA-2
     - :ref:`gcc-hgrids-nested-05-eu`
   * - Middle East
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-me`
   * - North America
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-0125-na` [#C]_
   * - North America
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-na`
   * - North America
     - MERRA-2
     - :ref:`gcc-hgrids-nested-05-na`
   * - Oceania
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-oc`
   * - Russia
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-ru`
   * - South America
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-0125-sa` [#D]_
   * - South America
     - GEOS-FP
     - :ref:`gcc-hgrids-nested-025-sa`

.. rubric:: Notes

.. [#A] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested Africa domain.  Other met fields are
        taken from the GEOS-FP  :ref:`gcc-hgrids-nested-025-af` archive.

.. [#B] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested Asia domain.  Other met fields are
        taken from the GEOS-FP :ref:`gcc-hgrids-nested-025-as` archive.

.. [#C] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested North America domain.  Other met
        fields are taken from the GEOS-FP
        :ref:`gcc-hgrids-nested-025-na` archive.

.. [#D] Winds, pressures, and specific humidity are read at 0.125° x
        0.15625° over the nested South America domain.  Other met
        fields are taken from the GEOS-FP
        :ref:`gcc-hgrids-nested-025-sa` archive.

The data can be accessed by:

- AWS S3 Explorer (https://gcgrid.s3.amazonaws.com/index.html)
- Direct HTTP or wget download
- :ref:`Dry-run simulation <dry-run>`

.. _gcid-special-portals-gcap2:

===========================================
GCAP 2.0 meteorology hosted at U. Rochester
===========================================

The `atmos.earth.rochester.edu
<http://atmos.earth.rochester.edu/input/gc/ExtData/>`_ portal
(curated by Lee Murray at the University of Rochester) contains the
GCAP 2.0 meteorological data inputs for use with GEOS-Chem
simulations.

The data can be accessed by:

- Direct HTTP or wget download (http://atmos.earth.rochester.edu/input/gc/ExtData/)
- :ref:`Dry run simulation <dry-run>`
