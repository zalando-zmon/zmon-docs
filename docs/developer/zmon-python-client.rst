.. _zmon-python-client:

*************
Python Client
*************

ZMON provides a python client library that can be imported and used in your own software.

Installation
------------

ZMON python client library is part of :ref:`ZMON CLI <zmon-cli>`.

.. code-block:: bash

    pip3 install --upgrade zmon-cli

Usage
-----

Using ZMON client is pretty straight forward.

.. code-block:: python

    >>> from zmon_cli.client import Zmon

    >>> zmon = Zmon('https://zmon.example.org', token='123')

    >>> entity = zmon.get_entity('entity-1')
    {
        'id': 'entity-1',
        'team': 'ZMON',
        'type': 'instance',
        'data': {'host': '192.168.20.16', 'port': 8080, 'name': 'entity-1-instance'}
    }

    >>> zmon.delete_entity('entity-102')
    True

    >>> check = zmon.get_check_definition(123)

    >>> check['command']
    http('http://www.custom-service.example.org/health').code()

    >>> check['command'] = "http('http://localhost:9090/health').code()"

    >>> zmon.update_check_definition(check)
    {
        'command': "http('http://localhost:9090/health').code()",
        'description': 'Check service health',
        'entities': [{'application_id': 'custom-service', 'type': 'instance'}],
        'id': 123,
        'interval': 60,
        'last_modified_by': 'admin',
        'name': 'Check service health',
        'owning_team': 'ZMON',
        'potential_analysis': None,
        'potential_impact': None,
        'potential_solution': None,
        'source_url': None,
        'status': 'ACTIVE',
        'technical_details': None
    }

Client
------

Exceptions
==========

.. autoclass:: zmon_cli.client.ZmonError
    :members:

.. autoclass:: zmon_cli.client.ZmonArgumentError
    :members:

Zmon
====

.. autoclass:: zmon_cli.client.Zmon
    :members:
