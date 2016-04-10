************************
Components
************************

.. image:: images/components.svg

Controller
==========

The ZMON controller is the running the AngularJS frontend and serves as an endpoint for retrieving data and managing your ZMON deployment via REST API (with help of the command line client). It needs a connection configured to:

 * PostgreSQL to store/retrieve all kind of data: entities, checks, dashboards, alerts
 * Redis keeping the state of ZMON's alerts
 * KairosDB if you want charts/Grafana

To provide means of authentication and authorization you do can choose between the following options:

 * Basic credential file
 * Pick an Oauth2 identity provider, e.g., Github

Scheduler
=========

The scheduler is responsible for keeping track of all existing entities, checks and alerts and scheduling checks in time for applicable entities which are then executed by the worker.

Needs connections to:

 * Redis as Redis serves ZMON as task queue
 * Controller to get check/alerts/entities
 * Custom adapters may need connections for entity discovery into your platform

Worker
======

The worker is the one doing the heavy lifting of executing tasks against entities, and evaluating all alerts assigned to this check. Tasks are picked up from Redis and the resulting check value plus alert state changes are written back to Redis.

Needs connection to:
 * Redis to retrieve tasks and update current state
 * KairosDB if you want to have metrics
 * EventLog service to store history events for alert state changes

EventLog Service
================

The EventLog service is our slim implementation of an event store, keeping track of Events related to alert state changes but also for events like alert and check modification by the user.

Needs connection to:
 * PostgreSQL to store events using jsonb
