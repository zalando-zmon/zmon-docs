Entities
--------

Provides access to ZMON entities.

.. py:function:: entities(service_url, infrastructure_account, verify=True, oauth2=False)

    ::

    Initialize entities wrapper.

    :param service_url: Entities service url.
    :type service_url: str

    :param infrastructure_account: Infrastructure account used to filter entities.
    :type infrastructure_account: str

    :param verify: Verify SSL connection. Default is ``True``.
    :type username: bool

    :param oauth2: Use OAUTH for authentication. Default is ``False``.
    :type oauth2: bool

.. note::

    If `service_url` or `infrastructure_account` were not supplied, their corresponding values in worker plugin config will be used.


Methods of Entities
^^^^^^^^^^^^^^^^^^^

.. py:function:: search_local(**kwargs)

    ::

    Search entities in local infrastructure account. If `infrastructure_account` is not supplied in kwargs, then should search entities "local" to your filtered entities by using the same `infrastructure_account` as a default filter.

    :param kwargs: Filtering kwargs
    :type kwargs: str

    :return: Entities
    :rtype: list

    Example searching all ``instance`` entities in local account:

    .. code-block:: python

        entities().search_local(type='instance')


.. py:function:: search_all(**kwargs)

    ::

    Search all entities.

    :param kwargs: Filtering kwargs
    :type kwargs: str

    :return: Entities
    :rtype: list


.. py:function:: alert_coverage(**kwargs)

    ::

    Return alert coverage for infrastructure_account.

    :param kwargs: Filtering kwargs
    :type kwargs: str

    :return: Alert coverage result.
    :rtype: list


    .. code-block:: python

        entities().alert_coverage(type='instance', infrastructure_account='1052643')

        [
            {
                'alerts': [],
                'entities': [
                    {'id': 'app-1-instance', 'type': 'instance'}
                ]
            }
        ]
