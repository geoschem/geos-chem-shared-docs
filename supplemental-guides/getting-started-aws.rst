########
Getting started
########

This tutorial will guide you through the process of accessing and using the GEOS-Chem Input Data on AWS. The workflow is

#. :ref:`Install and configure AWS CLI <conf_aws_cli>`
#. :ref:`Access and download data <access_data>`
#. :ref:`Follow the normal GEOS-Chem User Guide to use the data <normal_guide>` or :ref:`running GCHP on AWS <gchp_on_aws>`

.. _conf_aws_cli:

=======================
Install and configure AWS CLI
=======================

If you have already installed and configured the AWS CLI previously, continue to :ref:`access_data`.

Step 1: Install AWS CLI
-----------------------------

Follow the instructions to install the AWS CLI from the `AWS CLI User Guide <https://docs.aws.amazon.com/cli/latest/userguide/getting-started-install.html>`_.

Step 2: Configure AWS CLI
-------------------------------------

Run the following command to configure AWS CLI with your credentials:

.. code-block:: sh

   $ aws configure

For instructions on :literal:`aws configure`, refer to the `Configure the AWS CLI <https://docs.aws.amazon.com/cli/latest/userguide/cli-chap-configure.html>`_ .

.. _access_data:

=======================
Accessing and download data
=======================

Step 1: List available data
-------------------------------

To view the available data in the GEOS-Chem Input Data S3 bucket, use the following command:

.. code-block:: sh

   $ aws s3 ls s3://geos-chem/

or without AWS account required

.. code-block:: sh

   $ aws s3 ls --no-sign-request s3://geos-chem/

Step 2: Navigate through the directories
----------------------------------------
You can navigate through the directories to find the specific data you need. For example, 

.. code-block:: sh

   aws s3 ls s3://geos-chem/ExtData/GEOS_0.5x0.625/MERRA2/2024/05

Step 3: Download the data
-------------------------
Once you have located the data you need, you can download it to your local cluster or an EC2 instance. For example,

.. code-block:: sh

   aws s3 cp s3://geos-chem/ExtData/GEOS_0.5x0.625/MERRA2/2024/05 ./ --recursive

This command will copy the data to your current path. 

.. _normal_guide:

=======================
Using the data in GEOS-Chem
=======================

By following this tutorial, you can access and download the GEOS-Chem Input Data on AWS. Next, you can follow the normal `GEOS-Chem Classic <https://geos-chem.readthedocs.io/en/latest/getting-started/quick-start.html>`_ or `GCHP <https://gchp.readthedocs.io/en/latest/getting-started/quick-start.html>`_ user guide to use the data. 


.. _gchp_on_aws:

=======================
Running GCHP on AWS
=======================

If you want to use the computing resources on AWS to run GCHP and are seeking for an AMI, feel free to check `Set up AWS ParallelCluster
<https://gchp.readthedocs.io/en/latest/supplement/setting-up-aws-parallelcluster.html>`_. 