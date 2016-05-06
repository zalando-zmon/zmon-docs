.. _monitoringonaws:

*****************
Monitoring on AWS
*****************

This section assumes that you're running zmon-aws-agent_, which automatically discovers your EC2 instances, auto-scaling groups, ELBs, and more.

Security Groups
---------------

Depending on your AWS setup, you'll probably have to open particular ports/instances to access from ZMON. Using a limited set of ports to expose management APIs and the Prometheus node exporter will make your life easier.

Not having the proper security groups configured is mainly visible by not getting the expected results at all, as packages are dropped by the EC2 instance rather then e.g. getting a connection refused.

Low-Level or Basic Properties
-----------------------------

EC2 Instances
=============

Having enough **diskspace** on your instance is important; `here's a sample check`_. By default, you can only get space used from CloudWatch_. Using Amazon's own script, you can push free space to CloudWatch and pull this data via ZMON. Alternatively, you can run the `Prometheus Node exporter`_ to pull disk space data from the EC2 node itself via HTTP. 

Similarly, you can pull CPU-related metrics from CloudWatch. The Prometheus Node exporter also exposes these metrics.

You also need enough available **INodes**.

Regarding **memory**, you can either query via CloudWatch, use Prometheus Node exporter to feed ZMON, or go with low-level snmp(). In general, we don't encourage this.

Elastic Load Balancers
======================

You can query AWS CloudWatch to get ELB-specific metrics. The ZMON agent will put data into the ELB entity, allowing you to monitor instance and healthy instance count.

Auto-Scaling Groups
===================

ZMON's agent creates an auto-scaling group entity that provides you with the number of desired instances and the number of instances in a healthy state. This enables you to monitor whether the ASG actually works and hosts spawn into a productive state.

RDS Instances
-------------

Documentation to come.

ElasticCache Redis
------------------

Documentation to come.

Application API Monitoring
--------------------------

When monitoring an application, you'll usually want to check the number of received requests, latency patterns, and the number of returned status codes. These data points form a pretty clear picture of what is going on with the application. Additional metrics will help you find problems as well as opportunities for improvement. Assuming that your applications provide HTTP APIs hidden behind ELBs, you can use ZMON to gather this data from CloudWatch.

For more detailed data, ZMON offers options for different languages and frameworks; for example, zmon-actuator_ for Spring Boot. ZMON gathers the data by querying a JSON endpoint adhering to the DropWizard metrics standard with some convention on the naming of timers. But basically on timer per API PATH and status code. (We also recommend checking out Friboo_ for working with Clojure, and the Python/Flask framework Connexion_.

In any case, the http(url=...).actuator_metrics() will parse the data into a Python dict that allows you to easy monitor and alert on changes in API behavior.

.. _zmon-aws-agent: https://github.com/zalando/zmon-aws-agent
.. _CloudWatch: https://aws.amazon.com/cloudwatch/
.. _Prometheus Node exporter: https://github.com/prometheus/node_exporter
.. _here's a sample check: https://github.com/zalando/zmon/tree/master/examples/check-definitions/ec2-diskspace.yaml
.. _zmon-actuator: https://github.com/zalando/zmon-actuator
.. _Friboo: https://github.com/zalando-stups/friboo
.. _Connexion: https://github.com/zalando/connexion
