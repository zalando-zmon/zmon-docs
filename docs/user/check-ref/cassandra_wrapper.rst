Cassandra
---------

Provides access to a Cassandra cluster via ``cassandra()`` wrapper object.

.. py:function:: cassandra(host, port=9042)

  Creates a session connecting to the specified host and port.


.. note::

    You should always use an unprivileged user to access your databases. Use ``plugin.cassandra.user`` and ``plugin.cassandra.pass`` to configure credentials for the zmon-worker.

.. py:function:: execute(keyspace, stmt)

  Execute a CQL statement against the specified keyspace.
