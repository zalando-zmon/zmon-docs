Slack
-----

Notify Slack channel with alert status.

.. py:function:: send_slack(channel='#general', message=None, token=None)

    ::

    Send Slack notification to specified channel.

    :param channel: Channel to be notified. Default is ``#general``.
    :type channel: str

    :param message: Message to be sent. If ``None``, then a message constructed from the alert will be sent.
    :type message: str

    :param token: Slack API token.
    :type token: str
