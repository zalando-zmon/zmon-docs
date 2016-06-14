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
