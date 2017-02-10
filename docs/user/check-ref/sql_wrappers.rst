.. _sql-function:

SQL
---

.. py:function:: sql([shard])

    Provides a wrapper for connection to PostgreSQL database and allows
    executing queries. All queries are executed in read-only transactions.
    The connection wrapper requires one parameters: list of shard connections.
    The shard connections must come from the entity definition (see :ref:`database-entities`).
    Example query for log database which returns a primitive long value:

    .. code-block:: python

        sql().execute("SELECT count(*) FROM zl_data.log WHERE log_created > now() - '1 hour'::interval").result()

    Example query which will return a single dict with keys ``a`` and ``b``::

        sql().execute('SELECT 1 AS a, 2 AS b').result()

    The SQL wrapper will automatically sum up values over all shards::

        sql().execute('SELECT count(1) FROM zc_data.customer').result() # will return a single integer value (sum over all shards)

    It's also possible to query a single shard by providing its name::

        sql(shard='customer1').execute('SELECT COUNT(1) AS c FROM zc_data.customer').results() # returns list of values from a single shard
    
    It's also possible to query another database on the same server providing::
    
        sql(shards={'customer_db' : entity['host'] + ':' + str(entity['port']) + '/antother_db'}).execute('SELECT COUNT(1) AS c FROM my_table').results()

    To execute a SQL statement on all LIVE customer shards, for example, use the following entity filter:

    .. code-block:: json

        [
            {
                "type":        "database",
                "name":        "customer",
                "environment": "live",
                "role":        "master"
            }
        ]

    The check command will have the form

    .. code-block:: python

        >>> sql().execute('SELECT 1 AS a').result()
        8
        # Returns a single value: the sum over the result from all shards

        >>> sql().execute('SELECT 1 AS a').results()
        [{'a': 1}, {'a': 1}, {'a': 1}, {'a': 1}, {'a': 1}, {'a': 1}, {'a': 1}, {'a': 1}]
        # Returns a list of the results from all shards

        >>> sql(shard='customer1').execute('SELECT 1 AS a').results()
        [{'a': 1}]
        # Returns the result from the specified shard in a list of length one

        >>> sql().execute('SELECT 1 AS a, 2 AS b').result()
        {'a': 8, 'b': 16}
        # Returns a dict of the two values, which are each the sum over the result from all shards

    The results() function has several additional parameters: ::

        sql().execute('SELECT 1 AS ONE, 2 AS TWO FROM dual').results([max_results=100], [raise_if_limit_exceeded=True])

    ``max_results``
        The maximum number of rows you expect to get from the call. If not specified, defaults to 100. You cannot have an
        unlimited number of rows. There is an absolute maximum of 1,000,000 results that cannot be overridden.
        Note: If you require processing of larger dataset, it
        is recommended to revisit architecture of your monitoring subsystem and possibly move logic that does calculation
        into external web service callable by ZMON 2.0.

    ``raise_if_limit_exceeded``
        Raises an exception if the limit of rows would have been exceeded by the issued query.

.. py:function:: orasql()

    Provides a wrapper for connection to Oracle database and allows
    executing queries. All queries are executed in read-only transactions.
    The connection wrapper requires three parameters: host, port and sid,
    that must come from the entity definition (see :ref:`database-entities`).
    One idiosyncratic behaviour to be aware, is that when your query produces
    more than one value, and you get a dict with keys being the column names
    or aliases you used in your query, you will always get back those keys
    *in uppercase*. For clarity, we recommend that you write all aliases
    and column names in uppercase, to avoid confusion due to case changes.
    Example query of the simplest query, which returns a single value:

    .. code-block:: python

        orasql().execute("SELECT 'OK' from dual").result()

    Example query which will return a single dict with keys ``ONE`` and ``TWO``::

        orasql().execute('SELECT 1 AS ONE, 2 AS TWO from dual').result()

    To execute a SQL statement on a LIVE server, tagged with the name business_intelligence, for example,
    use the following entity filter:

    .. code-block:: json

        [
            {
                "type":        "oracledb",
                "name":        "business_intelligence",
                "environment": "live",
                "role":        "master"
            }
        ]


.. py:function:: exacrm()

    Provides a wrapper for connection to the CRM Exasol database executing
    queries.
    The connection wrapper requires one parameter: the query.

    Example query:

    .. code-block:: python

        exacrm().execute("SELECT 'OK';").result()

    To execute a SQL statement on the itr-crmexa* servers use the following
    entity filter:

    .. code-block:: json

        [
           {
               "type": "host",
                "host_role_id": "117"
           }
        ]

.. py:function:: mysql([shard])

    Provides a wrapper for connection to MySQL database and allows
    executing queries.
    The connection wrapper requires one parameters: list of shard connections.
    The shard connections must come from the entity definition (see :ref:`database-entities`).
    Example query of the simplest query, which returns a single value:

    .. code-block:: python

        mysql().execute("SELECT count(*) FROM mysql.user").result()

    Example query which will return a single dict with keys ``h`` and ``u``::

        mysql().execute('SELECT host AS h, user AS u FROM mysql.user').result()

    The SQL wrapper will automatically sum up values over all shards::

        mysql().execute('SELECT count(1) FROM zc_data.customer').result() # will return a single integer value (sum over all shards)

    It's also possible to query a single shard by providing its name::

        mysql(shard='customer1').execute('SELECT COUNT(1) AS c FROM zc_data.customer').results() # returns list of values from a single shard

    To execute a SQL statement on all LIVE customer shards, for example, use the following entity filter:

    .. code-block:: json

        [
            {
                "type":        "mysqldb",
                "name":        "lounge",
                "environment": "live",
                "role":        "master"
            }
        ]
