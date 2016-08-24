AppDynamics
-------------

Enable AppDynamics Healthrule violations check and *optionally* query underlying Elasticsearch cluster raw logs.

.. py:function:: appdynamics(url=None, username=None, password=None, es_url=None, index_prefix='')

    ::

    Initialize AppDynamics wrapper.

    :param url: Appdynamics url.
    :type url: str

    :param username: Appdynamics username.
    :type username: str

    :param password: Appdynamics password.
    :type password: str

    :param es_url: Appdynamics Elasticsearch cluster url.
    :type es_url: str

    :param index_prefix: Appdynamics Elasticsearch cluster logs index prefix.
    :type index_prefix: str

.. note::

    If ``username`` and ``password`` are not supplied, then OAUTH2 will be used.

    If ``appdynamics()`` is initialized with no args, then plugin configuration values will be used.

Methods of AppDynamics
^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: healthrule_violations(application, time_range_type=BEFORE_NOW, duration_in_mins=5, start_time=None, end_time=None, severity=None)

    ::

    Return Healthrule violations for AppDynamics application.

    :param application: Application name or ID
    :type application: str

    :param time_range_type: Valid time range type. Valid range types are BEFORE_NOW, BEFORE_TIME, AFTER_TIME and BETWEEN_TIMES. Default is BEFORE_NOW.
    :type time_range_type: str

    :param duration_in_mins: Time duration in mins. Required for BEFORE_NOW, AFTER_TIME, BEFORE_TIME range types. Default is 5 mins.
    :type duration_in_mins: int

    :param start_time: Start time (in milliseconds) from which the metric data is returned. Default is 5 mins ago.
    :type start_time: int

    :param end_time: End time (in milliseconds) until which the metric data is returned. Default is now.
    :type end_time: int

    :param severity: Filter results based on severity. Valid values are CRITICAL or WARNING.
    :type severity: str

    :return: List of healthrule violations
    :rtype: list

    Example query:

    .. code-block:: python

        appdynamics('https://appdynamics/controller/rest').healthrule_violations('49', time_range_type='BEFORE_NOW', duration_in_mins=5)

        [
            {
                affectedEntityDefinition: {
                    entityId: 408,
                    entityType: "BUSINESS_TRANSACTION",
                    name: "/error"
                },
                detectedTimeInMillis: 0,
                endTimeInMillis: 0,
                id: 39637,
                incidentStatus: "OPEN",
                name: "Backend errrors (percentage)",
                severity: "CRITICAL",
                startTimeInMillis: 1462244635000,
            }
        ]

.. py:function:: query_logs(q='', body=None, size=100, source_type=SOURCE_TYPE_APPLICATION_LOG, duration_in_mins=5)

    ::

    Perform search query on AppDynamics ES logs.

    :param q: Query string used in search.
    :type q: str

    :param body: (dict) holding an ES query DSL.
    :type body: dict

    :param size: Number of hits to return. Default is 100.
    :type size: int

    :param source_type: ``sourceType`` field filtering. Default to ``application-log``, and will be part of ``q``.
    :type source_type: str

    :param duration_in_mins: Duration in mins before current time. Default is 5 mins.
    :type duration_in_mins: int

    :return: ES query result ``hits``.
    :rtype: list

.. py:function:: count_logs(q='', body=None, source_type=SOURCE_TYPE_APPLICATION_LOG, duration_in_mins=5)

    ::

    Perform count query on AppDynamics ES logs.

    :param q: Query string used in search. Will be ingnored if ``body`` is not None.
    :type q: str

    :param body: (dict) holding an ES query DSL.
    :type body: dict

    :param source_type: ``sourceType`` field filtering. Default to ``application-log``, and will be part of ``q``.
    :type source_type: str

    :param duration_in_mins: Duration in mins before current time. Default is 5 mins. Will be ignored if ``body`` is not None.
    :type duration_in_mins: int

    :return: Query match count.
    :rtype: int

.. note::

    In case of passing an ES query DSL in ``body``, then all filter parameters should be explicitly added in the query body (e.g. ``eventTimestamp``, ``application_id``, ``sourceType``).
