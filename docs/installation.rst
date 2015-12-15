.. _installation:

************************
Installation
************************

  For an easy demo use our provided Vagrant Box from:

    https://github.com/zalando/zmon

Requirements
============

The requirements below are all open source technologies that need to be available for ZMON to run with all its features.

Redis
-----

The Redis service is one of the core dependencies, ZMON uses Redis for its task queue and to store its current state.

PostgreSQL
----------

PostgreSQL is ZMONs data store for entities, checks, alerts, dashboards and Grafana dashboards. The entities service relies on PostgreSQL's jsonb data type thus you need a PostgreSQL 9.4+ running.

Cassandra
---------

Cassandra needs to be available for KairosDB if you want to have historic data and make use of Grafana, which is highly suggested.

KairosDB
--------

KairosDB is our time series database of choice. ZMON will store every metric gathered in KairosDB so that you can use it directly or via Graphana to access historic data. ZMON itself allows you to plot charts from KairosDB in Dashboard widgets or go to check/alert specific charts directly.

Components
==========

Controller
----------

The ZMON controller is running the AngularJS frontend and serves as an endpoint for retrieving data and managing your ZMON deployment via REST API (with help of the command line client). It needs configured connections to:

 * PostgreSQL to store/retrieve all kind of data: entities, checks, dashboards, alerts
 * Redis keeping the state of ZMON's alerts
 * KairosDB if you want charts/Grafana

To provide means of authentication and authorization you can choose between the following options:

 * Basic credential file
 * Pick an Oauth2 identity provider, e.g., Github

Scheduler
---------

The scheduler is responsible for keeping track of all existing entities, checks and alerts and scheduling checks in time for applicable entities which are then executed by the worker.

Needs connections to:

 * Redis which serves as a task queue for ZMON
 * Controller to get check/alerts/entities
 * Custom adapters may need connections for entity discovery into your platform

Worker
------

The worker does the heavy lifting of executing tasks against entities, and evaluating all alerts assigned to this check. Tasks are queried from Redis and the resulting check value plus alert state changes are written back to Redis.

Needs connection to:
 * Redis to retrieve tasks and update current state
 * KairosDB if you want to have metrics
 * EventLog service to store history events for alert state changes

EventLog Service
----------------

The EventLog service is our slim implementation of an event store, keeping track of Events related to alert state changes but also for events like alert and check modification by the user.

Needs connection to:
 * PostgreSQL to store events using jsonb


Configuration options
=====================

If you decide not to run the supplied Docker images, you will find the relevant configuration options in the project specific configuration files ("application.yaml","web.conf","application.properties"). We suggest to run Docker images for now and use the following environment variables to specify your configuration options.

Controller
----------



Scheduler
---------

Specify the Redis server you want to use:

.. code-block:: bash

   SCHEDULER_REDIS_HOST
   SCHEDULER_REDIS_PORT

Specify where you run the controller hosting the entity service:

.. code-block:: bash

   SCHEDULER_ENTITY_SERVICE_URL

Specify the base URL to the controller for getting checks and alerts:

.. code-block:: bash

  SCHEDULER_CONTROLLER_URL

Worker
------

Specify the Redis server used:

.. code-block:: bash

   WORKER_REDIS_HOST
   WORKER_REDIS_PORT

If you plan to access your PostgreSQL cluster specify the credentials below. We suggest to use a distinct user for ZMON with limited read only privileges.

.. code-block:: bash

   WORKER_POSTGRESQL_USER
   WORKER_POSTGRESQL_PASSWORD

If you need to access MySQL specify the user credentials below, again we suggest to use a user with limited privileges only.

.. code-block:: bash

   WORKER_MYSQL_USER
   WORKER_MYSQL_PASSWORD
