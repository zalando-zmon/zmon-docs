Scalyr
------

The ``scalyr()`` wrapper enables querying Scalyr from your AWS worker if the credentials have been specified for the worker instance(s).

.. py:method:: count(query, minutes=5)

  Run a count query against Scalyr, depending on number of queries you may run into rate limit.


  ::

        scalyr().count(' ERROR ')


.. py:method:: timeseries(query, minutes=30)

  Runs a timeseries query agains Scalyr with more generous rate limits. (New time series are created on the fly by Scalyr)
