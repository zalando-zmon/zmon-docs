.. _installation:

************************
Installation
************************

  For easy demo use our provided Vagrant Box from:

    https://github.com/zalando/zmon

Requirements
============

The requirements below are all open soure technologies that need to be available for ZMON to run with all its features.

Redis
-----

The Redis service is one of the core dependencies, ZMON uses Redis for its task queue and to store its current state.

PostgreSQL
----------

PostgreSQL is ZMONs data store for entities, checks, alerts, dashboards and Grafana dashboards.

Cassandra
---------

Cassandra needs to be available for KairosDB if you want to have historic data and make use of Grafana, this is highly suggested.

KairosDB
--------

KairosDB is our time series database of choice. ZMON will store every metric gathered in KairosDB so that you can use it directly or via Graphana to access historic data. ZMON itself allows you to plot charts from KairosDB in Dashboard widgets or go to check/alert specific charts directly.

Components
==========

Controller
----------

The ZMON controller is the running the AngularJS frontend and serves as an endpoint for retrieving data and managing your ZMON deployment via REST API (with help of the command line client). It needs a connection configured to:

 * LDAP as its source for user data and credentials
 * PostgreSQL to store/retrieve all kind of data: entities, checks, dashboards, alerts
 * Redis keeping the state of ZMON's alerts
 * KairosDB if you want charts/Grafana

Scheduler
---------

The scheduler is responsible for keeping track of all existing entities, checks and alerts and scheduling checks in time for applicable entities which are then executed by the worker.

Needs connections to:
 * Redis as Redis serves ZMON as task queue
 * Controller to get check/alerts/entities
 * Custom adapters may need connections for entity discovery into your platform

Worker
------

The worker is the one doing the heavy lifting of executing tasks against entities, and evaluating all alerts assigned to this check. Tasks are picked up from Redis and the resulting check value plus alert state changes are written back to Redis.

Needs connection to:
 * Redis to retrieve tasks and update current state
 * KairosDB if you want to have metrics
 * EventLog service to store history events for alert state changes

EventLog Service
----------------

The EventLog service is our slim implementation of an event store, keeping track of Events related to alert state changes but also for events like alert and check modification by the user.

Needs connection to:
 * PostgreSQL to store events using jsonb

