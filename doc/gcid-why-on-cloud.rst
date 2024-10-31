.. _gcid-why:

############################################
Why do we store GEOS-Chem data on the cloud?
############################################

Storing the GEOS-Chem Input Data repository on the AWS cloud offers
several advantages:

.. _gcid-why-scal:

===========
Scalability
===========

Cloud storage scales seamlessly to accommodate growing datasets
without the need for physical infrastructure upgrades. 

.. _gcid-why-acc:

=============
Accessibility
=============

Data hosted on the cloud can be accessed from anywhere in the world,
facilitating collaboration among teams.  The data can also be accessed
accessed in :ref:`several different ways <gcid-data-access>`.

.. _gcid-why-perf:

===========
Performance
===========

Setting up a high-resolution nested simulation often requires
significant time to retrieve the necessary meteorological fields. The
new paradigm to solve this big data challenge is to "move compute to
data", meaning that computations are performed directly in the cloud
environment where the data is already available. This approach
leverages Amazon's considerable infrastructure, providing users with a
more customized computing environment (For more information, see
`Running GCHP on AWS 
<https://gchp.readthedocs.io/en/latest/supplement/setting-up-aws-parallelcluster.html>`_).  
