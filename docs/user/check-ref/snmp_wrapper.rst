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
