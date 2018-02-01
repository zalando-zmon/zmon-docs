Scalyr
------

Wrapper
^^^^^^^

The ``scalyr()`` wrapper enables querying Scalyr from your AWS worker if the credentials have been specified for the worker instance(s).


.. py:method:: count(query, minutes=5)

    Run a count query against Scalyr, depending on number of queries you may run into rate limit.


    ::

        scalyr().count(' ERROR ')


.. py:method:: timeseries(query, minutes=30)

    Runs a timeseries query against Scalyr with more generous rate limits. (New time series are created on the fly by Scalyr)


.. py:method:: facets(filter, field, max_count=5, minutes=30, prio='low')

    This method is used to retrieve the most common values for a field.


.. py:method:: messages(query, max_count=100, minutes=5, continuation_token=None)

    Runs a query against Scalyr and returns messages that match the query. At most ``max_count`` messages will be returned
    but more can be fetched with the same query by passing back the continuation_token from the last response back into the 
    messages method.
    
    An example messages result as JSON:

    .. code-block:: json

        {
            "messages": [
               "message line 1",
               "message line 2"
            ],
            "continuation_token": "a token"
        }


Custom Scalyr Region
^^^^^^^^^^^^^^^^^^^^

By default the Scalyr wrapper uses https://www.scalyr.com/ as the default region. Overriding is possible using ``scalyr(scalyr_region='eu')`` if you want to use their Europe environment https://eu.scalyr.com/. 


    ::

        scalyr(scalyr_region='eu').count(' ERROR ')
