.. _monitoringonaws:

*****************
Monitoring on AWS
*****************

For this section we will asume that you do have the ZMON AWS agent running. It will automatically discover EC2 instances, Auto-scaling groups, ELBs, and more for you.

Security groups
---------------

Depending on your AWS setup you most likely need to open particular ports/instances to access from ZMON. Using a limited set of ports to e.g. expose management APIs and the Prometheus node exporter will make your life easier.

Not having the proper security groups configured is mainly visible by not getting the expected results at all, as packages are dropped on AWS side rather then e.g. getting a connection refused.

Application monitoring
======================

Loadbalancer state
------------------

Number of healthy instances in the ELB.

Returning 200 on external ELB/application url?

Auto scaling group
------------------

Number of healthy instances, ASG sizing

EC2 instances
-------------

Diskspace:

 Free inodes
 Free diskspace

CPU:

Memory:

Application metrics:
