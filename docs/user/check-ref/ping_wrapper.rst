Ping
----

Simple ICMP ping function which returns ``True`` if the ping command returned without error and ``False`` otherwise.

.. py:function:: ping(timeout=1)

    ::

        ping()

    The ``timeout`` argument specifies the timeout in seconds.
    Internally it just runs the following system command::

        ping -c 1 -w <TIMEOUT> <HOST>
