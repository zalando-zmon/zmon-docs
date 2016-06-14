.. _permissions:

***********
Permissions
***********

.. note::

  Deprecated / not up to date

ZMON is a privilege-separated web application which allows different access levels for each user role. User roles are fetched from LDAP after successful authentication.

Currently there are 4 different roles:
 - ADMIN
 - LEAD
 - USER
 - VIEWER

The table below lists the different roles and corresponding access levels:

.. csv-table::
   :header: Feature, ADMIN, LEAD, USER, VIEWER, UNAUTHENTICATED

    Trial Run,               yes, yes, yes, no, no
    Add Comment,             yes, yes, yes, no, no
    Delete Comment,          yes, yes [#dc]_, yes [#dc]_, no, no
    Schedule Downtime,       yes, yes, yes, no, no
    Delete Downtime,         yes, yes, yes, no, no
    Add Dashboard,           yes, yes, yes, no, no
    Edit Dashboard,          yes, yes [#ed]_, yes [#ed]_, no, no
    Add Alert Definition,    yes, yes [#aad]_, yes [#aad]_, no, no
    Edit Alert Definition,   yes, yes [#eadl]_, yes [#eadu]_, no, no
    Delete Alert Definition, yes, yes [#dadl]_, yes [#dadu]_, no, no

As you may have noticed, roles VIEWER and UNAUTHENTICATED have the same access level. This will change in the future.
An unauthenticated user will only be able to see the dashboards, while users with role VIEWER will be able to see all resources.

.. rubric:: Notes

.. [#dc]  One can can only remove their own comments.
.. [#ed]  One can can only edit their own dashboards.
.. [#aad] One can create alert definitions for their own team (user belongs to the alert definition’s ``team``). It is also possible to
   create alert definitions for other teams (like incident) if and only if the user belongs to the ``responsible team`` and the alert definition status is INACTIVE.
.. [#eadl] One can edit alert definitions for their own team (user belongs to the alert definitions's ``team``). It is also possible
   to edit alert definitions of other teams (like incident) if and only if the user belongs to the ``responsible team``. Status can only be updated from REJECTED to INACTIVE.
.. [#eadu] One can edit alert definitions of their own team (user belongs to the alert definition's ``team``). It is also possible
   to edit alert definitions of other teams (like incident) if and only if the user belongs to the ``responsible team`` and status is REJECTED or INACTIVE. Status can only be updated from REJECTED to INACTIVE.
.. [#dadl] One can delete alert definitions for their own team (user belongs to the alert definitions's ``team``). It is also possible
   to delete alert definitions of other teams (like incident) if and only if the user belongs to the ``responsible team``.
.. [#dadu] One can edit alert definitions of their own team (user belongs to the alert definition's ``team``).
