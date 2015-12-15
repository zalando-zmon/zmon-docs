.. _glossary:

********
Glossary
********

.. KEEP IN ALPHABETCAL ORDER!

.. glossary::

    alert definition
        Alert definitions define when to trigger an alert and for which entity.
        See :ref:`alert-definitions`

    alert condition
        Python expression defining the "threshold" when to trigger an alert. See :ref:`alert-condition`.

    check command
        Python expression defining the value of a check. See :ref:`check-commands`.

    check definition
        A check definition provides a source of data for alerts to monitor. See :ref:`check-definitions`

    dashboard
        A dashboard is the main monitoring page of ZMON and consists of widgets and the list of active alerts.
        See :ref:`dashboards`

    downtime
        In ZMON, downtime refers to a period of time where certain alerts/entities should not be triggered.
        One use case for downtimes are scheduled maintenance works. See :ref:`downtimes`

    entity
        Entities are "objects" to be monitored. Entities can be hosts, Zomcat instances, but they can also be more abstract things like app domains.
        See :ref:`entities`

    JSON
        JavaScript Object Notation. A minimal data interchange format. You probably already know it. If you don't, there's good documentation on its `official page <http://json.org/>`_.

    Markdown
        A simple markup language that can mostly pass for plain text. There's an `introduction <http://daringfireball.net/projects/markdown/basics>`_ and a `syntax reference <http://daringfireball.net/projects/markdown/syntax>`_ on its official page.

    time period
        Alert definition's time period can restrict its active alerting to certain time frames. This allows for alerts to be active e.g. only during work hours.
        See :ref:`time-periods`

    YAML
        Not actually Yet Another Markup Language. A powerful but succinct data interchange format. This document should be sufficient to learn how to use YAML in ZMON. In case it isn't, the `Wikipedia entry on YAML <http://en.wikipedia.org/wiki/Yaml>`_ is actually slightly more useful that the `official documentation <http://yaml.org/spec/1.1/#id857168>`_.

        Note that YAML is a strict superset of :term:`JSON`. That is, wherever YAML is required, JSON can be used instead.

.. KEEP IN ALPHABETCAL ORDER!
