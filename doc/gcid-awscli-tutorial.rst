.. |br| raw:: html

   <br />

.. _gcid-tut:

######################################################
Tutorial: Accessing GEOS-Chem Input Data using AWS CLI
######################################################

This tutorial will guide you through the process of accessing and
using the :ref:`GEOS-Chem Input Data <gcid>` with
AWS CLI. Alternatively, you can access the data via `AWS S3 Explorer
<https://geos-chem.s3.amazonaws.com/index.html>`_.

.. note::

   When you open an AWS account you will be asked for credit card
   information.  But even if you don't have (or don't wish to open) an
   AWS account, you may still access and download the GEOS-Chem Input
   Data using AWS CLI with anonymous login, which is completely free.

The workflow is:

#. :ref:`Install AWS CLI <gcid-tut-install>`

   - This step only has to be done once.

#. :ref:`Configure AWS CLI <gcid-tut-conf>`

   - You may skip this step if you do not have (or do not wish to
     open) an AWS account.

#. :ref:`Download data from the GEOS-Chem Input Data portal
   <gcid-tut-access>`.

#. :ref:`Run a GEOS-Chem Classic, GCHP, or HEMCO standalone simulation
   <gcid-tut-using>`.

.. _gcid-tut-install:

===============
Install AWS CLI
===============

Follow the installation instructions from the `AWS CLI User Guide <https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html>`_.

.. _gcid-tut-conf:

======================================================
Configure AWS CLI (if you already have an AWS account)
======================================================

.. note::

   You may :ref:`skip ahead to the next section <gcid-tut-access>` if
   you do not have (or do not wish to open) an AWS account but wish to
   access the data via anonymous login.

Configure AWS CLI with this command:

.. code-block:: sh

   $ aws configure

and supply your credentials when prompted.

For instructions on :literal:`aws configure`, refer to the `Configure the AWS CLI <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html>`_ .

.. _gcid-tut-access:

========================
Access and download data
========================

.. _gcid-tut-access-list:

Step 1: List available data
---------------------------

To view the available data in the GEOS-Chem Input Data S3 bucket, use
one of the following commands:

**If you have an AWS account:**

.. code-block:: console

   $ aws s3 ls s3://geos-chem/

**If you do not have an AWS account:**

.. code-block:: console

   $ aws s3 ls --no-sign-request s3://geos-chem/

Make sure that the S3 bucket name ends with a trailing slash
(:literal:`/`) character; that is, use :literal:`s3://geos-chem/`
instead of :literal:`s3://geos-chem`.

.. tip::

   Adding the :literal:`--no-sign-request` flag to any AWS CLI command
   will access or download data via anonymous login.

.. _gcid-tut-access-nav:

Step 2: Navigate through the directories
----------------------------------------

You can navigate through the directories to find the specific data you
need. For example,

**If you have an AWS account:**

.. code-block:: console

   $ aws s3 ls s3://geos-chem/GEOS_0.5x0.625/MERRA2/2024/05/

**If you do not have an AWS account:**

.. code-block:: console

   $ aws s3 ls --no-sign-request s3://geos-chem/GEOS_0.5x0.625/MERRA2/2024/05/

.. _gcid-tut-access-download:

Step 3: Download the data
-------------------------

.. tip::

   If you are using :program:`GEOS-Chem Classic` or the
   :program:`HEMCO standalone model`, you can `download data with a
   dry-run simulation
   <https://geos-chem.readthedocs.io/en/stable/gcclassic-user-guide/dry-run.html>`_,
   while still using the AWS CLI data transfer protocol.

Once you have located the data you need, you can download it to your
local cluster or an EC2 instance. For example,

**If you have an AWS account:**

.. code-block:: console

   $ aws s3 cp --recursive s3://geos-chem/GEOS_0.5x0.625/MERRA2/2024/05 ./

**If you do not have an AWS account:**

.. code-block:: console

   $ aws s3 cp --recursive --no-sign-request s3://geos-chem/GEOS_0.5x0.625/MERRA2/2024/05 ./

This command will copy the data to your current path.

.. _gcid-tut-using:

=====================================
Run simulations using downloaded data
=====================================

Once you have :ref:`downloaded the data <gcid-tut-access>` from the
GEOS-Chem Input Data portal to your computer system or EC2
instance, you may run a :program:`GEOS-Chem Classic`,
:program:`GCHP`, or :program:`HEMCO standalone` simulation.  Please
refer to the relevant user guide listed below.

- `GEOS-Chem Classic Quickstart Guide
  <https://geos-chem.readthedocs.io/en/latest/getting-started/quick-start.html>`_

- `GCHP Quickstart Guide
  <https://gchp.readthedocs.io/en/latest/getting-started/quick-start.html>`_

- `HEMCO Standalone Guide
  <https://hemco.readthedocs.io/en/stable/hco-sa-guide/intro.html>`_

.. _gcid-tut-gchp-on-aws:

Running GCHP on AWS
-------------------

If you wish to use the computing resources on AWS to run GCHP and are
seeking for an AMI, feel free to check our `Set up AWS ParallelCluster <https://gchp.readthedocs.io/en/latest/supplement/setting-up-aws-parallelcluster.html>`_
guide.
