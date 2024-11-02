.. |br| raw:: html

   <br />

.. _gcid-data:

###################################
The GEOS-Chem Input Data repository
###################################

The main `GEOS-Chem Input Data <https://aws.amazon.com/marketplace/pp/prodview-gsu7hiudejnxq#resources>`_
repository is hosted at the AWS S3 bucket `s3://gcgrid
<https://geos-chem.s3.amazonaws.com/index.html>`_.  From here you may
download the data required to run :program:`GEOS-Chem Classic`,
:program:`GCHP`, or :program:`HEMCO standalone` simulations.

.. note::

   We maintain additional repositories for special meteorology
   products. Please see our :ref:`gcid-special-repos` chapter for
   details.

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

The GCAP meteorology is available via a :ref:`separate data repository
<gcid-special-repos-gcap2>` maintained at the University of Rochester.

.. attention::

   We are still evaluating GEOS-Chem with the new NASA GEOS-IT
   meterorology product.  For the time being, you should use one of
   the other meteorology options.

.. _gcid-data-access:

===========
Data access
===========

You may access the GEOS-Chem Input Data repository in several ways, as
described below.

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
then use the :program:`wget` command to download the data.

.. _gcid-data-access-dryrun:

Dry-run simulation (GEOS-Chem Classic and HEMCO standalone only)
----------------------------------------------------------------

If you plan to run a `GEOS-Chem Classic
<https://geos-chem.readthedocs.io>`_ or `HEMCO standalone
<https://hemco.readthedocs.io/en/stable/hco-sa-guide/intro.html>`_
simulation, we recommend first performing a :program:`dry-run
simulation`.  The dry-run simulation workflow is as follows:

#. Configure your GEOS-Chem Classic or HEMCO standalone
   simulation. |br|
   |br|

#. Run GEOS-Chem Classic or HEMCO standalone with the :code:`--dryrun`
   flag.  This will generate a list of required data files. |br|
   |br|

#. Pass this list to a Python script, which will download the data to
   your computer system or AWS EC2 instance.

For more information, please see the `geos-chem.readthedocs.io
<https://geos-chem.readthedocs.io>`_ and `hemco.readthedocs.io
<https://hemco.readthedocs.io>`_ online manuals.

.. _gcid-data-access-globus:

Globus
------

Many institutions use the :program:`Globus` file transfer utility,
which has much higher data download speeds than normal SSH or HTTP
connections.

If your institution uses Globus, you can download data from the
:program:`GEOS-Chem Data (WashU)` endpoint to your institution's
endpoint.  Ask your IT support staff for more information.

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
