***************
Getting Started
***************

To quickly get started with ZMON, use the preconfigured Vagrant box featured on the `main ZMON repository`_. Make sure you've installed Vagrant *(at least 1.7.4)* and a Vagrant provider like VirtualBox on your machine. Clone the repository with Git:

.. code-block:: bash

   $ git clone https://github.com/zalando/zmon.git
   $ cd zmon/

From within the cloned repository, run:

.. code-block:: bash

   $ vagrant up

Bootstrapping the image for the first time will take a bit of time. You might want to grab some coffee while you wait. :)

When it's finally up, Vagrant will report on how to reach the ZMON web interface:

.. code-block:: bash

    ==> default: ZMON installation is done!
    ==> default: Goto: https://localhost:8443
    ==> default: Login with your GitHub credentials

Creating Your First Alert
=======================

Log In
------

Open your web browser and navigate to the URL reported by Vagrant: e.g. https://localhost:8443/. Click on *Sign In* and enter your GitHub credentials. You'll be redirected to GitHub.

Checks and Alerts
-----------------

An alert shown on ZMON's dashboard typically consists of two parts: the *check-definition*, which is responsible for
fetching the underlying data; and the *alert-definition*, which defines the condition under which the situation is
considered critical. Multiple alerts with different conditions can operate on the same check.

Let's explore this concept now by creating a simple check and defining some alerts on it.

Create a Check-Definition
-------------------------

One way to create a check command is by using ZMON's CLI. (See: :ref:`cli-usage`). A more convenient way, however, is to use the "Trial Run" feature, which enables you to develop checks and alerts, execute them immediately, and note their impact. Once you are happy with your check command and filter, you can save it from the Trial Run directly. Some users prefer to download the YAML definition from there to store and maintain it in Git.

Create an Alert
---------------

In the top navigation of ZMON's web interface, select `Check defs <https://localhost:8443/#/check-definitions>`_ from the list and click on *Website HTTP status*. Then click *"Add New Alert Definition"* to create a new alert for this check-definition. Fill out the form (see example below), and hit *"Save"*:

==================== ==========================
**Name**             Oops ... website is gone!
-------------------- --------------------------
**Description**      Website was not reachable.
-------------------- --------------------------
**Priority**         Priority 1 (red)
-------------------- --------------------------
**Alert Condition**  value!=200
-------------------- --------------------------
**Team**             Team 1
-------------------- --------------------------
**Responsible Team** Team 1
-------------------- --------------------------
**Status**           ACTIVE
==================== ==========================

View Dashboard
--------------

If the alerts condition evaluates True, the alert will appear on the dashboard. Currently there's only one dashboard, and it's configured to show all present alerts. To view the dashboard, select
`Dashboards <https://localhost:8443/#/dashboards>`_ from the main menu and click on *Example Dashboard*.

To see the alert, you must simulate the error condition; try modifying its condition or the check-definition to return an error code). You do this, set the URL in the check command to http://httpstat.us/500. (The number in the URL represents the HTTP error code you will get.)

To see the actual error code in the alert, you might want to create/modify it like this:

==================== ================================
**Name**             Website gone with status {code}
-------------------- --------------------------------
**Description**      Website was not reachable.
-------------------- --------------------------------
**Priority**         Priority 1 (red)
-------------------- --------------------------------
**Alert Condition**  capture(code=value)!=200
-------------------- --------------------------------
**Team**             Team 1
-------------------- --------------------------------
**Responsible Team** Team 1
-------------------- --------------------------------
**Status**           ACTIVE
==================== ================================

.. _cli-usage:

Using the CLI
=========

The ZMON Vagrant box comes preinstalled with *zmon-cli*. To use the CLI, log in to the running Vagrant box with:

.. code-block:: bash

   $ vagrant ssh

The Vagrant box also contains some sample yaml files for creating entities, checks and alerts. You can find these in */vagrant/examples*.

As an example of using ZMON's CLI, let's create a definition to check if google.com is reachable. *cd* to */vagrant/examples/check-definitions* and, using zmon-cli, create a new check-definition:

.. code-block:: bash

   $ cd /vagrant/examples/check-definitions
   $ zmon check-definitions init website-availability.yaml
   $ vim website-availability.yaml

Edit the newly created *website-availability.yaml* to contain the following code. (type :kbd:`i` for insert-mode)

.. code-block:: yaml

   name: "Website HTTP status"
   owning_team: "Team 1"
   command: http("http://httpstat.us/200", timeout=5).code()
   description: "Returns current http status code for Website"
   interval: 60
   entities:
    - type: GLOBAL
   status: ACTIVE

Type :kbd:`ESC :wq RETURN` to save the file.

To push the updated check definition to ZMON, run:

.. code-block:: bash

   $ zmon check-definitions update website-availability.yaml
   Updating check definition... http://localhost:8080/#/check-definitions/view/2

Find more detailed information here: :ref:`zmon-cli`.

.. _main ZMON repository: https://github.com/zalando/zmon
