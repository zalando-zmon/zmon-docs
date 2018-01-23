History
--------

Wrapper for KairosDB to access history data about checks.


.. py:function:: history(url=None, check_id='', entities=None, oauth2=False)


Methods of History
^^^^^^^^^^^^^^^^^^^

.. py:function:: result(time_from=ONE_WEEK_AND_5MIN, time_to=ONE_WEEK)

    Return query result.

    :param time_from: Relative time from in seconds. Default is ``ONE_WEEK_AND_5MIN``.
    :type application: int

    :param time_to: Relative time to in seconds. Default is ``ONE_WEEK``.
    :type application: int

    :return: Json result
    :rtype: dict

.. py:function:: get_one(time_from=ONE_WEEK_AND_5MIN, time_to=ONE_WEEK)

    Return first result values.

    :param time_from: Relative time from in seconds. Default is ``ONE_WEEK_AND_5MIN``.
    :type application: int

    :param time_to: Relative time to in seconds. Default is ``ONE_WEEK``.
    :type application: int

    :return: List of values
    :rtype: list

.. py:function:: get_aggregated(key, aggregator, time_from=ONE_WEEK_AND_5MIN, time_to=ONE_WEEK)

    Return first result values. If no ``key`` filtering matches, empty list is returned.

    :param key: Tag key used in filtering the results.
    :type key: str

    :param aggregator: Aggregator used in query. (e.g 'avg')
    :type aggregator: str

    :param time_from: Relative time from in seconds. Default is ``ONE_WEEK_AND_5MIN``.
    :type application: int

    :param time_to: Relative time to in seconds. Default is ``ONE_WEEK``.
    :type application: int

    :return: List of values
    :rtype: list

.. py:function:: get_avg(key, time_from=ONE_WEEK_AND_5MIN, time_to=ONE_WEEK)

    Return aggregated average.

    :param key: Tag key used in filtering the results.
    :type key: str

    :param time_from: Relative time from in seconds. Default is ``ONE_WEEK_AND_5MIN``.
    :type application: int

    :param time_to: Relative time to in seconds. Default is ``ONE_WEEK``.
    :type application: int

    :return: List of values
    :rtype: list

.. py:function:: get_std_dev(key, time_from=ONE_WEEK_AND_5MIN, time_to=ONE_WEEK)

    Return aggregated standard deviation.

    :param key: Tag key used in filtering the results.
    :type key: str

    :param time_from: Relative time from in seconds. Default is ``ONE_WEEK_AND_5MIN``.
    :type application: int

    :param time_to: Relative time to in seconds. Default is ``ONE_WEEK``.
    :type application: int

    :return: List of values
    :rtype: list

.. py:function:: distance(self, weeks=4, snap_to_bin=True, bin_size='1h', dict_extractor_path='')

    For detailed docs on distance function please see :ref:`History distance functionality <history-distance-label>` .
