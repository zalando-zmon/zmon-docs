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
