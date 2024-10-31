.. _gcid:

#################################
GEOS-Chem Input Data on AWS cloud
#################################

The `GEOS-Chem Input Data
<https://aws.amazon.com/marketplace/pp/prodview-gsu7hiudejnxq>`_
repository provides essential datasets for `GEOS-Chem Classic
<https://geos-chem.readthedocs.io/en/stable/>`_, `GCHP
<https://gchp.readthedocs.io/en/stable/>`_, and 
`HEMCO  <https://hemco.readthedocs.io/en/stable>`_.
This includes NASA/GMAO MERRA-2 and GEOS-FP :ref:`meteorological
products <gcid-data-org-met>`, :ref:`chemistry input data
<_gcid-data-org-chem-inputs>`, :ref:`emissions input data
<gcid-data-org-emis-inputs>`, and other smaller datasets such as model
:ref:`initial conditions <gcid-data-org-init-cond>`. We are pleased to
announce that we are now part of the `AWS Open Data Sponsorship
Program
<https://aws.amazon.com/opendata/open-data-sponsorship-program/>`_. We
will continuously upload and maintain the data in an S3 Bucket
(:file:`s3://geos-chem`) for convenient access.

In the following chapters, we will describe how the GEOS-Chem Input
Data repository is organized and how to access the data stored there.

.. toctree::
   :maxdepth: 2

   ./gcid-why-on-cloud
   ./gcid-data-on-aws
   ./gcid-awscli-tutorial
