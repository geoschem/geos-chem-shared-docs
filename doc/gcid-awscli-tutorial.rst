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

The workflow is

#. :ref:`Install and configure AWS CLI <gcid-tut-conf>`

   - This step only has to be done once.

#. :ref:`Download data from the GEOS-Chem Input Data portal
   <gcid-tut-access>`.

#. :ref:`Run a GEOS-Chem Classic, GCHP, or HEMCO standalone simulation
   <gcid-tut-using>`.

.. _gcid-tut-conf:

=============================
Install and configure AWS CLI
=============================

If you have already installed and configured the AWS CLI previously,
continue to :ref:`gcid-data-access`.

.. _gcid-tut-conf-install:

Step 1: Install AWS CLI
-----------------------

Follow the instructions to install the AWS CLI from the `AWS CLI User Guide <https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html>`_.

.. _gcid-tut-conf-conf:

Step 2: Configure AWS CLI
-------------------------

Run the following command to configure AWS CLI with your credentials:

.. code-block:: sh

   $ aws configure

For instructions on :literal:`aws configure`, refer to the `Configure the AWS CLI <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html>`_ .

.. _gcid-tut-access:

========================
Access and download data
========================

.. _gcid-tut-access-list:

Step 1: List available data
---------------------------

To view the available data in the GEOS-Chem Input Data S3 bucket, use
the following command:

.. code-block:: sh

   $ aws s3 ls s3://geos-chem/

or without AWS account required

.. code-block:: sh

   $ aws s3 ls --no-sign-request s3://geos-chem/

.. _gcid-tut-access-nav:

Step 2: Navigate through the directories
----------------------------------------

You can navigate through the directories to find the specific data you
need. For example,

.. code-block:: sh

   $ aws s3 ls s3://geos-chem/GEOS_0.5x0.625/MERRA2/2024/05

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

.. code-block:: sh

   $ aws s3 cp s3://geos-chem/GEOS_0.5x0.625/MERRA2/2024/05 ./ --recursive

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
