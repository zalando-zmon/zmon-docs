DNS
---

The ``dns()`` function provide a way to resolve hosts.

.. py:function:: dns(host=None)


Methods of DNS
^^^^^^^^^^^^^^

.. py:method:: resolve(host=None)

    ::

    Return IP address of host. If host is ``None``, then will resolve host used in initialization. If both are ``None`` then exception will be raised.

    :return: IP address
    :rtype: str

    Example query:

    .. code-block:: python

        dns('google.de').resolve()
        '173.194.65.94'

        dns().resolve('google.de')
        '173.194.65.94'
