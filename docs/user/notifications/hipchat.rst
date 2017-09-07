Hipchat
-------

Notify Hipchat room with alert status.

.. py:function:: send_hipchat(room=None, message=None, token=None, notify=False, color='red', link=False)

    ::

    Send Hipchat notification to specified room.

    :param room: Room to be notified.
    :type room: str

    :param message: Message to be sent. If ``None``, then a message constructed from the alert will be sent.
    :type message: str

    :param token: Hipchat API token.
    :type token: str

    :param notify: Hipchat notify flag. Default is False.
    :type notify: bool

    :param color: Message color. Default is ``red`` if alert is raised.
    :type color: str

    :param link: Add link to Hipchat message. Default is ``False``.
    :type color: bool

.. note::

    Message color will be determined based on alert status. If alert has ended, then ``color`` will be ``green``, otherwise ``color`` argument will be used.

    Example message:

    .. code-block:: python

        {
            "message": "NEW ALERT: Requests failing with status 500 on host-production-1-entity",
            "color": "red",
            "notify": true,
        }
