************
Introduction
************

ZMON is an open-source platform monitoring tool developed at Zalando_ and used in production since early 2014. It offers unlimited scaling and storage with KairosDB and Cassandra; iOS and Android clients; and a common language (Python). ZMON splits checking and alerting responsibilities from processes that use entities to describe what's being monitored. RESTful APIs manage both, giving teams the power to configure their requirements autonomously. 

ZMON can be used by anyone, but offers particular advantages for technical organizations with many autonomous teams. Its frontend (see demo_) comes with Grafana "built-in," enabling teams to create and manage their own data-heavy, customized alerts and dashboards and share observed data with other teams. Alerts can be inherited and cloned, which makes reusing and sharing code and knowledge easy. 

ZMON also enables painless integration with CMDBs and service discovery and deploy tools via custom adapters or its built-in entity service's REST API. For an example, see zmon-aws-agent_ to learn how we connect AWS instance discovery with our monitoring.

ZMON Components
========

.. image:: images/components.svg

Using ZMON requires these four components: zmon-controller_, zmon-scheduler_, zmon-worker_, and zmon-eventlog-service_. The following components are optional:

- zmon-android_: An Android client for ZMON monitoring 
- zmon-ios_: An iOS client for ZMON monitoring 
- zmon-cli_: A command line client
- zmon-actuator_: Offering improved REST endpoint metrics for your Spring Boot projects
- zmon-aws-agent_: Works with the AWS API to retrieve "known" applications
- zmon-data-service_: Intercepts data from zmon-worker for Redis storage (for the frontend), KairosDB storage (for charting), and notification handling

ZMON Origins
========

ZMON was born in late 2013 during Zalando's annual `Hack Week`_, when a group of Zalando engineers aimed to develop a replacement for ICINGA. Scalability, manageability and flexibility were all critical, as Zalando's small teams needed to be able to monitor their services independent of each other. In early 2014, Zalando teams began migrating all checks from ICINGA. ZMON continues to serve Zalando Tech.

Entities
========

ZMON uses entities to describe your infrastructure or platform, and to bind check variables to fixed values.

.. code-block:: json

  {
	"type":"host",
	"id":"cassandra01",
	"host":"cassandra01",
	"role":"cassandra-host",
	"ip":"192.168.1.17",
	"dc":"data-center-1"
  }

Or more abstract objects:

.. code-block:: json

  {
  	"type":"postgresql-cluster",
  	"id":"article-cluster",
  	"name":"article-cluster",
  	"shards": {
		"shard1":"articledb01:5432/shard1",
		"shard2":"articledb02:5432/shard2"
  	}
  }

Entity properties are not defined in any schema, so you can add properties as you see fit. enabling later a finer grained filtering or selection of entities. E.g. our host entities also include a physical model to later select the proper hardware checks.

Checks
======

A check describes how data is acquired, where the two key properties are: An entity filter and a check command. The entity filter selects a subset of entities by requiring an overlap on specified properties.

Example:

.. code-block:: json

  {
    "type":"host", "role":"cassandra-host"
  }

The check command itself is an executable Python_ expression. ZMON provides a lot of custom functions that are bound to the selected entity. The following example uses our PostgreSQL wrapper to execute a query on every shard defined above.

.. code-block:: python

  # sql() in this context is aware of the "shards" property

  sql().execute("SELECT 1 as a").results()

A check command always returns a value to the alert, this can be of any type.

For people less familiar with Pythons ZMON also allows you to define a function on the top level and define your command in an easier less functional way:

.. code-block:: python

  def check():
    # entity will be injected as entity
    return sql().execute("SELECT 1 as a").result()

Alerts
======

A basic alert consists of an alert condition, and entity filter, and a team. More properties are available like exclude entities, details are explained later. Any alert has only two states, up or down. We do not support levels of criticality, and something like unknown. And alert is up, if it yields anything but False. This also includes exceptions thrown from check or alert expression, e.g. in case of connection problems.

Going back to the PostgreSQL check the below alert would pop up if either shard is not reachable, making use of exceptions bubbling up from the check command itself.

.. code-block:: yaml

  team: database
  entities:
    - type: postgresql-cluster
  alert_condition: "False"

Alerts support parameters to the alert condition via UI, thus teams can decide to easily implement different thresholds. Together with the priority field defining the dashboard color this enables users to render their dashboards according to their understanding of priorities.

Dashboards
==========

Dashboards consists of a widget area where you can render important data in a graphical way, with charts, gauges or just text. The second section consists of rendering all active alerts for the team filter defined on dashboard level. Using the team filter you select the alerts you want to have on your dashboards, multiple teams can be specified. Additionally TAGs are supported to subselect topics.

.. image:: images/dashboard.png

REST API and CLI
================

ZMON features a REST API to manage all the essential moving parts during your daily work: creating and updating entities, which allows easy sync with your existing infrastructure. Creating and modifying checks and alerts is possible, the scheduler will pick changes up quickly, no need to restart or deploy anything.

To make your life easier there is the command line client, a slim wrapper around the REST API, that makes it easier for humans to work with it, hiding away some issues and enabling e.g. working with YAML files or pushing collections of entities.

.. _Python: http://www.python.org
.. _Zalando: https://tech.zalando.de/
.. _zmon-controller: https://github.com/zalando/zmon-controller
.. _zmon-demo: https://github.com/zalando/zmon-demo
.. _zmon-scheduler: https://github.com/zalando/zmon-scheduler
.. _zmon-worker: https://github.com/zalando/zmon-worker
.. _zmon-eventlog-service: https://github.com/zalando/zmon-eventlog-service
.. _zmon-android: https://github.com/zalando/zmon-android
.. _zmon-ios: https://github.com/zalando/zmon-ios
.. _zmon-cli: https://github.com/zalando/zmon-cli
.. _zmon-actuator: https://github.com/zalando/zmon-actuator
.. _zmon-aws-agent: https://github.com/zalando/zmon-aws-agent
.. _zmon-data-service: https://github.com/zalando/zmon-data-service
.. _Hack Week: https://tech.zalando.de/blog/?tags=Hack%20Week
