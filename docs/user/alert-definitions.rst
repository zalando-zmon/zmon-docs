.. _alert-definitions:

*****************
Alert Definitions
*****************

Alert definitions specify when (condition, time period) and who (team) to notify for a desired monitoring event.
Alert definitions can be defined in the ZMON web frontend and via the :ref:`ZMON CLI <zmon-cli>`.

The following fields exist for alert definitions:

    name
        The alert's display name on the dashboard.
        This field can contain curly-brace variables like ``{mycapture}`` that are replaced by capture's value when the alert is triggered.
        It's also possible to format decimal precision (e.g. "My alert ``{mycapture:.2f}``" would show as "My alert ``123.45``" if mycapture is ``123.456789``).
        To include a comma separated list of entities as part of the alert's name, just use the special placeholder ``{entities}``.

    description
        Meaningful text for people trying to handle the alert, e.g. incident support.

    priority
        The alert's dashboard priority. This defines color and sort order on the dashboard.

    condition
        Valid Python expression to return true when alert should be triggered.

    parameters
        You may apply parameters your alert condition using variables. More details :ref:`here <alert-definition-parameters>`

    entities filter
        Additional filter to apply the alert definition only to a subset of entities.

    notifications
        List of notification commands, e.g. to send out emails.

    time_period
        Notification time period.

    team
        Team dashboard to show alert on.

    responsible_team
        Additional team field to allow delegating alert monitoring to other teams.
        The responsible team's name will be shown on the dashboard.

    status
        Alerts will only be triggered if status is "ACTIVE".

    template
        A template is an alert definition that is not evaluated and can only be used for extension. More details :ref:`here <alert-definition-inheritance>`

.. _alert-condition:

Condition
---------
Simple expressions can start directly with an operator. To trigger an alert if the check result value is larger than zero:

.. code-block:: python

    > 0

You can use the ``value`` variable to create more complex conditions:

.. code-block:: python

    value >= 10 and value <= 100

Some more examples of valid conditions:

.. code-block:: python

    == 'OK'
    != False
    value in ('banana', 'apple')

If the value already is a dictionary (hash map), we can apply all the Python magic to it:

.. code-block:: python

    ['mykey'] > 100                                       # check a specific dict value
    'error-message' in value                              # trigger alert if key is present
    not empty([ k for k, v in value.items() if v > 100 ]) # trigger alert if some dict value is > 100

.. _captures:

Captures
--------

You can capture intermediate results in alert conditions by using the
``capture`` function. This allows easier debugging of complex alert
conditions.

.. code-block:: python

    capture(value["a"]/value["b"]) > 0
    capture(myval=value["a"]/value["b"]) > 0
    any([capture(foo=FOO) > 10, capture(bar=BAR) > 10])

Please refer to Recipes section in :ref:`Python Tutorial <python-tutorial>` for some Python tricks you may use.

Named captures can be used to customize the alert display on the :term:`dashboard` by using template substitution in the alert name.

If you call your capture *dashboard*, it will be used on dashboard next to entity name instead of entity value.
For example, if you have a host-based alert that fails on z-host1 and z-host2, you would normally see something like that

ALERT TITLE (N)
z-host1 (value1), z-host2 (value2)

Once you introduce capture called *dashboard*, you will get something like

ALERT TITLE (N)
z-host1 (capturevalue1), z-host2 (capturevalue2)

where capturevalue1 is value of "dashboard" capture evaluated against z-host1.

Example alert condition (based on PF/System check for diskspace)

.. code-block:: python

    "ERROR" not in value
    and
    capture(dashboard=(lambda d: '{}:{}'.format(d.keys()[0], d[d.keys()[0]]['percentage_space_used']) if d else d)(dict((k, v) for k,v in value.iteritems() if v.get('percentage_space_used', 0) >= 90))))

Entities
--------

The :ref:`check definition <check-definitions>` already defines on what entities the checks should run.
Usually the check definition's ``entities`` are broader than you want.
A diskspace check might be defined for all hosts, but you want to trigger alerts only for hosts you are interested in.
The alert definition's ``entities`` field allows to filter entities by their attributes.

See :ref:`entities` for details on supported entities and their attributes.

Note: The entity name can be included in the alert message by using a special placeholder `{entities}`` on the alert name.

Notifications
-------------

ZMON notifications lets you know when you have a new alert without check the web UI. This section will explain how to use the different options available to notify about changes in alert states. We support E-Mail, HipChat, Slack and one SMS provider that we have been using.

The notifications field is a list of function calls (see below for examples), calling one of the following methods of notification:

.. py:function:: send_email(email*, [subject, message, repeat])
.. py:function:: send_sms(number*, [message, repeat])
.. py:function:: send_push([message, repeat, url, key])
.. py:function:: send_slack([channel, message, repeat, token])
.. py:function:: send_hipchat([room, message, color='red', repeat, token, notify=False])

If the alert has the top priority and should be handled immediately, you can specify the repeat interval for each
notification. In this case, you will be notified periodically, according to the specified interval, while the alert
persists. The interval is specified in seconds.

To receive push notifications you need one of the ZMON mobile apps (configured for your deployment) and subscribe to alert ids, before you can receive notifications.

In addition, you may use :ref:`notification-groups` to configure groups of people with associated **emails** and/or **phone numbers** and use these groups in notifications like this:

Example JSON email and SMS configuration using groups:

.. code-block:: yaml

   [
      "send_sms('active:2nd-database')",
      "send_email('group:2nd-database')"
   ]

In the above example you send SMS to **active** member of **2nd-database** group and send email to **all members** of the group.

Example JSON email configuration:

.. code-block:: yaml

   [
      "send_mail('a@example.org', 'b@example.org')",
      "send_mail('a@example.com', 'b@example.com', subject='Critical Alert please do something!')",
      "send_mail('c@example.com', repeat=60)"
   ]

Example JSON Slack configuration:

.. code-block:: yaml

   [
      "send_slack()",
      "send_slack(channel='#incidents')",
      "send_slack(channel='#incidents', token='your-token')"
   ]

Example JSON HipChat configuration:

.. code-block:: yaml

   [
      "send_hipchat()",
      "send_hipchat(room='#incidents', color='red')",
      "send_hipchat(room='#incidents', token='your-token')",
      "send_hipchat(room='#incidents', token='your-token', notify=True)"
   ]

Example JSON Push configuration:

   .. code-block:: yaml

      [
         "send_push()"
      ]

Example JSON SMS configuration:

.. code-block:: yaml

   [
      "send_sms('0049123555555', '0123111111')",
      "send_sms('0049123555555', '0123111111', message='Critical Alert please do something!')",
      "send_sms('0029123555556', repeat=300)"
   ]

Example email:

::

   From: ZMON <zmon@example.com>
   Date: 2014-05-28 18:37 GMT+01:00
   Subject: NEW ALERT: Low Orders/m: 84.9% of last weeks on GLOBAL
   To: Undisclosed Recipients <zmon@example.com>

   New alert on GLOBAL: Low Orders/m: {percentage_wow:.1f}% of last weeks


   Current value: {'2w_ago': 188.8, 'now': 180.8, '1w_ago': 186.6, '3w_ago': 196.4, '4w_ago': 208.8}


   Captures:

   percentage_wow: 184.9185496584

   last_weeks_avg: 195.15



   Alert Definition
   Name (ID):     Low Orders/m: {percentage_wow:.1f}% of last weeks (ID: 190)
   Priority:      1
   Check ID:      203
   Condition      capture(percentage_wow=100. * value['now']/capture(last_weeks_avg=(value['1w_ago'] + value['2w_ago'] + value['3w_ago'] + value['4w_ago'])/4. )) < 85
   Team:          Platform/Software
   Resp. Team:    Platform/Software
   Notifications: [u"send_mail('example@example.com')"]

   Entity

   id: GLOBAL

   type: GLOBAL

   percentage_wow: 184.9185496584

   last_weeks_avg: 195.15

Example SMS:

::

   Message details:
      Type: Text Message
      From: zmon2
   Message text:
      NEW ALERT: DB instances test alert on all shards on customer-integration-master


.. _time-periods:

Time periods
------------

ZMON 2.0 allows specifying time periods in alert definitions. When
specified, user will be notified about the alert only when it occurs
during given period. Examples below cover most common use cases of time
periods’ definitions.

To specify a time period from Monday through Friday, 9:00 to 17:00, use a
period such as

        wd {Mon-Fri} hr {9-16}

When specifying a range by using -, it is best to think of - as meaning
through. It is 9:00 through 16:00, which is just before 17:00 (16:59:59).

To specify a time period from Monday through Friday, 9:00 to 17:00 on
Monday, Wednesday, and Friday, and 9:00 to 15:00 on Tuesday and Thursday,
use a period such as

        wd {Mon Wed Fri} hr {9-16}, wd{Tue Thu} hr {9-14}

To specify a time period that extends Mon-Fri 9-16, but alternates
weeks in a month, use a period such as

        wk {1 3 5} wd {Mon Wed Fri} hr {9-16}

A period that specifies winter in the northern hemisphere:

        mo {Nov-Feb}

This is equivalent to the previous example:

        mo {Jan-Feb Nov-Dec}

As is

        mo {jan feb nov dec}

And this is too:

        mo {Jan Feb}, mo {Nov Dec}

To specify a period that describes every other half-hour, use something
like:

        minute { 0-29 }

To specify the morning, use

        hour { 0-11 }

Remember, 11 is not 11:00:00, but rather 11:00:00 - 11:59:59.

5 second blocks:

        sec {0-4 10-14 20-24 30-34 40-44 50-54}

To specify every first half-hour on alternating week days, and the
second half-hour the rest of the week, use the period

        wd {1 3 5 7} min {0-29}, wd {2 4 6} min {30-59}

For more examples and syntax reference, please refer to this `documentation <http://search.cpan.org/~pryan/Period-1.20/Period.pm#PERIOD_EXAMPLES>`_


Alert Deletion
--------------

To delete an alert definition, you must either have ADMIN, LEAD or USER roles and execute the following steps:

* Open the alert definition to delete.
* Click on the top right link named `Delete`.
* Confirm deletion.

Want to know more about alert deletion permissions? Please take a look into the :ref:`permissions` section.


Alert Functions Reference
-------------------------

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

    Returns the last n values for the underlying checks and the current entity.

.. py:function:: alert_series(f, [n=1])

    Returns True if function f either raises exception or returns True for the last n check values for the given entity. Use this function to build an alert that only is raised if the last n intervals are up. This can solve alert where you face flapping due to technical issues.

.. py:function:: entity_results()

     List for every entity containing a dict with the following keys: ``value`` (the most recent value for the alert's check on that entity), ``ts`` (the time when the check evaluation was started, in seconds since the epoch, as a floating-point number), and ``td`` (the check's duration, in seconds, as a floating-point number). Works regardless of the type of value. DOES NOT WORK in Trial Run right now!


.. py:function:: entity_values()

    Returns a list for each entity containing the most recent value for the alert's check on that entity. Works regardless of the type of value. DOES NOT WORK in Trial Run right now!

History distance functionality
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
The history distance functionality currently only works for numeric values, and not for structured ones, or arrays.
Call for a DistanceWrapper object. ::

    history().distance([weeks=4], [bin_size='1h'], [snap_to_bin = True], [dict_extractor_path=lambda x: x])

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


Time Specifications
^^^^^^^^^^^^^^^^^^^

Whenever one of these functions takes an argument named ``time_spec``, that argument is a string of the form :samp:`{<magnitude>}{<unit>}`, where :samp:`{<magnitude>}` is an positive integer, and :samp:`{<unit>}` is one of ``s`` (for seconds), ``m`` (for minutes), ``h`` (for hours), and ``d`` (for days).

Therefore, a value of ``5m`` would indicate that all values gathered in the last five minutes should be taken into account.
