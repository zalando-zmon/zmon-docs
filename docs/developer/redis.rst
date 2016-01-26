====================
Redis Data Structure
====================

ZMON stores its primary working data in Redis. This page describes the used Redis keys and data structures.

Queues are Redis keys like ``zmon:queue:<NAME>`` of type "list", e.g. ``zmon:queue:default``.

New queue items are added by the ZMON Scheduler via the `Redis "rpush" command`_.

Important Redis key patterns are:

``zmon:queue:<QUEUE-NAME>``
    List of worker tasks for given queue.
``zmon:checks``
    Set of all executed check IDs.
``zmon:checks:<CHECK-ID>``
    Set of entity IDs having check results.
``zmon:checks:<CHECK-ID>:<ENTITY-ID>``
    List of last N check results. The first list item contains the most recent check result.
    Each check result is a JSON object with the keys ``ts`` (result timestamp), ``td`` (check duration), ``value`` (actual result value) and ``worker`` (ID of worker having produced the check result).
``zmon:alerts``
    Set of all active alert IDs.
``zmon:alerts:<ALERT-ID>``
    Set of entity IDs in alert state.
``zmon:alerts:<ALERT-ID>:entities``
    Hash of entity IDs to alert captures. This hash contains *all* entity IDs matched by the alert, i.e. not only entities in alert state.
``zmon:alerts:<ALERT-ID>:<ENTITY-ID>``
    Alert detail JSON containing alert start time, captures, worker, etc.
``zmon:downtimes``
    Set of all alert IDs having downtimes.
``zmon:downtimes:<ALERT-ID>``
    Set of all entity IDs having a downtime for this alert.
``zmon:downtimes:<ALERT-ID>:<ENTITY-ID>``
    Hash of downtimes for this entity/alert. Each hash value is a JSON object with keys ``start_time``, ``end_time`` and ``comment``.
``zmon:active_downtimes``
    Set of currently active downtimes. Each set item has the form ``<ALERT-ID>:<ENTITY-ID>:<DOWNTIME-ID>``.
``zmon:metrics``
    Set of worker and scheduler IDs with metrics.
``zmon:metrics:<WORKER-OR-SCHEDULER-ID>:ts``
    Timestamp of last worker or scheduler metrics update.
``zmon:metrics:<WORKER-OR-SCHEDULER-ID>:check.count``
    Increasing counter of executed (or scheduled) checks.

.. _Redis "rpush" command: http://redis.io/commands/rpush
