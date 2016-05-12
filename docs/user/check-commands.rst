.. _check-commands:

**************
Check Commands
**************

To give an overview of available commands, we divided them into several
categories.

HTTP
----

Access to HTTP (and HTTPS) endpoints is provided by the :py:func:`http` function.

.. py:function:: http(url, [timeout=10], [max_retries=0], [verify=True], [oauth2=True])

    :param str url: The URL that is to be queried. See below for details.
    :param float timeout: The timeout for the HTTP request, in seconds. Defaults to :py:obj:`10`.
    :param int max_retries: The number of times the HTTP request should be retried if it fails. Defaults to :py:obj:`0`.
    :param bool verify: Can be set to :py:obj:`False` to disable SSL certificate verification.
    :return: An object encapsulating the response from the server. See below.

        For checks on entities that define the attributes :py:attr:`url` or :py:attr:`host`, the given URL may be relative. In that case, the URL :samp:`http://<{value}><{url}>` is queried, where :samp:`<{value}>` is the value of that attribute, and :samp:`<{url}>` is the URL passed to this function. If an entity defines both :py:attr:`url` and :py:attr:`host`, the former is used.

    This function cannot query URLs using a scheme other than HTTP or HTTPS; URLs that do not start with :samp:`http://` or :samp:`https://` are considered to be relative.

    Sample usage::

        http('http://www.example.org/data?fetch=json')

HTTP Responses
^^^^^^^^^^^^^^

The object returned by the :py:func:`http` function provides methods: :py:meth:`json`, :py:meth:`text`, :py:meth:`headers`, :py:meth:`cookies`, :py:meth:`content_size`, :py:meth:`time` and :py:meth:`code`.

.. py:method:: json()

    This method returns an object representing the content of the JSON response from the queried endpoint. Usually, this will be a map (represented by a Python :py:obj:`dict`), but, depending on the endpoint, it may also be a list, string, set, integer, floating-point number, or Boolean.

.. py:method:: text()

    Returns the text response from queried endpoint::

        http("/heartbeat.jsp", timeout=5).text().strip()=='OK: JVM is running'

    Since we’re using a relative url, this check has to be defined for
    specific entities (e.g. type=zomcat will run it on all zomcat
    instances). The strip function removes all leading and trailing
    whitespace.

.. py:method:: headers()

    Returns the response headers in a case-insensitive dict-like object::

        http("/api/json", timeout=5).headers()['content-type']=='application/json'

.. py:method:: cookies()

    Returns the response cookies in a dict like object::

        http("/heartbeat.jsp", timeout=5).cookies()['my_custom_cookie'] == 'custom_cookie_value'

.. py:method:: content_size()

    Returns the length of the response content::

        http("/heartbeat.jsp", timeout=5).content_size() > 1024

.. py:method:: time()

    Returns the elapsed time in seconds until response was received::

        http("/heartbeat.jsp", timeout=5).time() > 1.5

.. py:method:: code()

    Return HTTP status code from the queried endpoint.::

        http("/heartbeat.jsp", timeout=5).code()

.. py:method:: actuator_metrics(prefix='zmon.response.')

    Parses the json result of a metrics endpoint into a map ep->method->status->metric

        http("/metrics", timeout=5).actuator_metrics()

.. py:method:: prometheus()

    Parse the resulting text result according to the Prometheus specs using their prometheus_client.

        http("/metrics", timeout=5).prometheus()

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

LDAP
----

Retrieve OpenLDAP statistics (needs "cn=Monitor" database installed in LDAP server). ::

    ldap().statistics()

This would return a dict like:

.. code-block:: json

    {
        "connections_current": 77,
        "connections_per_sec": 27.86,
        "entries": 359369,
        "max_file_descriptors": 65536,
        "operations_add_per_sec": 0.0,
        "operations_bind_per_sec": 27.99,
        "operations_delete_per_sec": 0.0,
        "operations_extended_per_sec": 0.23,
        "operations_modify_per_sec": 0.09,
        "operations_search_per_sec": 24.09,
        "operations_unbind_per_sec": 27.82,
        "waiters_read": 76,
        "waiters_write": 0
    }

All information is based on the cn=Monitor OpenLDAP tree. You can get more information in the `OpenLDAP Administrator's Guide`_.
The meaning of the different fields is as follows:

``connections_current``
    Number of currently established TCP connections.

``connections_per_sec``
    Increase of connections per second.

``entries``
    Number of LDAP records.

``operations_*_per_sec``
    Number of operations per second per operation type (add, bind, search, ..).

``waiters_read``
    Number of waiters for read (whatever that means, OpenLDAP documentation does not say anything).

.. _OpenLDAP Administrator's Guide: http://www.openldap.org/doc/admin24/monitoringslapd.html#Monitor%20Information

Nagios
------

This function provides a wrapper for Nagios plugins.

.. py:method:: check_load()

    ::

        nagios().nrpe('check_load')

    Example check result as JSON:

    .. code-block:: json

        {
            "load1": 2.86,
            "load15": 3.13,
            "load5": 3.23
        }

.. py:method:: check_list_timeout()

    ::

        nagios().nrpe('check_list_timeout',  path="/data/production/", timeout=10)

    This command will run "timeout 10 ls /data/production/" on the
    target host via nrpe.

    Example check result as JSON:

    .. code-block:: json

        {

            "exit":0,
            "timeout":0
        }

    Exit is the exitcode from nrpe 0 for OK, 2 for ERROR.
    Timeout should not be used, yet.

.. py:method:: check_diff_reverse()

    ::

        nagios().nrpe('check_diff_reverse')

    Example check result as JSON:

    .. code-block:: json

        {
            "CommitLimit-Committed_AS": 16022524
        }

.. py:method:: check_mailq_postfix()

    ::

        nagios().nrpe('check_mailq_postfix')

    Example check result as JSON:

    .. code-block:: json

        {
            "unsent": 0
        }

.. py:method:: check_memcachestatus()

    ::

        nagios().nrpe('check_memcachestatus', port=11211)

    Example check result as JSON:

    .. code-block:: json

        {
            "curr_connections": 0.0,
            "cmd_get": 3569.09,
            "bytes_written": 66552.9,
            "get_hits": 1593.9,
            "cmd_set": 0.04,
            "curr_items": 0.0,
            "get_misses": 1975.19,
            "bytes_read": 83077.28
        }

.. py:method:: check_findfiles()

    Find-file analyzer plugin for Nagios. This plugin checks for newer files within a directory and checks their access time, modification time and count.

    ::

        nagios().nrpe('check_findfiles', directory='/data/example/error/', epoch=1)

    Example check result as JSON:

    .. code-block:: json

        {
            "ftotal": 0,
            "faccess": 0,
            "fmodify": 0
        }

.. py:method:: check_findolderfiles()

    Find-file analyzer plugin for Nagios. This plugin checks for files within a directory older than 2 given times in minutes.

    ::

        nagios().nrpe('check_findolderfiles', directory='/data/stuff,/mnt/other', time01=480, time02=600)

    Example check result as JSON:

    .. code-block:: json

        {
            "total files": 831,
            "files older than time01": 112,
            "files older than time02": 0
        }

.. py:method:: check_findfiles_names()

    Find-file analyzer plugin for Nagios. This plugin checks for newer files within a directory, optionally matching a filename pattern, and checks their access time, modification time and count.

    ::

        nagios().nrpe('check_findfiles_names', directory='/mnt/storage/error/', epoch=1, name='app*')

    Example check result as JSON:

    .. code-block:: json

        {
            "ftotal": 0,
            "faccess": 0,
            "fmodify": 0
        }

.. py:method:: check_findfiles_names_exclude()

    Find-file analyzer plugin for Nagios. This plugin checks for newer files within a directory, optionally matching a filename pattern(in this command the files are excluded), and checks their access time, modification time and count.

    ::

        nagios().nrpe('check_findfiles_names_exclude', directory='/mnt/storage/error/', epoch=1, name='app*')

    Example check result as JSON:

    .. code-block:: json

        {
            "ftotal": 0,
            "faccess": 0,
            "fmodify": 0
        }

.. py:method:: check_logwatch()

    ::

        nagios().nrpe('check_logwatch', logfile='/var/logs/example/p{}/catalina.out'.format(entity['instance']), pattern='Full.GC')

    Example check result as JSON:

    .. code-block:: json

        {
            "last": 0,
            "total": 0
        }

.. py:method:: check_ntp_time()

    ::

        nagios().nrpe('check_ntp_time')

    Example check result as JSON:

    .. code-block:: json

        {
            "offset": 0.003063
        }

.. py:method:: check_iostat()

    ::

        nagios().nrpe('check_iostat', disk='sda')

    Example check result as JSON:

    .. code-block:: json

        {
            "tps": 944.7,
            "iowrite": 6858.4,
            "ioread": 6268.4
        }

.. py:method:: check_hpacucli()

    ::

        nagios().nrpe('check_hpacucli')

    Example check result as JSON:

    .. code-block:: json

        {
            "logicaldrive_1": "OK",
            "logicaldrive_2": "OK",
            "logicaldrive_3": "OK",
            "physicaldrive_2I:1:6": "OK",
            "physicaldrive_2I:1:5": "OK",
            "physicaldrive_1I:1:3": "OK",
            "physicaldrive_1I:1:2": "OK",
            "physicaldrive_1I:1:1": "OK",
            "physicaldrive_1I:1:4": "OK"
        }

.. py:method:: check_hpasm_fix_power_supply()

    ::

        nagios().nrpe('check_hpasm_fix_power_supply')

    Example check result as JSON:

    .. code-block:: json

        {
            "status": "OK",
            "message": "System: 'proliant dl360 g6', S/N: 'CZJ947016M', ROM: 'P64 05/05/2011', hardware working fine, da: 3 logical drives, 6 physical drives cpu_0=ok cpu_1=ok ps_2=ok fan_1=46% fan_2=46% fan_3=46% fan_4=46% temp_1=21 temp_2=40 temp_3=40 temp_4=36 temp_5=35 temp_6=37 temp_7=32 temp_8=36 temp_9=32 temp_10=36 temp_11=32 temp_12=33 temp_13=48 temp_14=29 temp_15=32 temp_16=30 temp_17=29 temp_18=39 temp_19=37 temp_20=38 temp_21=45 temp_22=42 temp_23=39 temp_24=48 temp_25=35 temp_26=46 temp_27=35 temp_28=71 | fan_1=46%;0;0 fan_2=46%;0;0 fan_3=46%;0;0 fan_4=46%;0;0 'temp_1_ambient'=21;42;42 'temp_2_cpu#1'=40;82;82 'temp_3_cpu#2'=40;82;82 'temp_4_memory_bd'=36;87;87 'temp_5_memory_bd'=35;78;78 'temp_6_memory_bd'=37;87;87 'temp_7_memory_bd'=32;78;78 'temp_8_memory_bd'=36;87;87 'temp_9_memory_bd'=32;78;78 'temp_10_memory_bd'=36;87;87 'temp_11_memory_bd'=32;78;78 'temp_12_power_supply_bay'=33;59;59 'temp_13_power_supply_bay'=48;73;73 'temp_14_memory_bd'=29;60;60 'temp_15_processor_zone'=32;60;60 'temp_16_processor_zone'=3"
        }

.. py:method:: check_hpasm_gen8()

    ::

        nagios().nrpe('check_hpasm_gen8')

    Example check result as JSON:

    .. code-block:: json

        {
            "status": "OK",
            "message": "ignoring 16 dimms with status 'n/a' , System: 'proliant dl360p gen8', S/N: 'CZJ2340R6C', ROM: 'P71 08/20/2012', hardware working fine, da: 1 logical drives, 4 physical drives"
        }

.. py:method:: check_openmanage()

    ::

        nagios().nrpe('check_openmanage')

    Example check result as JSON:

    .. code-block:: json

        {
            "status": "OK",
            "message": "System: 'PowerEdge R720', SN: 'GN2J8X1', 256 GB ram (16 dimms), 5 logical drives, 10 physical drives|T0_System_Board_Inlet=21C;42;47 T1_System_Board_Exhaust=36C;70;75 T2_CPU1=59C;95;100 T3_CPU2=52C;95;100 W2_System_Board_Pwr_Consumption=168W;896;980 A0_PS1_Current_1=0.8A;0;0 A1_PS2_Current_2=0.2A;0;0 V25_PS1_Voltage_1=230V;0;0 V26_PS2_Voltage_2=232V;0;0 F0_System_Board_Fan1=1680rpm;0;0 F1_System_Board_Fan2=1800rpm;0;0 F2_System_Board_Fan3=1680rpm;0;0 F3_System_Board_Fan4=2280rpm;0;0 F4_System_Board_Fan5=2400rpm;0;0 F5_System_Board_Fan6=2400rpm;0;0"
        }

.. py:method:: check_ping()

    ::

        nagios().local('check_ping')

    Example check result as JSON:

    .. code-block:: json

        {
            "rta": 1.899,
            "pl": 0.0
        }

.. py:method:: check_apachestatus_uri()

    ::

        nagios().nrpe('check_apachestatus_uri', url='http://127.0.0.1/server-status?auto') or nagios().nrpe('check_apachestatus_uri', url='http://127.0.0.1:10083/server-status?auto')

    Example check result as JSON:

    .. code-block:: json

        {
            "idle": 60.0,
            "busy": 15.0,
            "hits": 24.256,
            "kBytes": 379.692
        }

.. py:method:: check_check_command_procs()

    ::

        nagios().nrpe('check_command_procs', process='httpd')

    Example check result as JSON:

    .. code-block:: json

        {
            "procs": 33
        }

.. py:method:: check_http_expect_port_header()

    ::

        nagios().nrpe('check_http_expect_port_header', ip='localhost', url= '/', redirect='warning', size='9000:90000', expect='200', port='88', hostname='www.example.com')

    Example check result as JSON:

    .. code-block:: json

        {
            "size": 33633.0,
            "time": 0.080755
        }

    **NOTE**: if the nrpe check returns an 'expect'result(return code is not the expected) , the check returns a NagiosError

.. py:method:: check_mysql_processes()

    ::

        nagios().nrpe('check_mysql_processes', host='localhost', port='/var/lib/mysql/mysql.sock', user='myuser', password='mypas')

    Example check result as JSON:

    .. code-block:: json

        {
            "avg": 0,
            "threads": 1
        }

.. py:method:: check_mysqlperformance()

    ::

        nagios().nrpe('check_mysqlperformance', host='localhost', port='/var/lib/mysql/mysql.sock', user='myuser', password='mypass')

    Example check result as JSON:

    .. code-block:: json

        {
            "Com_select": 15.27,
            "Table_locks_waited": 0.01,
            "Select_scan": 2.25,
            "Com_change_db": 0.0,
            "Com_insert": 382.26,
            "Com_replace": 8.09,
            "Com_update": 335.7,
            "Com_delete": 0.02,
            "Qcache_hits": 16.57,
            "Questions": 768.14,
            "Qcache_not_cached": 1.8,
            "Created_tmp_tables": 2.43,
            "Created_tmp_disk_tables": 2.25,
            "Aborted_clients": 0.3
        }

.. py:method:: check_mysql_slave()

    ::

        nagios().nrpe('check_mysql_slave', host='localhost', port='/var/lib/mysql/mysql.sock', database='mydb', user='myusr', password='mypwd')

    Example check result as JSON:

    .. code-block:: json

        {
            "Uptime": 6215760.0,
            "Open tables": 3953.0,
            "Slave IO": "Yes",
            "Queries per second avg": 967.106,
            "Slow queries": 1047406.0,
            "Seconds Behind Master": 0.0,
            "Threads": 1262.0,
            "Questions": 6011300666.0,
            "Slave SQL": "Yes",
            "Flush tables": 1.0,
            "Opens": 59466.0
        }


.. py:method:: check_ssl_cert()

    ::

        nagios().nrpe('check_ssl_cert', host_ip='91.240.34.73', domain_name='www.example.com') or nagios().local('check_ssl_cert', host_ip='91.240.34.73', domain_name='www.example.com')

    Example check result as JSON:

    .. code-block:: json

        {
            "days": 506
        }

NRPE checks for Windows Hosts
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

Checks are based on nsclient++ v.0.4.1. For more info look: http://docs.nsclient.org/

.. py:method:: CheckCounter()

    Returns performance counters for a process(usedMemory/WorkingSet)

    ::

        nagios().win('CheckCounter', process='eo_server')

    Example check result as JSON:

    used memory in bytes

    .. code-block:: json

        {
            "ProcUsedMem": 811024384
        }

.. py:method:: CheckCPU()

    ::

        nagios().win('CheckCPU')

    Example check result as JSON:

    .. code-block:: json

        {
            "1": 4,
            "10": 8,
            "5": 14
        }

.. py:method:: CheckDriveSize()

    ::

        nagios().win('CheckDriveSize')

    Example check result as JSON:

        Used Space in MByte

    .. code-block:: json

        {
            "C:\\ %": 61.0,
            "C:\\": 63328.469

        }

.. py:method:: CheckEventLog()

    ::

        nagios().win('CheckEventLog', log='application', query='generated gt -7d AND type=\'error\'')

    'generated gt -7d' means in the last 7 days

    Example check result as JSON:

    .. code-block:: json

        {
            "eventlog": 20
        }

.. py:method:: CheckFiles()

    ::

        nagios().win('CheckFiles', path='C:\\Import\\Exchange2Clearing', pattern='*.*', query='creation lt -1h')

    'creation lt -1h' means older than 1 hour

    Example check result as JSON:

    .. code-block:: json

        {
            "found files": 22
        }

.. py:method:: CheckLogFile()

    ::

        nagios().win('CheckLogFile', logfile='c:\Temp\log\maxflow_portal.log', seperator=' ', query='column4 = \'ERROR\' OR column4 = \'FATAL\'')

    Example check result as JSON:

    .. code-block:: json

        {
            "count": 4
        }

.. py:method:: CheckMEM()

    ::

        nagios().win('CheckMEM')

    Example check result as JSON:

    used memory in MBytes

    .. code-block:: json

        {
            "page file %": 16.0,
            "page file": 5534.105,
            "physical memory": 3331.109,
            "virtual memory": 268.777,
            "virtual memory %": 0.0,
            "physical memory %": 20.0
        }

.. py:method:: CheckProcState()

    ::

        nagios().win('CheckProcState', process='check_mk_agent.exe')

    Example check result as JSON:

    .. code-block:: json

        {
            "status": "OK",
            "message": "check_mk_agent.exe: running"
        }

.. py:method:: CheckServiceState()

    ::

        nagios().win('CheckServiceState', service='ENAIO_server')

    Example check result as JSON:

    .. code-block:: json

        {
            "status": "OK",
            "message": "ENAIO_server: started"
        }

.. py:method:: CheckUpTime()

    ::

        nagios().win('CheckUpTime')

    Example check result as JSON:

    uptime in ms

    .. code-block:: json

        {
            "uptime": 412488000
        }

CloudWatch
----------

If running on AWS you can use ``cloudwatch()`` to access AWS metrics easily.

.. py:method:: query_one(dimensions, metric_name, statistics, namespace, period=60, minutes=5)

  Query a single AWS CloudWatch metric and return a single scalar value (float).
  Metric will be aggregated over the last five minutes using the provided aggregation type.

  This method is a more low-level variant of the ``query`` method: all parameters, including all dimensions need to be known.


.. py:method:: query(dimensions, metric_name, statistics='Sum', namespace=None, period=60, minutes=5)

  Query AWS CloudWatch for metrics. Metrics will be aggregated over the last five minutes using the provided aggregation type (default "Sum").

  *dimensions* is a dictionary to filter the metrics to query. See the `list_metrics boto documentation`_.
  You can provide the special value "NOT_SET" for a dimension to only query metrics where the given key is not set.
  This makes sense e.g. for ELB metrics as they are available both per AZ ("AvailabilityZone" has a value) and aggregated over all AZs ("AvailabilityZone" not set).
  Additionally you can include the special "*" character in a dimension value to do fuzzy (shell globbing) matching.

  *metric_name* is the name of the metric to filter against (e.g. "RequestCount").

  *namespace* is an optional namespace filter (e.g. "AWS/EC2).

  To query an ELB for requests per second:

  ::

        # both using special "NOT_SET" and "*" in dimensions here:
        val = cloudwatch().query({'AvailabilityZone': 'NOT_SET', 'LoadBalancerName': 'pierone-*'}, 'RequestCount', 'Sum')['RequestCount']
        requests_per_second = val / 60

You can find existing metrics with the AWS CLI tools:

.. code-block:: bash

    $ aws cloudwatch list-metrics --namespace "AWS/EC2"

Use the "dimensions" argument to select on what dimension(s) to aggregate over:

.. code-block:: bash

    $ aws cloudwatch list-metrics --namespace "AWS/EC2" --dimensions Name=AutoScalingGroupName,Value=my-asg-FEYBCZF

The desired metric can now be queried in ZMON:

.. code-block:: python

    cloudwatch().query({'AutoScalingGroupName': 'my-asg-*'}, 'DiskReadBytes', 'Sum')



.. _list_metrics boto documentation: http://boto.readthedocs.org/en/latest/ref/cloudwatch.html#boto.ec2.cloudwatch.CloudWatchConnection.list_metrics


Scalyr
------

The ``scalyr()`` wrapper enables querying Scalyr from your AWS worker if the credentials have been specified for the worker instance(s).

.. py:method:: count(query, minutes=5)

  Run a count query against Scalyr, depending on number of queries you may run into rate limit.


  ::

        scalyr().count(' ERROR ')


.. py:method:: timeseries(query, minutes=30)

  Runs a timeseries query agains Scalyr with more generous rate limits. (New time series are created on the fly by Scalyr)

Redis
-----

Read-only access to Redis servers is provided by the :py:func:`redis` function.


.. py:function:: redis([port=6379], [db=0])

    Returns a connection to the Redis server at :samp:`{<host>}:{<port>}`, where :samp:`{<host>}` is the value
    of the current entity's ``host`` attribute, and :samp:`{<port>}` is the given port (default ``6379``). See
    below for a list of methods provided by the returned connection object.

Please also have a look at the `Redis documentation`_.


Methods of the Redis Connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The object returned by the :py:func:`redis` function provides the following methods:


.. py:method:: llen(key)

    Returns the length of the list stored at `key`. If `key` does not exist, it's value is treated as if it were
    an empty list, and 0 is returned. If `key` exists but is not a list, an error is raised.

    ::

        redis().llen("prod_eventlog_queue")


.. py:method:: lrange(key, start, stop)

    Returns the elements of the list stored at `key` in the range [`start`, `stop`]. If `key` does not
    exist, it's value is treated as if it were an empty list. If `key` exists but is not a list, an
    error is raised.

    The parameters `start` and `stop` are zero-based indexes. Negative numbers are converted to indexes
    by adding the length of the list, so that ``-1`` is the last element of the list, ``-2`` the
    second-to-last element of the list, and so on.

    Indexes outside the range of the list are not an error: If both `start` and `stop` are less than 0 or
    greater than or equal to the length of the list, an empty list is returned. Otherwise, if `start` is
    less than 0, it is treated as if it were 0, and if `stop` is greater than or equal to the the length
    of the list, it is treated as if it were equal to the length of the list minus 1. If `start` is
    greater than `stop`, an empty list is returned.

    Note that this method is subtly different from Python's list slicing syntax, where ``list[start:stop]``
    returns elements in the range [`start`, `stop`).

    ::

        redis().lrange("prod_eventlog_queue", 0, 9)   # Returns *ten* elements!
        redis().lrange("prod_eventlog_queue", 0, -1)  # Returns the entire list.


.. py:method:: get(key)

    Returns the string stored at `key`. If `key` does not exist, returns ``None``. If `key` exists
    but is not a string, an error is raised.

    ::

        redis().get("example_redis_key")


.. py:method:: hget(key, field)

    Returns the value of the field `field` of the hash `key`. If `key` does not exist or does not have
    a field named `field`, returns ``None``. If `key` exists but is not a hash, an error is raised.

    ::

        redis().hget("example_hash_key", "example_field_name")


.. py:method:: hgetall(key)

    Returns a ``dict`` of all fields of the hash `key`. If `key` does not exist, returns an empty ``dict``.
    If `key` exists but is not a hash, an error is raised.

    ::

        redis().hgetall("example_hash_key")


.. py:method:: scan(cursor, [match=None], [count=None])

    Returns a ``set`` with the next cursor and the results from this scan.
    Please see the Redis documentation on how to use this function exactly: http://redis.io/commands/scan

    ::

        redis().scan(0, 'prefix*', 10)

.. py:method:: statistics()

    Returns a ``dict`` with general Redis statistics such as memory usage and operations/s.
    All values are extracted using the `Redis INFO command`_.

    Example result:

    .. code-block:: json

        {
            "blocked_clients": 2,
            "commands_processed_per_sec": 15946.48,
            "connected_clients": 162,
            "connected_slaves": 0,
            "connections_received_per_sec": 0.5,
            "dbsize": 27351,
            "evicted_keys_per_sec": 0.0,
            "expired_keys_per_sec": 0.0,
            "instantaneous_ops_per_sec": 29626,
            "keyspace_hits_per_sec": 1195.43,
            "keyspace_misses_per_sec": 1237.99,
            "used_memory": 50781216,
            "used_memory_rss": 63475712
        }

    Please note that the values for both `used_memory` and `used_memory_rss` are in Bytes.

.. _Redis documentation: http://redis.io/
.. _Redis INFO command: http://redis.io/commands/info



Cassandra
---------

Provides access to a Cassandra cluster

.. py:function:: cassandra(host, port=9042)

Methods of Cassandra
^^^^^^^^^^^^^^^^^^^^

.. py:function:: execute(keyspace, stmt)


MongoDB
-------

Provides access to a MongoDB cluster

.. py:function:: mongodb(host, port=27017)

Methods of MongoDB
^^^^^^^^^^^^^^^^^^

.. py:function:: find(database, collection, query)

KairosDB
--------

Provides read access to the target KairosDB


.. py:function:: kairosdb(url)


Methods of KairosDB
^^^^^^^^^^^^^^^^^^^

.. py:function:: query(name, group_by = [], tags = None, start = -5, end = 0, time_unit='seconds', aggregators = None)

Elasticsearch
-------------

Provides search queries and health check against an Elasticsearch cluster.


.. py:function:: elasticsearch(url, timeout=10, oauth2=False)


Methods of Elasticsearch
^^^^^^^^^^^^^^^^^^^^^^^^

.. py:function:: search(indices=None, q='', body=None, source=True, size=DEFAULT_SIZE)

    ::

    Search ES cluster using URI or Request body search. If ``body`` is None then GET request will be used.

    :param indices: List of indices to search. Limited to only 10 indices. ['_all'] will search all available
                    indices, which effectively leads to same results as `None`. Indices can accept wildcard form.
    :type indices: list

    :param q: Search query string. Will be ignored if ``body`` is not None.
    :type  q: str

    :param body: Dict holding an ES query DSL.
    :type body: dict

    :param source: Whether to include `_source` field in query response.
    :type source: bool

    :param size: Number of hits to return. Maximum value is 1000. Set to 0 if interested in hits count only.
    :type size: int

    :return: ES query result.
    :rtype: dict

    Example query:

    .. code-block:: python

        elasticsearch('http://es-cluster').search(indices=['logstash-*'], q='client:192.168.20.* AND http_status:500', size=0, source=False)

        {
            "_shards": {
                "failed": 0,
                "successful": 5,
                "total": 5
            },
            "hits": {
                "hits": [],
                "max_score": 0.0,
                "total": 1
            },
            "timed_out": false,
            "took": 2
        }

.. py:method:: health()

    ::

    Return ES cluster health.

    :return: Cluster health result.
    :rtype: dict

    .. code-block:: python

        elasticsearch('http://es-cluster').health()

        {
            "active_primary_shards": 11,
            "active_shards": 11,
            "active_shards_percent_as_number": 50.0,
            "cluster_name": "big-logs-cluster",
            "delayed_unassigned_shards": 0,
            "initializing_shards": 0,
            "number_of_data_nodes": 1,
            "number_of_in_flight_fetch": 0,
            "number_of_nodes": 1,
            "number_of_pending_tasks": 0,
            "relocating_shards": 0,
            "status": "yellow",
            "task_max_waiting_in_queue_millis": 0,
            "timed_out": false,
            "unassigned_shards": 11
        }

SNMP
----

Provides a wrapper for SNMP functions listed below. SNMP checks require
specifying hosts in the entities filter. The partial object `snmp()` accepts a
`timeout=seconds` parameter, default is 5 seconds timeout. **NOTE**: this timeout
is per answer, so multiple answers will add up and may block the whole check

.. py:method:: memory()

    ::

        snmp().memory()

    Returns host's memory usage statistics. All values are in KiB (1024 Bytes).

    Example check result as JSON:

    .. code-block:: json

        {
            "ram_buffer": 359404,
            "ram_cache": 6478944,
            "ram_free": 20963524,
            "ram_shared": 0,
            "ram_total": 37066332,
            "ram_total_free": 22963392,
            "swap_free": 1999868,
            "swap_min": 16000,
            "swap_total": 1999868,
        }

.. py:method:: load()

    ::

        snmp().load()

    Returns host's CPU load average (1 minute, 5 minute and 15 minute averages).

    Example check result as JSON:

    .. code-block:: json

        {"load1": 0.95, "load5": 0.69, "load15": 0.72}

.. py:method:: cpu()

    ::

        snmp().cpu()

    Returns host's CPU usage in percent.

    Example check result as JSON:

    .. code-block:: json

        {"cpu_system": 0, "cpu_user": 17, "cpu_idle": 81}


.. py:method:: df()

    ::

        snmp().df()

    Example check result as JSON:

    .. code-block:: json

        {
            "/data/postgres-wal-nfs-example": {
                "available_space": 524287840,
                "device": "example0-2-stp-123:/vol/example_pgwal",
                "percentage_inodes_used": 0,
                "percentage_space_used": 0,
                "total_size": 524288000,
                "used_space": 160,
            }
        }

.. py:method:: logmatch()

    ::

        snmp().logmatch()

.. py:method:: interfaces()

    ::

        snmp().interfaces()

    Example check result as JSON:

    .. code-block:: json

        {
            "lo": {
                "in_octets": 63481918397415,
                "in_discards": 11,
                "adStatus": 1,
                "out_octets": 63481918397415,
                "opStatus": 1,
                "out_discards": 0,
                "speed": "10",
                "in_error": 0,
                "out_error": 0
            },
            "eth1": {
                "in_octets": 55238870608924,
                "in_discards": 8344,
                "adStatus": 1,
                "out_octets": 6801703429894,
                "opStatus": 1,
                "out_discards": 0,
                "speed": "10000",
                "in_error": 0,
                "out_error": 0
            },
            "eth0": {
                "in_octets": 3538944286327,
                "in_discards": 1130,
                "adStatus": 1,
                "out_octets": 16706789573119,
                "opStatus": 1,
                "out_discards": 0,
                "speed": "10000",
                "in_error": 0,
                "out_error": 0
            }
        }

.. py:method:: get()

    ::

        snmp().get('iso.3.6.1.4.1.42253.1.2.3.1.4.7.47.98.105.110.47.115.104', 'stunnel', int)

    Example check result as JSON:

    .. code-block:: json

        {
            "stunnel": 0
        }

SOAP
----

We have no documentation for the SOAP wrapper yet.


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


Ping
----

Simple ICMP ping function which returns ``True`` if the ping command returned without error and ``False`` otherwise.

.. py:function:: ping(timeout=1)

    ::

        ping()

    The ``timeout`` argument specifies the timeout in seconds.
    Internally it just runs the following system command::

        ping -c 1 -w <TIMEOUT> <HOST>

TCP
---

This function opens a TCP connection to a host on a given port. If the
connection succeeds, it returns ‘OK’. The host can be provided directly for global checks or resolved from
entities filter. Assuming that we have an entity filter type=host, the
example below will try to connect to every host on port 22::

    tcp().open(22)

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

EventLog
--------

The ``eventlog()`` function allows you to conveniently count EventLog_ events by type and time.


.. py:method:: count(event_type_ids, time_from, [time_to=None], [group_by=None])

    Return event counts for given parameters.

    *event_type_ids* is either a single integer (use hex notation, e.g. ``0x96001``) or a list of integers.

    *time_from* is a string time specification (``'-5m'`` means 5 minutes ago, ``'-1h'`` means 1 hour ago).

    *time_to* is a string time specification and defaults to *now* if not given.

    *group_by* can specify an EventLog field name to group counts by

    ::

        eventlog().count(0x96001, time_from='-1m')                         # returns a single number
        eventlog().count([0x96001, 0x63005], time_from='-1m')              # returns dict {'96001': 123, '63005': 456}
        eventlog().count(0x96001, time_from='-1m', group_by='appDomainId') # returns dict {'1': 123, '5': 456, ..}

    The ``count()`` method internally requests the EventLog Viewer's "count" JSON endpoint.



Entity
------

You also have access to the variable ``entity`` which is a dict containing all entity attributes.
Example usage::

    nagios().nrpe('check_logwatch', logfile='/var/example/logs/p{}/catalina.out'.format(entity['instance']), pattern='Full.GC')


See :ref:`entities` for a list of entity attributes.


ZMON
----

The ``zmon()`` function provides access to Zmon's internal properties. Currently, there are two functions available:
``stale_active_alerts`` and ``check_entities_total``. The first one returns a list of active alerts that weren't
executed in given period of time. By default, the period is equal to twice the check's interval plus five minute offset.
The period can be modified by passing the *multiplier* and *offset* parameters. The last function returns an overall
number of entities checked by Zmon.

.. py:method zmon()

``stale_active_alerts`` example: ::

    zmon().stale_active_alerts()

This would return a list like:

.. code-block:: python

    [{
        'id': 1,
        'team': 'Platform/Software',
        'responsible_team': 'Platform/Software'
    }]


Available Functions
-------------------

The following general-purpose functions are available in check commands:

.. py:function:: abs(number)

    Returns the absolute value of the argument. Does not have overflow issues. ::

        >>> abs(-1)
        1
        >>> abs(1)
        1
        >>> abs(-2147483648)
        2147483648


.. py:function:: all(iterable)

    Returns ``True`` if none of the elements of `iterable` are falsy. ::

        >>> all([4, 2, 8, 0, 3])
        False

        >>> all([])
        True


.. py:function:: any(iterable)

    Returns ``True`` if at least one element of `iterable` is truthy. ::

        >>> any([None, [], '', {}, 0, 0.0, False])
        False

        >>> any([])
        False


.. py:function:: avg(results)

    Returns the arithmetic mean of the values in `results`. Returns ``None`` if there are no values. `results` must not be an iterator. ::

        >>> avg([1, 2, 3])
        2.0

        >>> avg([])
        None


.. py:function:: basestring

    Superclass of ``str`` and ``unicode`` useful for checking whether a value is a string of some sort. ::

        >>> isinstance('foo', basestring)
        True
        >>> isinstance(u'ˈ', basestring)
        True


.. py:function:: bin(n)

    Returns a string of the given integer in binary representation.

        >>> bin(1000)
        '0b1111101000'


.. py:function:: bool(x)

    Returns ``True`` if `x` is truthy, and ``False`` otherwise. Does not parse strings. Also usable to check whether a value is Boolean. ::

        >>> bool(None)
        False

        >>> bool('False')
        True

        >>> isinstance(False, bool)
        True


.. py:function:: chain(*iterables)

    Returns an iterator that that yields the elements of the first iterable, followed by the elements of the second iterable, and so on. ::

        >>> list(chain([1, 2, 3], 'abc'))
        [1, 2, 3, 'a', 'b', 'c']

        >>> list(chain())
        []


.. py:function:: chr(n)

    Returns the character for the given ASCII code.

        >>> chr(65)
        'A'


.. py:class:: Counter([iterable-or-mapping])

    Creates a specialized ``dict`` for counting things. See `the official Python documentation <http://docs.python.org/2/library/collections.html#collections.Counter>`__ for details.


.. py:function:: dict([mapping], [**kwargs])

    Creates a new ``dict``. Usually, using a literal will be simpler, but the function may be useful to copy ``dict``\ s, to covert a list of key/value pairs to a ``dict``, or to check whether some object is a ``dict``.  ::

        >>> dict(a=1, b=2, c=3)
        {'a': 1, 'c': 3, 'b': 2}

        >>> dict({'a': 1, 'b': 2, 'c': 3})
        {'a': 1, 'c': 3, 'b': 2}   # This is a copy of the original dict.

        >>> dict([['a', 1], ['b', 2], ['c', 3]])
        {'a': 1, 'c': 3, 'b': 2}

        >>> isinstance({}, dict)
        True


.. py:function:: divmod(x, y):

    Performs integer division and modulo as a single operation. ::

        >>> divmod(23, 5)
        (4, 3)


.. py:function:: empty(v)

    Indicates whether `v` is falsy. Equivalent to ``not v``. ::

        >>> empty([])
        True

        >>> empty([0])
        False


.. py:function:: enumerate(iterable, [start=0])

    Generates tuples ``(start + 0, iterable[0]), (start + 1, iterable[1]), ...``. Useful to have access to the index in a loop. ::

        >>> list(enumerate(['a', 'b', 'c'], start=1))
        [(1, 'a'), (2, 'b'), (3, 'c')]


.. py:function:: filter(function, iterable)

    Returns a list of all objects in `iterable` for which `function` returns a truthy value. If function is ``None``, the returned list contains all truthy objects in `iterable`. ::

        >>> filter(lambda n: n % 3, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10])
        [1, 2, 4, 5, 7, 8, 10]

        >>> filter(None, [False, None, 0, 0.0, '', [], {}, 1000])
        [1000]


.. py:function:: float(x)

    Returns `x` as a floating-point number. Parses stings. ::

        >>> float('2.5')
        2.5

        >>> float('-inf')
        -inf

        >>> float(2)
        2.0

    This is useful to force proper division::

        >>> 2 / 5
        0

        >>> float(2) / 5
        0.4

    Also usable to check whether a value is a floating-point number::

        >>> isinstance(2.5, float)
        True

        >>> isinstance(2, float)
        False


.. py:function:: groupby(iterable, [key])

    A somewhat obscure function for grouping consecutive equal elements in an iterable. See `the official Python documentation <http://docs.python.org/2/library/itertools.html#itertools.groupby>`__ for more details. ::

        >>> [(k, list(v)) for k, v in groupby('abba')]
        [('a', ['a']), ('b', ['b', 'b']), ('a', ['a'])]


.. py:function:: hex(n)

    Returns a string of the given integer in hexadecimal representation.

        >>> hex(1000)
        '0x3e8'


.. py:function:: int(x, [base])

    Returns `x` as an integer. Truncates floating-point numbers and parses strings. Also usable to check whether a value is an integer. ::

        >>> int(2.5)
        2

        >>> int(-2.5)
        2

        >>> int('2')
        2

        >>> int('abba', 16)
        43962

        >>> isinstance(2, int)
        True


.. py:function:: isinstance(object, classinfo)

    Indicates whether `object` is an instance of the given class or classes. ::

        >>> isinstance(2, int)
        True

        >>> isinstance(2, (int, float))
        True

        >>> isinstance('2', int)
        False


.. py:function:: json(s)

    Converts the given :term:`JSON` string to a Python object.

        >>> json('{"list": [1, 2, 3, 4]}')
        {u'list': [1, 2, 3, 4]}


.. py:function:: jsonpath_flat_filter(obj, path)

    Executes json path expression using `jsonpath_rw`_ and returns a flat dict of (full_path, value). ::

        >>> data = {"timers":{"/api/v1/":{"m1.rate": 12, "99th": "3ms"}}}
        >>> jsonpath_flat_filter(data, "timers.*.*.'m1.rate'")
        {"timers./api/v1/.m1.rate": 12}

.. py:function:: jsonpath_parse(path)

    Creates a json path parse object from the `jsonpath_rw`_ to be used in your check command.

.. _jsonpath_rw: https://github.com/kennknowles/python-jsonpath-rw

.. py:function:: len(s)

    Returns the length of the given collection. ::

        >>> len('foo')
        3

        >>> len([0, 1, 2])
        3

        >>> len({'a': 1, 'b': 2, 'c': 3})
        3


.. py:function:: list(iterable)

    Creates a new list. Usually, using a literal will be simpler, but the function may be useful to copy lists, to covert some other iterable to a list, or to check whether some object is a list. ::

        >>> list({'a': 1, 'b': 2, 'c': 3})
        ['a', 'c', 'b']

        >>> list(chain([1, 2, 3], 'abc'))
        [1, 2, 3, 'a', 'b', 'c']   # Without the list call, this would be a chain object.

        >>> isinstance([1, 2, 3], list)
        True


.. py:function:: long(x, [base])

    Converts a number or string to a long integer. ::

        >>> long(2.5)
        2L

        >>> long(-2.5)
        -2L

        >>> long('2')
        2L

        >>> long('abba', 16)
        43962L


.. py:function:: map(function, iterable)

    Calls `function` on each element of `iterable` and returns the results as a list. ::

        >>> map(lambda n: n**2, [0, 1, 2, 3, 4, 5])
        [0, 1, 4, 9, 16, 25]


.. py:function:: max(iterable)

    Returns the greatest element of `iterable`. With two or more arguments, returns the greatest argument instead. ::

        >>> max([2, 4, 1, 3])
        4

        >>> max(2, 4, 1, 3)
        4


.. py:function:: min(iterable)

    Returns the smallest element of `iterable`. With two or more arguments, returns the smallest argument instead. ::

        >>> min([2, 4, 1, 3])
        1

        >>> min(2, 4, 1, 3)
        1


.. py:function:: normalvariate(mu, sigma)

    Returns a normally distributed random variable with the given mean and standard derivation. ::

        >>> normalvariate(0, 1)
        -0.1711153439880709


.. py:function:: oct(n)

    Returns a string of the given integer in octal representation.

        >>> oct(1000)
        '01750'


.. py:function:: ord(n)

    Returns the ASCII code of the given character.

        >>> ord('A')
        65


.. py:function:: pow(x, y, [z])

    Computes `x` to the power of `y`. The result is modulo `z`, if `z` is given, and the function is much, much faster than ``(x ** y) % z`` in that case.

        >>> pow(56876845793546543243783543735425734536873, 12425445412439354354394354397364398364378, 10)
        9L


.. py:function:: range([start], stop, [step])

    Returns a list of integers ``[start, start + step * 1, start + step * 2, ...]`` where all integers are less than `stop`, or greater than `stop` if `step` is negative.

        >>> range(10)
        [0, 1, 2, 3, 4, 5, 6, 7, 8, 9]
        >>> range(1, 11)
        [1, 2, 3, 4, 5, 6, 7, 8, 9, 10]
        >>> range(1, 1)
        []
        >>> range(11, 1)
        []
        >>> range(0, 10, 3)
        [0, 3, 6, 9]
        >>> range(10, -1, -1)
        [10, 9, 8, 7, 6, 5, 4, 3, 2, 1, 0]


.. py:function:: reduce(function, iterable, [initializer])

    Calls ``function(r, e)`` for each element ``e`` in `iterable`, where ``r`` is the result of the last such call, or `initializer` for the first such call. If `iterable` has no elements, returns `initializer`.

    If `initializer` is ommitted, the first element of `iterable` is removed and used in place of `initializer`. In that case, an error is raised if `iterable` has no elements. ::

        >>> reduce(lambda a, b: a * b, [1, 2, 3, 4, 5, 6, 7, 8, 9, 10], 1)
        3628800  # 10!

    Note: Because of a Python bug, ``reduce`` used to be unreliable. This issue should now be fixed.


.. py:function:: reversed(iterable)

    Returns an iterator that iterates over the elements in `iterable` in reverse order.

        >>> list(reversed([1, 2, 3]))
        [3, 2, 1]


.. py:function:: round(n, [digits=0])

    Rounds the given number to the given number of digits, rounding half away from zero.

        >>> round(23.4)
        23.0
        >>> round(23.5)
        24.0
        >>> round(-23.4)
        -23.0
        >>> round(-23.5)
        -24.0
        >>> round(0.123456789, 3)
        0.123
        >>> round(987654321, -3)
        987654000.0


.. py:function:: set(iterable)

    Returns a set built from the elements of `iterable`. Useful to remove duplicates from some collection. ::

        >>> set([1, 2, 1, 4, 3, 2, 2, 3, 4, 1])
        set([1, 2, 3, 4])


.. py:function:: sorted(iterable, [reverse])

    Returns a sorted list containing the elements of `iterable`. ::

        >>> sorted([2, 4, 1, 3])
        [1, 2, 3, 4]

        >>> sorted([2, 4, 1, 3], reverse=True)
        [4, 3, 2, 1]


.. py:function:: str(object)

    Returns the string representation of `object`. Also usable to check whether a value is a string. If the result would contain Unicode characters, the :py:func:`unicode` function must be used instead. ::

        >>> str(2)
        '2'

        >>> str({'a': 1, 'b': 2, 'c': 3})
        "{'a': 1, 'c': 3, 'b': 2}"

        >>> isinstance('foo', str)
        True


.. py:function:: sum(iterable)

    Returns the sum of the elements of `iterable`, or ``0`` if `iterable` is empty. ::

        >>> sum([1, 2, 3, 4])
        10

        >>> sum([])
        0


.. py:function:: time([spec], [utc])

    Given a time specification such as ``'-10m'`` for "ten minutes ago" or ``'+3h'`` for "in three hours", returns an object representing that timestamp. Valid units are ``s`` for seconds, ``m`` for minutes, ``h`` for hours, and ``d`` for days.

    The time specification `spec` can also be a valid ISO timestamp in of the following formats:
    ``YYYY-MM-DD HH:MM:SS.mmmmm``,
    ``YYYY-MM-DD HH:MM:SS``,
    ``YYYY-MM-DD HH:MM`` or
    ``YYYY-MM-DD``.

    If `spec` is omitted, the current time is used. If `utc` is True the timestamp uses UTC, otherwise it uses local time.

    The returned object has two methods:

    .. py:method:: isoformat([sep])

        Returns the timestamp as a string of the form ``YYYY-MM-DD HH:MM:SS.mmmmmm``. The default behavior is to omit the ``T`` between date and time. This can be overridden by passing the optional `sep` parameter to the method. ::

            >>> time('+4d').isoformat()
            '2014-03-29 18:05:50.098919'

            >>> time('+4d').isoformat('T')
            '2014-03-29T18:05:50.098919'

    .. py:method:: format(fmt)

        Returns the timestamp as a string formatted according to the given format. See `the official Python documentation <http://docs.python.org/2/library/datetime.html#strftime-strptime-behavior>`__ for an incomplete list of supported format directives.

    Additionally, the subtraction operator is overloaded and returns the time difference in seconds::

        >>> time('2014-01-01 01:13') - time('2014-01-01 01:01')
        12


.. py:function:: tuple(iterable)

    Returns the given iterable as a tuple (an immutable list, basically). Also usable to check whether a value is a tuple. ::

        >>> tuple([1, 2, 3])
        (1, 2, 3)
        >>> isinstance((1, 2, 3), tuple)
        True


.. py:function:: unicode(object)

    Returns the string representation of `object` as a Unicode string. Also usable to check whether a value is a Unicode string. ::

        >>> unicode({u'α': 1, u'β': 2, u'γ': 3})
        u"{u'\\u03b1': 1, u'\\u03b3': 3, u'\\u03b2': 2}"

        >>> isinstance(u'ˈ', unicode)
        True


.. py:function:: unichr(n)

    Returns the unicode character with the given code point. Might be limited to code points less than 0x10000.

        >>> unichr(0x2a13)  # LINE INTEGRATION WITH SEMICIRCULAR PATH AROUND POLE
        u'⨓'


.. py:function:: xrange([start], stop, [step])

    As :py:func:`range`, but returns an iterator rather than a list.


.. py:function:: zip(*iterables)

    Returns a list of tuples where the `i`\ -th tuple contains the `i`\ -th element from each of the given iterables. Uses the lowest length if the iterables have different lengths.

        >>> zip(['a', 'b', 'c'], [1, 2, 3])
        [('a', 1), ('b', 2), ('c', 3)]
        >>> zip(['A', 'B', 'C'], ['a', 'b', 'c'], [1, 2, 3])
        [('A', 'a', 1), ('B', 'b', 2), ('C', 'c', 3)]
        >>> zip([], [1, 2, 3])
        []
