Mail
----

Send email notifications.

.. py:function:: send_mail(subject=None, cc=None, html=False, hide_recipients=True, include_value=True, include_definition=True, include_captures=True, include_entity=True, per_entity=True)

    Send email notification.

    :param subject: Email subject.
                    You must use a unicode string (e.g. `u'äöüß'`) if you have non-ASCII
                    characters in there.
                    If None, the alert name will be used.
    :type subject: str or unicode or None

    :param cc: List of CC recipients.
    :type cc: list

    :param html: HTML email.
    :type html: bool

    :param hide_recipients: Hide recipients. Will be sent as BCC.
    :type hide_recipients: bool

    :param include_value: Include alert value in notification message.
    :type include_value: bool

    :param include_definition: Include alert definition details in notification message.
    :type include_definition: bool

    :param include_captures: Include alert captures in message.
    :type include_captures: bool

    :param include_entity: Include affected entities in notification message.
    :type include_entity: bool

    :param per_entity: Send new email notification per entity. Default is ``True``.
    :type per_entity: bool


.. note::

    ``send_email`` is an alias for this notification function.
