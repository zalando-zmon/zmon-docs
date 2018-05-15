Hipchat
-------

Notify Hipchat room with alert status.

.. py:function:: send_hipchat(room=None, message=None, token=None, message_format='html', notify=False, color='red', link=False, link_text='go to alert')

    Send Hipchat notification to specified room.

    :param room: Room to be notified.
    :type room: str

    :param message: Message to be sent. If ``None``, then a message constructed from the alert will be sent.
    :type message: str

    :param token: Hipchat API token.
    :type token: str
    
    :param message_format: message format - ``html`` (default) or ``text`` (which correctly treats @mentions).
    :type token: str    

    :param notify: Hipchat notify flag. Default is False.
    :type notify: bool

    :param color: Message color. Default is ``red`` if alert is raised.
    :type color: str

    :param link: Add link to Hipchat message. Default is ``False``.
    :type link: bool

    :param link_text: if ``link`` param is ``True``, this will be displayed as a link in the hipchat message. Default is  ``go to alert``.
    :type link_text: str

.. note::

    Message color will be determined based on alert status. If alert has ended, then ``color`` will be ``green``, otherwise ``color`` argument will be used.

    Example message - using html format (default):

    .. code-block:: python

        {
            "message": "NEW ALERT: Requests failing with status 500 on host-production-1-entity",
            "color": "red",
            "notify": true
        }
        
    Example message - using text format with @mention:

    .. code-block:: python

        {
            "message": "@here NEW ALERT: Requests failing with status 500 on host-production-1-entity",
            "color": "red",
            "notify": true,
            "message_format": "text"
        }
        
