.. _monitoringonaws:

*****************
Monitoring on AWS
*****************

For this section we will assume that you do have the ZMON AWS agent running. It will automatically discover EC2 instances, Auto-scaling groups, ELBs, and more for you.

Security groups
---------------

Depending on your AWS setup you most likely need to open particular ports/instances to access from ZMON. Using a limited set of ports to e.g. expose management APIs and the Prometheus node exporter will make your life easier.

Not having the proper security groups configured is mainly visible by not getting the expected results at all, as packages are dropped by the EC2 instance rather then e.g. getting a connection refused.

Low level or basic properties
-----------------------------


EC2 instances
=============

Diskspace:

You want to avoid running into troubles with not having enough diskspace on your instance. Sadly by default you can only get space used from cloud watch. Using Amazon's own script you can push free space to cloud watch and pull this data via ZMON. Alternatively what we are doing is running the Prometheus Node exporter that allows you to pull not only disk space data from the EC2 node itself via HTTP.

Besides diskspace you also want to make sure that you have enough free INodes available!

Example check:

    https://github.com/zalando/zmon/tree/master/examples/check-definitions/ec2-diskspace.yaml

CPU:

Similar to diskspace CPU related metrics can be pulled from cloud watch or exposed via the Prometheus node exporter.

Memory:

Again as above you can either query via cloud watch, use Prometheus Node exporter to feed ZMON or you can go with low level snmp() but we in general don't encourage this.

Elastic Load Balancers
======================

As mentioned above you can query AWS Cloud Watch to get ELB specific metrics. On top of that the ZMON agent will also put data into the ELB entity allowing you to monitor instance and healthy instance count.

Auto scaling groups
===================

The agent creates an auto scaling group entity that provides you with the number of desired instances and the number of instances in a healthy state, this should enable you to monitor wether the ASG actually works and hosts spawn into a productive state.

RDS Instances
-------------


ElasticCache Redis
------------------


Application API monitoring
--------------------------

  In most cases your want to monitor what is really going on in your application itself, most likely you are interested in the number of requests you are receiving, how your latency looks like and how it looks in terms of status codes returned.
  Gathering this data you can paint a pretty good picture of what is going on and combined with other metrics and systems should help you find the problems or options for improvement.

  Assuming many applications are providing HTTP APIs hidden behind ELBs, you can use ZMON to gather data from Amazon's Cloud Watch, this gives you a very rough impression.

  For more detailed data we have different options for different languages/frameworks implemented, mainly for Spring Boot using the zmon-actuator or for Friboo (Clojure) and for Connextion(Python/Flask). In that case ZMON will gather the data by querying a JSON endpoint adhering to the DropWizard metrics standard with some convention on the naming of timers. But basically on timer per API PATH and status code.

  In any case, the http(url=...).actuator_metrics() will parse the data into a Python dict, that allows you to easy monitor and alert on changes in API behavior.
