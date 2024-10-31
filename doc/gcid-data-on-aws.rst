.. _gcid-data:

###################################
The GEOS-Chem Input Data repository
###################################

The main `GEOS-Chem Input Data <https://aws.amazon.com/marketplace/pp/prodview-gsu7hiudejnxq#resources>`_
repository is hosted at the AWS S3 bucket `s3://gcgrid
<https://geos-chem.s3.amazonaws.com/index.html>`_.  You may
download the data that you need to run :program:`GEOS-Chem Classic`,
:program:`GCHP`, or :program:`HEMCO standalone` simulations from this
repository.

.. note::

   We maintain additional data repositories for special data inputs.
   Please see our :ref:`other-portals` chapter for details.

.. _gcid-data-org:

=================
Data organization
=================

The GEOS-Chem Input Data repository is structured into the following
categories:

1. :ref:`gcid-data-org-init-cond` (aka `Restart files <https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/restart-files.html#restart-files>`_)
#. :ref:`gcid-data-org-chem-inputs`
#. :ref:`gcid-data-org-emis-inputs`
#. :ref:`gcid-data-org-met`

.. _gcid-data-org-init-cond:

Initial conditions input data
-----------------------------

Initial conditions include initial species concentrations (aka
`Restart files
<https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/restart-files.html#restart-files>`_)
used to start a GEOS-Chem simulation.

.. _gcid-data-org-chem-inputs:

Chemistry input data
--------------------

Chemistry input data includes:

- Tables of aerosol optical properties
- Quantum yields and cross sections for photolysis using either
  ``Cloud-J`` or legacy ``FAST-JX``
- Climatology data for :program:`Linoz` stratospheric ozone chemistry
- Boundary conditions for :program:`UCX` stratospheric chemistry routines

.. _gcid-data-org-emis-inputs:

Emissions input data
--------------------

Emissions input data includes the following data:

- Emissions inventories
- Input data for HEMCO Extensions
- Input data for GEOS-Chem specialty simulations
- Scale factors
- Mask definitions
- Surface boundary conditions
- Leaf area indices
- Land cover map

.. _gcid-data-org-met:

Meteorology input data
----------------------

GEOS-Chem Classic be driven by the following meteorology products:

#. `MERRA-2 <http://wiki.geos-chem.org/MERRA-2>`_
#. `GEOS-FP <http://wiki.geos-chem.org/GEOS_FP>`_
#. `GCAP 2.0 <http://atmos.earth.rochester.edu/input/gc/ExtData>`_

The GCAP meteorology is available via a separate data portal at the
University of Rochester.

.. attention::

   We are still evaluating GEOS-Chem with the new NASA GEOS-IT
   meterorology product.  For the time being, you should use one of
   the other meteorology options.

.. _gcid-data-access:

===========
Data access
===========

You may access the GEOS-Chem Input Data repository in several ways, as
described below:

.. _gcid-data-access-we:

AWS S3 Explorer
---------------

You can browse the contents of the GEOS-Chem Input Data repository
with the :program:`AWS S3 Explorer` interface.  Simply point your web
browser to the following link:

- https://geos-chem.s3.amazonaws.com/index.html.

This is an easy way for you to familiarize yourself with the directory
structure.  Before downloading large amounts of data, we recommend
that you use the AWS S3 Explorer to find the path to the relevant
data directories.

.. _gcid-data-access-cli:

AWS CLI (command-line interface)
--------------------------------

You can also use the AWS command-line interface (aka AWS CLI) to
browse and download data from the GEOS-Chem Input Data repository.
For example, use this command to get a data listing:
command

.. code-block:: console

   $ aws s3 ls s3://geos-chem/   # Get a directory listing

For detailed instructions about using AWS CLI, please see our
:ref:`gcid-tut` chapter.

.. _gcid-data-access-http:

HTTP or wget download
---------------------

You can also access the GEOS-Chem Input Data repository via the
alternate web link http://geoschemdata.wustl.edu.

As with the AWS S3 Explorer, you can navigate through the web
interface to find the data sets that you wish to download.  You can
then use the :program:``wget` command to download the data.

.. _gcid-data-access-globus:

Globus
------

Many universities and other institutions use :program:`Globus`, which
has much higher data download speeds than normal SSH or HTTP connections.

If your institution uses Globus, you can download data from the
:program:`GEOS-Chem Data (WashU)` endpoint to your institution's
endpoint.  Ask your IT support staff for more information about Globus
at your institution.

.. _gcid-data-access-bashdatacatalog:

Bashdatacatalog
---------------

We have created the :program:`bashdatacatalog` tool to
facilitate downloading large amounts of data from the GEOS-Chem Input
Data repository. Please see our :ref:`bashdatacatalog` guide for usage
instructions.

.. _gcid-data-dir-structure:

===========================
Example directory structure
===========================

The directory structure of the GEOS-Chem Input Data repository adheres
to the format listed below.  You can see easily browse through the
repository using one of the following web links:

- https://geos-chem.s3.amazonaws.com/index.html (Recommended)
- http://geoschemdata.wustl.edu

.. code-block:: text

   ExtData/
   │
   ├── GEOSCHEM-RESTARTS/
   │   ├── GC_14.2.0/
   │   ├── GC_14.3.0/
   │   └── ...
   │
   ├── CHEM_INPUTS/
   │   ├── CLOUD-J/
   │   ├── FAST-JX/
   │   └── ...
   │
   ├── HEMCO/
   │   ├── UVALBEDO/
   │   └── ...
   │
   ├── GEOS_0.5x0.625/
   │   ├── MERRA2/
   │   │   ├── 2023/
   │   │   ├── 2024/
   │   │   └── ...
   │   └── ...
   │
   ├── GEOS_0.25x0.3125/
   │   ├── GEOS_FP/
   │   │   ├── 2023/
   │   │   ├── 2024/
   │   │   └── ...
   │   ├── GEOS_FP_Raw/
   │   └── ...
   │
   └── ...
