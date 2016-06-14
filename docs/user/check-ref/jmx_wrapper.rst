JMX
---

To use JMXQuery, run "jmxquery" (this is not yet released)

Queries beans’ attributes on hosts specified in entities filter::

    jmx().query('java.lang:type=Memory', 'HeapMemoryUsage', 'NonHeapMemoryUsage').results()

Another example::

    jmx().query('java.lang:type=Threading', 'ThreadCount', 'DaemonThreadCount', 'PeakThreadCount').results()

This would return a dict like:

.. code-block:: json

    {
        "DaemonThreadCount": 524,
        "PeakThreadCount": 583,
        "ThreadCount": 575
    }
