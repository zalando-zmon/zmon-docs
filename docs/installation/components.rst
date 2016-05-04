************************
Essential ZMON Components
************************

To use ZMON requires these four components: zmon-controller_, zmon-scheduler_, zmon-worker_, and zmon-eventlog-service_.

.. image:: ../images/components.svg

Controller
==========

zmon-controller_ runs the AngularJS frontend and serves as an endpoint for retrieving data and managing your ZMON deployment via REST API (with help from the command line client). It needs a connection configured to:

 * PostgreSQL to store/retrieve all kind of data: entities, checks, dashboards, alerts
 * Redis, to keep the state of ZMON's alerts
 * KairosDB, if you want charts/Grafana

To provide a means of authentication and authorization, you can choose between the following options:

 * A basic credential file
 * An OAuth2 identity provider, e.g., GitHub

Scheduler
=========

zmon-scheduler_ is responsible for keeping track of all existing entities, checks and alerts and scheduling checks in time for applicable entities, which are then executed by the worker.

Needs connections to:

 * Redis, which serves ZMON as a task queue
 * Controller, to get check/alerts/entities
 * Custom adapters might need connections for entity discovery in your platform

Worker
======

zmon-worker_ does the heavy lifting — executing tasks against entities and evaluating all alerts assigned to this check. Tasks are picked up from Redis and the resulting check value plus alert state changes are written back to Redis.

Needs connection to:
 * Redis to retrieve tasks and update current state
 * KairosDB if you want to have metrics
 * EventLog service to store history events for alert state changes

EventLog Service
================

zmon-eventlog-service_ is our slim implementation of an event store, keeping track of Events related to alert state changes as well as events like alert and check modification by the user.

Needs connection to:
 * PostgreSQL to store events using jsonb

.. _zmon-controller: https://github.com/zalando/zmon-controller
.. _zmon-scheduler: https://github.com/zalando/zmon-scheduler
.. _zmon-worker: https://github.com/zalando/zmon-worker
.. _zmon-eventlog-service: https://github.com/zalando/zmon-eventlog-service
