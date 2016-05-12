.. _requirements:

************
Requirements
************

The requirements below are all open soure technologies that need to be available for ZMON to run with all its features.

Redis
=====

The Redis service is one of the core dependencies, ZMON uses Redis for its task queue and to store its current state.

PostgreSQL
==========

PostgreSQL is ZMONs data store for entities, checks, alerts, dashboards and Grafana dashboards. The entities service relies on PostgreSQL's jsonb data type thus you need a PostgreSQL 9.4+ running.

Cassandra
=========

Cassandra needs to be available for KairosDB if you want to have historic data and make use of Grafana, this is highly suggested.

KairosDB
========

KairosDB is our time series database of choice. ZMON will store every metric gathered in KairosDB so that you can use it directly or via Graphana to access historic data. ZMON itself allows you to plot charts from KairosDB in Dashboard widgets or go to check/alert specific charts directly.
