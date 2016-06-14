Zomcat
------

Retrieve zomcat instance status (memory, CPU, threads). ::

    zomcat().health()

This would return a dict like:

.. code-block:: json

    {
        "cpu_percentage": 5.44,
        "gc_percentage": 0.11,
        "gcs_per_sec": 0.25,
        "heap_memory_percentage": 6.52,
        "heartbeat_enabled": true,
        "http_errors_per_sec": 0.0,
        "jobs_enabled": true,
        "nonheap_memory_percentage": 20.01,
        "requests_per_sec": 1.09,
        "threads": 128,
        "time_per_request": 42.58
    }

Most of the values are retrieved via JMX:

``cpu_percentage``
    CPU usage in percent (retrieved from JMX).

``gc_percentage``
    Percentage of time spent in garbage collection runs.

``gcs_per_sec``
    Garbage collections per second.

``heap_memory_percentage``
    Percentage of heap memory used.

``nonheap_memory_percentage``
    Percentage of non-heap memory (e.g. permanent generation) used.

``heartbeat_enabled``
    Boolean indicating whether heartbeat.jsp is enabled (``true``) or not (``false``). If ``/heartbeat.jsp`` cannot be retrieved, the value is ``null``.

``http_errors_per_sec``
    Number of Tomcat HTTP errors per second (all 4xx and 5xx HTTP status codes).

``jobs_enabled``
    Boolean indicating whether jobs are enabled (``true``) or not (``false``). If ``/jobs.monitor`` cannot be retrieved, the value is ``null``.

``requests_per_sec``
    Number of HTTP/AJP requests per second.

``threads``
    Total number of threads.

``time_per_request``
    Average time in milliseconds per HTTP/AJP request.
