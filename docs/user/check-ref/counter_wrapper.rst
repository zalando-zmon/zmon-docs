Counter
-------

The ``counter()`` function allows you to get increment rates of increasing counter values.
Main use case for using ``counter()`` is to get rates per second of JMX counter beans (e.g. "Tomcat Requests").
The counter function requires one parameter ``key`` to identify the counter.


.. py:method:: per_second(value)

    ::

        counter('requests').per_second(get_total_requests())

    Returns the value's increment rate per second. Value must be a float or integer.

.. py:method:: per_minute(value)

    ::

        counter('requests').per_minute(get_total_requests())

    Convenience method to return the value's increment rate per minute (same as result of ``per_second()`` divided by 60).

Internally counter values and timestamps are stored in Redis.
