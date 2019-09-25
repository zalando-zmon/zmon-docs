Scalyr
------

Wrapper
^^^^^^^

The ``scalyr()`` wrapper enables querying Scalyr from your AWS worker if the credentials have been specified for the worker instance(s).
For more description of each type of query, please refer to https://www.scalyr.com/help/api .

Default parameters:

* ``minutes`` specifies the start time of the query. I.e. "5" will mean 5 minutes ago.
* ``end`` specifies the end time of the query. I.e. "2" will mean until 2 minutes ago. If set to ``None``, then the end is set to 24h after ``minutes``. The default "0" means `now`.

   

.. py:method:: count(query, minutes=5, end=0)

    Run a count query against Scalyr, depending on number of queries you may run into rate limit.


    ::

        scalyr().count(' ERROR ')


.. py:method:: timeseries(query, minutes=30, end=0)

    Runs a timeseries query against Scalyr with more generous rate limits. (New time series are created on the fly by Scalyr)


.. py:method:: facets(filter, field, max_count=5, minutes=30, end=0)

    This method is used to retrieve the most common values for a field.


.. py:method:: logs(query, max_count=100, minutes=5, continuation_token=None, columns=None, end=0)

    Runs a query against Scalyr and returns logs that match the query. At most ``max_count`` log lines will be returned.
    More can be fetched with the same query by passing back the continuation_token from the last response into the
    logs method.

    Specific columns can be returned (as defined in scalyr parser) using the columns array e.g. ``columns=['severity','threadName','timestamp']``.
    If this is unspecified, only the message column will be returned.

    An example logs result as JSON:

    .. code-block:: json

        {
            "messages": [
               "message line 1",
               "message line 2"
            ],
            "continuation_token": "a token"
        }


.. py:method:: power_query(query, minutes=5, end=0)

    Runs a power query against Scalyr and returns the results as response. You can create and test power queries also via the _UI:https://eu.scalyr.com/query . More information on power queries can be found _here:https://eu.scalyr.com/help/power-queries

    An example response as JSON:

    .. code-block:: json

        {
            "columns": [
                {
                    "name": "cluster"
                },
                {
                    "name": "application"
                },
                {
                    "name": "volume"
                }
            ],
            "warnings": [],
            "values": [
                [
                    "cluster-1-eu-central-1:kube-1",
                    "application-2",
                    9481810.0
                ],
                [
                    "cluster-2-eu-central-1:kube-1",
                    "application-1",
                    8109726.0
                ]
            ],
            "matchingEvents": 8123.0,
            "status": "success",
            "omittedEvents": 0.0
        }


Custom Scalyr Region
^^^^^^^^^^^^^^^^^^^^

By default the Scalyr wrapper uses https://www.scalyr.com/ as the default region. Overriding is possible using ``scalyr(scalyr_region='eu')`` if you want to use their Europe environment https://eu.scalyr.com/.


    ::

        scalyr(scalyr_region='eu').count(' ERROR ')
