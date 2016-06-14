.. _check-definitions:

*****************
Check Definitions
*****************

Checks are ZMON's way of gathering data from arbitrary entities, e.g. databases, micro services, hosts and more. Create them as describe below using either the UI or the CLI.

Key properties
==============

Command
-------

The command is being executed by the worker and is considered the data gathering part.
It is executed once per selected entity and its result made available to all attached alerts.
You have different wrappers at hand and the ``entity`` variable is also available for access.

Entity Filter
-------------

Select the entities you want the check to execute against in general, often only a type filter is applied, sometimes more specific.
The alert allows you to do more fine grained filtering.
This proves useful to allow checks to be easily reused.

Interval
--------

Specify the interval in seconds at which you want the check to be executed.

Owning team
-----------

This is the team originally creating the check, right now this has little effect.

Creating new checks
===================

Using trial run
---------------

Using the CLI
-------------
