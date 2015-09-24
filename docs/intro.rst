************
Introduction
************

ZMON should allow development teams to define checks and alerts for
their monitoring use cases. Check definitions are basically data sources
on which alerts can be triggered. Both :term:`check definition` commands
(“source code” which defines how to get data) and alert conditions
(“thresholds” when to notify) are using Python_ expressions. Checks can
run for multiple so-called entities,  e.g. one check definition would be
used to get data for multiple hosts (“host” is one entity type).

.. image:: images/components.svg

.. _Python: http://www.python.org
