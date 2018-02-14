.. _tv-login:

*************************
"Read Only" Display Login
*************************

The ZMON front end requires users to login.
However a very common way of deploying dashboards is on TV screens running across office spaces to e.g. render Grafana or ZMON dashboards.
For this ZMON provides you with a way to login a read only authenticated user via one-time tokens.

Those tokens can be created by any real user by login in first and switching to TV mode or via the ZMON CLI.

How does it work
================

First time a valid one time token is used to login we associate a random UUID with it and the device IP.
Both are registered within ZMON to create a persisted session, thus this will continue to work after the frontend gets deployed.

Tokens can't be reused. Once used, it can no longer be used and you need to create a new one. You'll need a different token per additional
device or location. One time token sessions will last up to 365 days.


Using the menu option
+++++++++++++++++++++

First you need to login using your own personal credentials or Single Sign-On mechanism. After logging in you can use the top right
drop-down menu with your username to reveal the "Switch to TV mode" option.

.. image:: /images/switch-tv-mode.png

Clicking this option will replace your login session with a new session using a newly created one time token, but your personal session
will still be valid!. You must log out before leaving the device unattended.

A pop-up dialog will ask you to take action. If you decide to Logout, a new Tab will open to log you out. You can safely
close this Tab after successful logout and return to ZMON, which will now be on TV Mode.

For more information on the Logout URL, please check :doc:`/installation/configuration`.

.. image:: /images/tv-mode-logout-dialog.png

You'll be able to confirm by checking the username in the drop-down menu where your username used to be present. There will be a new username with
the pattern "ZMON_TV_123abc".

.. image:: /images/tv-mode.png

After this you can leave the device safely unattended. TV mode allows only read access to ZMON.

Using the ZMON CLI
++++++++++++++++++

You can also generate one time tokens using the command line tool. The tool also allows you to list which tokens you already generated.

Getting a token
===============

.. code-block:: bash

    zmon onetime-token get

    Retrieving new one-time token ...
    https://zmon.example.org/tv/AocciOWf/
    OK


Login with token
================

Use the URL in the target browser to login directly. This will create a read-only session.

.. code-block:: bash

    https://<your zmon url>/tv/<your token>

.. note::

    Please make sure you access the generated URL in order to login. Appending the <token> to any other ZMON device or location won't work.

Listing existing tokens
=======================

.. code-block:: bash

    zmon onetime-token list

    - bound_at: 2008-05-08 12:16:21.696000
      bound_expires: 1234567800000
      bound_ip: ''
      created: 2008-05-08 12:16:20.533000
      token: 1234abCD
