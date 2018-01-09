Memcached
---------

Read-only access to memcached servers is provided by the :py:func:`memcached` function.


.. py:function:: memcached([host=some.host], [port=11211])

    Returns a connection to the Memcached server at :samp:`{<host>}:{<port>}`, where :samp:`{<host>}` is the value
    of the current entity's ``host`` attribute, and :samp:`{<port>}` is the given port (default ``11211``). See
    below for a list of methods provided by the returned connection object.


Methods of the Memcached Connection
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

The object returned by the :py:func:`memcached` function provides the following methods:

.. py:method:: get(key)

    Returns the string stored at `key`. If `key` does not exist an error is raised.

    ::

        memcached().get("example_memcached_key")


.. py:method:: json(key)

    Returns the data of the key as unserialized JSON data. I.e. you can store a JSON object as
    value of the key and get a dict back

    ::

        memcached().json("example_memcached_key")



.. py:method:: stats([extra_keys=[STR,STR])

    Returns a ``dict`` with general Memcached statistics such as memory usage and operations/s.
    All values are extracted using the `Memcached STATS command`_.

    The `extra_keys` may be retrieved as returned as well from the memcached server's `stats`
    command, e.g. `version` or `uptime`.

    Example result:

.. code-block:: json

    {
        "incr_hits_per_sec": 0,
        "incr_misses_per_sec": 0,
        "touch_misses_per_sec": 0,
        "decr_misses_per_sec": 0,
        "touch_hits_per_sec": 0,
        "get_expired_per_sec": 0,
        "get_hits_per_sec": 100.01,
        "cmd_get_per_sec": 119.98,
        "cas_hits_per_sec": 0,
        "cas_badval_per_sec": 0,
        "delete_misses_per_sec": 0,
        "bytes_read_per_sec": 6571.76,
        "auth_errors_per_sec": 0,
        "cmd_set_per_sec": 19.97,
        "bytes_written_per_sec": 6309.17,
        "get_flushed_per_sec": 0,
        "delete_hits_per_sec": 0,
        "cmd_flush_per_sec": 0,
        "curr_items": 37217768,
        "decr_hits_per_sec": 0,
        "connections_per_sec": 0.02,
        "cas_misses_per_sec": 0,
        "cmd_touch_per_sec": 0,
        "bytes": 3902170728,
        "evictions_per_sec": 0,
        "auth_cmds_per_sec": 0,
        "get_misses_per_sec": 19.97
    }


.. _Memcached documentation: https://lzone.de/cheat-sheet/memcached
.. _Memcached STATS command: https://lzone.de/cheat-sheet/memcached#stats
