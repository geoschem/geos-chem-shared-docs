.. _gcid-why:

############################################
Why do we store GEOS-Chem data on the cloud?
############################################

Storing the GEOS-Chem Input Data portal on the AWS cloud offers
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

Data hosted on the cloud can be :ref:`accessed from anywhere in the
world <gcid-data-access>`, facilitating collaboration among
teams. Data stored at the GEOS-Chem Input Data portal is covered
by the `AWS Open Data Sponsorship Program
<https://aws.amazon.com/opendata/open-data-sponsorship-program/>`_.
and may be downloaded without incurring any data egress fees.

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
