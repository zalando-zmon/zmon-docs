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

Entity (Exclude) Filter
-----------------------

The :ref:`check definition <check-definitions>` already defines on what entities the checks should run.
Usually the check definition's ``entities`` are broader than you want.
A diskspace check might be defined for all hosts, but you want to trigger alerts only for hosts you are interested in.
The alert definition's ``entities`` field allows to filter entities by their attributes.

See :ref:`entities` for details on supported entities and their attributes.

Note: The entity name can be included in the alert message by using a special placeholder `{entities}`` on the alert name.

Notifications
-------------

ZMON notifications lets you know when you have a new alert without check the web UI.
This section will explain how to use the different options available to notify about changes in alert states.
We support E-Mail, HipChat, Slack and one SMS provider that we have been using.

The notifications field is a list of function calls (see below for examples), calling one of the following methods of notification:

.. py:function:: send_email(email*, [subject, message, repeat])
.. py:function:: send_sms(number*, [message, repeat])
.. py:function:: send_push([message, repeat, url, key])
.. py:function:: send_slack([channel, message, repeat, token])
.. py:function:: send_hipchat([room, message, color='red', repeat, token, notify=False])

If the alert has the top priority and should be handled immediately, you can specify the repeat interval for each notification.
In this case, you will be notified periodically, according to the specified interval, while the alert persists.
The interval is specified in seconds.

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

ZMON 2.0 allows specifying time periods in alert definitions.
When specified, user will be notified about the alert only when it occurs during given period.
Examples below cover most common use cases of time periods’ definitions.

To specify a time period from Monday through Friday, 9:00 to 17:00, use a
period such as

        wd {Mon-Fri} hr {9-16}

When specifying a range by using -, it is best to think of - as meaning through.
It is 9:00 through 16:00, which is just before 17:00 (16:59:59).

To specify a time period from Monday through Friday, 9:00 to 17:00 on Monday, Wednesday, and Friday, and 9:00 to 15:00 on Tuesday and Thursday, use a period such as

        wd {Mon Wed Fri} hr {9-16}, wd{Tue Thu} hr {9-14}

To specify a time period that extends Mon-Fri 9-16, but alternates weeks in a month, use a period such as

        wk {1 3 5} wd {Mon Wed Fri} hr {9-16}

A period that specifies winter in the northern hemisphere:

        mo {Nov-Feb}

This is equivalent to the previous example:

        mo {Jan-Feb Nov-Dec}

As is

        mo {jan feb nov dec}

And this is too:

        mo {Jan Feb}, mo {Nov Dec}

To specify a period that describes every other half-hour, use something like:

        minute { 0-29 }

To specify the morning, use

        hour { 0-11 }

Remember, 11 is not 11:00:00, but rather 11:00:00 - 11:59:59.

5 second blocks:

        sec {0-4 10-14 20-24 30-34 40-44 50-54}

To specify every first half-hour on alternating week days, and the
second half-hour the rest of the week, use the period

        wd {1 3 5 7} min {0-29}, wd {2 4 6} min {30-59}

For more examples and syntax reference, please refer to this `documentation <http://search.cpan.org/~pryan/Period-1.20/Period.pm#PERIOD_EXAMPLES>`_,
note that suffixes like `am` or `pm` for hours are **not** supported, only
integers between 0 and 23. In doubt, try calling with python with your period definition
like

.. code-block:: python

    from timeperiod import in_period
    in_period('hr { 0 - 23 }')

This should not throw an exception. The
timeperiod module in use is `timeperiod2 <https://pypi.python.org/pypi/timeperiod2>`_.
The `in_period` function accepts a second parameter which is a
`datetime <https://docs.python.org/2/library/datetime.html#datetime-objects>`_ like

.. code-block:: python

    from datetime import datetime
    from timeperiod import in_period
    in_period('hr { 7 - 23 }', datetime(2018, 1, 8, 2, 15)) # check 2018-01-08 02:15:00


.. include:: alert-definition-inheritance.rst
.. include:: alert-definition-parameters.rst
.. include:: downtimes.rst
.. include:: comments.rst
