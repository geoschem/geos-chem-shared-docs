.. |br| raw:: html

   <br />

.. _gcid:

#################################
GEOS-Chem Input Data on AWS cloud
#################################

:ref:`gcid-data` provides essential datasets for `GEOS-Chem
Classic <https://geos-chem.readthedocs.io/en/stable/>`_, `GCHP
<https://gchp.readthedocs.io/en/stable/>`_, and
`HEMCO  <https://hemco.readthedocs.io/en/stable>`_.
This includes NASA/GMAO MERRA-2 and GEOS-FP :ref:`meteorological
products <gcid-data-org-met>`, :ref:`chemistry input data
<gcid-data-org-chem-inputs>`, :ref:`emissions input data
<gcid-data-org-emis-inputs>`, and other smaller datasets such as model
:ref:`initial conditions <gcid-data-org-init-cond>`.  We will
continuously upload and maintain the data in an S3 Bucket
(`s3://geos-chem <https://geos-chem.s3.amazonaws.com/index.html>`_)
for convenient access.

We are pleased to announce that the GEOS-Chem Input Data portal is
part of the `AWS Open Data Sponsorship Program
<https://aws.amazon.com/opendata/open-data-sponsorship-program/>`_.
As a result, **the data is completely free to use**.  You will NOT
incur any data egress fees when downloading data from the
`s3://geos-chem <https://geos-chem.s3.amazonaws.com/index.html>`_
bucket.  This is now the recommended repository for GEOS-Chem data
download.

.. note::

   Certain data are stored separately from the main :ref:`GEOS-Chem
   Input Data portal <gcid-data>`:

   - NASA/GMAO meteorology fields that have been cropped to the
     various `nested grid domains
     <https://geos-chem.readthedocs.io/en/latest/supplemental-guides/horizontal-grids.html#nested-grids>`_
     may be downloaded from our :ref:`gcid-special-portals-nested`
     portal (aka `s3://gcgrid
     <https://gcgrid.s3.amazonaws.com/index.html>`_). |br|
     |br|

   - GCAP 2.0 meteorology fields for present & future scenarios may be
     downloaded from our :ref:`GCAP 2.0 meteorology portal hosted
     at U. Rochester <gcid-special-portals-gcap2>`.

In the following chapters, we will describe how the GEOS-Chem Input
Data portal is organized and how to access the data stored there.

.. toctree::
   :maxdepth: 2

   ./gcid-why-on-cloud
   ./gcid-data-on-aws
   ./gcid-awscli-tutorial
