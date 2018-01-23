Cassandra
---------

Provides access to a Cassandra cluster via ``cassandra()`` wrapper object.

.. py:function:: cassandra(node, keyspace, username=None, password=None, port=9042, connect_timeout=1, protocol_version=3)

    Initialize cassandra wrapper.

    :param node: Cassandra host.
    :type node: str

    :param keyspace: Cassandra keyspace used during the session.
    :type keyspace: str

    :param username: Username used in connection. It is recommended to use unprivileged user for cassandra checks.
    :type username: str

    :param password: Password used in connection.
    :type password: str

    :param port: Cassandra host port. Default is 9042.
    :type port: int

    :param connect_timeout: Connection timeout.
    :type connect_timeout: int

    :param protocol_version: Protocol version used in connection. Default is 3.
    :type protocol_version: str

.. note::

    You should always use an unprivileged user to access your databases. Use ``plugin.cassandra.user`` and ``plugin.cassandra.pass`` to configure credentials for the zmon-worker.

.. py:function:: execute(stmt)

    Execute a CQL statement against the specified keyspace.

    :param stmt: CQL statement
    :type stmt: str

    :return: CQL result
    :rtype: list
