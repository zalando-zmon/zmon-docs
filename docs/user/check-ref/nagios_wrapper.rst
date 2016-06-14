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
