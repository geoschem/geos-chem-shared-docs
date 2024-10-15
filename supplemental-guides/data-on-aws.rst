########
GEOS-Chem input data
########

=======================
Data organization
=======================

The GEOS-Chem Input Data is structured into the following main categories (For more information, see `Download input data <https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/download-data.html>`_):

1. :ref:`c1-init-cond` (aka `Restart files <https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/restart-files.html#restart-files>`_)
#. :ref:`c1-chem-inputs`
#. :ref:`c1-emis-inputs`
#. :ref:`c1-met`

.. _c1-init-cond:

Initial conditions input data
------------------
Initial conditions include initial species concentrations (aka `Restart files <https://geos-chem.readthedocs.io/en/latest/gcclassic-user-guide/restart-files.html#restart-files>`_) used to start a GEOS-Chem simulation.

.. _c1-chem-inputs:

Chemistry input data
------------------
Chemistry input data includes:

- Quantum yields and cross sections for photolysis using either ``Cloud-J`` or legacy ``FAST-JX``
- Climatology data for :program:`Linoz`
- Boundary conditions for :program:`UCX` stratospheric chemistry routines

.. _c1-emis-inputs:

Emissions input data
------------------
Emissions input data includes the following data:

- Emissions inventories
- Input data for HEMCO Extensions
- Input data for GEOS-Chem specialty simulations
- Scale factors
- Mask definitions
- Surface boundary conditions
- Leaf area indices
- Land cover map

.. _c1-met:

Meteorology input data
------------------
GEOS-Chem Classic
be driven by the following meteorology products:

#. `MERRA-2 <http://wiki.geos-chem.org/MERRA-2>`_
#. `GEOS-FP <http://wiki.geos-chem.org/GEOS_FP>`_
#. `GCAP 2.0 <http://atmos.earth.rochester.edu/input/gc/ExtData>`_

.. attention::

   We are still evaluating GEOS-Chem with the new NASA GEOS-IT
   meterorology product.  For the time being, you should use one of
   the other meteorology options.

.. _c1-access_data:

=======================
Access the data
=======================
Users can access the GEOS-Chem Input Data in multiple ways:

.. option:: AWS S3 Bucket

   The data is stored in an AWS S3 bucket. Users can navigate these directories to find the specific datasets they need.

   Users can browse the bucket from https://geos-chem.s3.amazonaws.com/index.html.
   
   Example commands for accessing data using AWS CLI:

     .. code-block:: sh

        aws s3 ls s3://geos-chem/

.. option:: HTTP Download
   
   The data can be downloaded from http://geoschemdata.wustl.edu/. 
   
   Users can navigate through the web interface to find and download the datasets via HTTP or wget.

.. option:: Globus

   The data is also available through the Globus endpoint “GEOS-Chem Data (WashU)”. 
   
   Users can use the Globus interface to transfer data to their local storage.

=======================
Example directory structure
=======================
.. s3://geos-chem/
.. http://geoschemdata.wustl.edu/

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