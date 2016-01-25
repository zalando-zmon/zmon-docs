====================
Redis Data Structure
====================

Queues are Redis keys like ``zmon:queue:<NAME>`` of type "list", e.g. ``zmon:queue:default``.

New queue items are added by the ZMON Scheduler via the `Redis "rpush" command`_.

Important Redis key patterns are:

``zmon:checks:<CHECK-ID>``
    Set of entity IDs.
``zmon:checks:<CHECK-ID>:<ENTITY-ID>``
    List of check results.
``zmon:alerts``
    Set of all active alert IDs.
``zmon:alerts:<ALERT-ID>``
    Set of entity IDs in alert state.
``zmon:alerts:<ALERT-ID>:entities``
    Hash of alert captures.
``zmon:alerts:<ALERT-ID>:<ENTITY-ID>``
    Alert detail JSON containing alert start time, captures, worker, etc.

.. _Redis "rpush" command: http://redis.io/commands/rpush
