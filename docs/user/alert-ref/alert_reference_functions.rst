Alert Functions Reference
-------------------------

Time Specifications
^^^^^^^^^^^^^^^^^^^

Whenever one of these functions takes an argument named ``time_spec``, that argument is a string of the form :samp:`{<magnitude>}{<unit>}`, where :samp:`{<magnitude>}` is an positive integer, and :samp:`{<unit>}` is one of ``s`` (for seconds), ``m`` (for minutes), ``h`` (for hours), and ``d`` (for days).

Therefore, a value of ``5m`` would indicate that all values gathered in the last five minutes should be taken into account.

.. note::
    Trial Run doesn't provide any previous values. Please check how functions depending on check values behave in case values were not available.

The following functions are available in the alert condition expression:


.. py:function:: capture(value)
                 capture(name=value)

    Saves the given value as a capture, and returns it unaltered. In the first form, the capture receives a generated name (:samp:`capture_{N}`). In the second form, the specified name is used as the name of the capture.

    **Example:** ``capture(foo=1)`` saves the value ``1`` in a capture named ``foo`` and returns ``1``.


.. py:function:: timeseries_avg(time_spec)

    The arithmetic mean of the check values gathered in the specified time period. Returns ``None`` if there are no values. Only works for numeric values.

    **Example:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. ``timeseries_avg('5m')`` is (5 + 12 + 14 + 13 + 6) / 5 = 10.


.. py:function:: timeseries_median(time_spec)

    The median of the check values gathered in the specified time period. If the number of such values is even, the arithmetic mean of the two middle values is returned. Returns ``None`` if there are no values. Equivalent to    :samp:`timeseries_percentile({time_spec}, 0.5)`. Only works for numeric values.

    **Example 1:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. Sorting these values gives 5, 6, 12, 13, 14. The middle value is 12. Therefore, ``timeseries_median('5m')`` is 12.

    **Example 2:** The check has gathered the values 12, 14, 13, and 6 over the last four minutes. Sorting these values gives 6, 12, 13, 14. The two middle values are 12 and 13. Therefore, ``timeseries_median('4m')`` is (12 + 13) / 2 = 12.5.


.. py:function:: timeseries_percentile(time_spec, percent)

    The *P*\ -th percentile of the values gathered in the specified time period, where *P* = *percent* × 100, using linear interpolation. Only works for numeric values.

    The *P*\ -th percentile of *N* values is *V*\ (⌊\ *K*\ ⌋) + (\ *V*\ (⌈\ *K*\ ⌉) − *V*\ (⌊\ *K*\ ⌋)) × (\ *K* − ⌊\ *K*\ ⌋), where *K* = (*N* − 1) × *P* / 100 and *V*\ (\ *I*\ ) for *I* in [0, *N*\ ) is the *I*\ -th element of the list of values sorted in ascending order. Returns ``None`` if there are no values.

    **Example 1:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. Sorting these values gives 5, 6, 12, 13, 14. Let *P* = 30. There are *N* = 5 values, and *K* = (*N* − 1) × *P* / 100 = (5 − 1) × 30 / 100 = 1.2. The value at index ⌊1.2⌋ = 1 is 6, and the value at index ⌈1.2⌉ = 2 is 12. Therefore, ``timeseries_percentile('5m', 0.3)`` is 6 + (12 − 6) × (1.2 − ⌊1.2⌋) = 7.2.

    **Example 2:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. Sorting these values gives 5, 6, 12, 13, 14. Let *P* = 25. There are *N* = 5 values, and *K* = (*N* − 1) × *P* / 100 = (5 − 1) × 25 / 100 = 1. ⌊1⌋ = ⌈1⌉ = 1. The value at index 1 is 6. Therefore, ``timeseries_percentile('5m', 0.25)`` is 6 + (6 − 6) × (1 − ⌊1⌋) = 6.


.. py:function:: timeseries_first(time_spec)

    The oldest value among the values gathered in the specified time period. Returns ``None`` if there are no values. Works regardless of the type of value.

    **Example:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. The oldest value is 5. Therefore, ``timeseries_first('5m')`` is 5.


.. py:function:: timeseries_delta(time_spec)

    The newest value among the values gathered in the specified time period minus the oldest one. Returns ``0`` if there are no values. Only works for numeric values.

    **Example 1:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. The newest value is 6 and the oldest value is 5. Therefore, ``timeseries_delta('5m')`` is 6 − 5 = 1.

    **Example 2:** The check has gathered the values 12, 14, 13, and 6 over the last four minutes. The newest value is 6 and the oldest value is 12. Therefore, ``timeseries_delta('4m')`` is 6 − 12 = −6 (not 6).


.. py:function:: timeseries_min(time_spec)

    The smallest value among the values gathered in the specified time period. Returns ``None`` if there are no values. Works regardless of the type of value, but is unlikely to be particularly useful for non-numeric values.

    **Example:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. The smallest value is 5. Therefore, ``timeseries_min('5m')`` is 5.


.. py:function:: timeseries_max(time_spec)

    The largest value among the values gathered in the specified time period. Returns ``None`` if there are no values. Works regardless of the type of value, but is unlikely to be particularly useful for non-numeric values.

    **Example:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. The largest value is 14. Therefore, ``timeseries_max('5m')`` is 14.


.. py:function:: timeseries_sum(time_spec)

    The sum of the values gathered in the specified time period. Returns ``0`` if there are no values. Only works for numeric values.

    **Example:** The check has gathered the values 5, 12, 14, 13, and 6 over the last five minutes. Therefore, ``timeseries_sum('5m')`` is 5 + 12 + 14 + 13 + 6 = 50.


.. py:function:: value_series([n=1])

    Returns the last n values for the underlying checks and the current entity. Return ``[]`` if there are no values.

.. py:function:: alert_series(f, [n=1])

    Returns True if function f either raises exception or returns True for the last n check values for the given entity. Use this function to build an alert that only is raised if the last n intervals are up. This can solve alert where you face flapping due to technical issues.

    .. note::

        If number of check values is less than ``n``, then ``f`` will be evaluated for those values and alerts could be raised accordingly.

.. py:function:: entity_results()

     List for every entity containing a dict with the following keys: ``value`` (the most recent value for the alert's check on that entity), ``ts`` (the time when the check evaluation was started, in seconds since the epoch, as a floating-point number), and ``td`` (the check's duration, in seconds, as a floating-point number). Works regardless of the type of value. DOES NOT WORK in Trial Run right now!


.. py:function:: entity_values()

    Returns a list for each entity containing the most recent value for the alert's check on that entity. Works regardless of the type of value. DOES NOT WORK in Trial Run right now!

.. _history-distance-label:

History distance functionality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The history distance functionality currently only works for numeric values, and not for structured ones, or arrays.
Call for a DistanceWrapper object. ::

    history().distance([weeks=4], [bin_size='1h'], [snap_to_bin = True], [dict_extractor_path=''])

An object will be returned, where you can call additional functions on. The default parameters should be good for most cases,
but in case you'd like to change them:

``weeks``
    Changes how far you'd like to look into the past. It is good to average more than one week, since you might have seen
    something unusual a week ago, and I assume you would like to get warned in the next week if something similar happens.

``bin_size``
    Defines the size of the bins you are using to aggregate the history. Defaults to 1h. Is a ``time_spec``. See the
    next parameter for an explanation of the bins.

``snap_to_bin``
    Determines wether you'd like to have sliding bins, or fixed bin start points. Consider the following example:
    You run your check at monday, 10.30 AM. If ``snap_to_bin`` is ``True``, you would gather data from the past 4 weeks,
    every monday from 10 AM to 11 AM, and then calculate the mean and standard deviation to use in the functions below.
    If the value is ``snap_to_bin`` is ``False``, you would gather data from every monday, 9.30 AM to 10.30 AM.

    Setting the value to ``True`` allows for some internal caching of already-calculated values for a bin, since the
    mean and standard deviation don't change for about an hour, so you don't stress the network and servers as much
    as with having it set to ``False``. **Attention:** Caching optimizations for ``snap_to_bin`` not yet implemented.
    Please use it nevertheless, so that we can benefit from optimizations in the future.

``dict_extractor_path``
    Takes a string that is used for accessing the ``value`` if it is not a scalar value, but a dict. Normally, the history
    functionality only works for scalar values. Using this access string, you can use structured values, too.
    The `dict_extractor_path` is of the form 'a.b.c' for a dict with the structure {'a':{'b':{'c':5}}} to extract the value 5.
    Effectively, you use the `dict_extractor_path` to boil a structured check value down to a scalar value. The
    `dict_extractor_path` is
    applied on the historic values, and on the parameters of the ``sigma()`` and ``absolute()`` functions.

    **Example:** Your check gives you a map of data instead of a single value:
    ``{"CREDITCARD": 25, "PAYPAL": 10, "MAK": 10, "PTF": 30}``
    which contains the number of requests for the payment methods CREDITCARD, PAYPAL, MAKSUTURVA and PRZELEWY24
    of the last few minutes. If
    you want to check the history of Paypal orders, take this one: ::

        history().distance(dict_extractor_path = 'PAYPAL').sigma(value) < 2.0

    which will take a look at the history of Paypal orders only and warn you if there is something unusual
    (too low number of requests). An even better query would be: ::

        capture(suspect_payment_methods=
            {
                k: value[k]
                for k,v in
                    {
                        payment_method: history().distance(dict_extractor_path = payment_method).sigma(value)
                        for payment_method in value.keys()
                    }.items()
                if v < -2.0
            }
        )

    which takes a look at the history of every payment method and then tells you in a capture which payment methods are
    suspect and should be looked at manually.

    **Attention:** Some structured values are not written to the history (when they are too complex). If you have trouble,
    try to change your check to return less complex values. Lists are currently not supported.

.. py:function:: absolute(value)

    Returns the absolute distance of the actual value to the history of the check that is linked to this function.
    The absolute distance is just the difference of the value provided and the mean of the history values.

    **Example:** You can use it e.g. to warn when you get 5 more exceptions than you would get on average: ::

        history().distance().absolute(value) < 5

    The distance is directed, which means that you will not get warned if you get "too little" exceptions. You can use
    abs() to get an undirected value.

.. py:function:: sigma(value)

    Returns the distance of the actual value to the history of the check, normalized by the standard deviation.

    **Example**: You can use it e.g. to get warned when you get more exceptions than usual: ::

        history().distance().sigma(value) < 2.0

    This check warns you in 4% of all cases on average. You will not be warned if there are some small spikes in
    the exception count, but you will be warned if there are spikes that are twice as far away from the mean as what is
    usual.

    The distance is directed, which means that you will not get warned if you get "too little" exceptions. You can use
    abs() to get an undirected value.

.. py:function:: bin_mean()

    Returns the mean of the bins that were aggregated.

.. py:function:: bin_standard_deviation()

    Returns the standard deviation of the bins that were aggregated.


Additional helper functions
^^^^^^^^^^^^^^^^^^^^^^^^^^^

You can also use some additional functions that are used in check commands.

* :ref:`time() <time-helper-function>`
* :ref:`kairosdb() <check-kairosdb>`
