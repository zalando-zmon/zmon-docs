***************
Getting Started
***************

In order to easily get started use our preconfigured Vagrant box.

You need to have Vagrant *(at least 1.7.4)* and a Vagrant Provider (e.g. VirtualBox) installed on your machine.

You can obtain the Vagrant box from https://github.com/zalando/zmon. Either clone the repository or download it as zip archive.

**git**

.. code-block:: bash
    
   $ git clone https://github.com/zalando/zmon.git
   $ cd zmon/

**zip**

.. code-block:: bash
    
   $ wget https://github.com/zalando/zmon/archive/master.zip
   $ unzip master.zip
   $ cd zmon-master/

From within the cloned repository (or extracted zip archive) run:

.. code-block:: bash

   $ vagrant up

Bootstrapping the image for the first time will take quite long. (Time to grab some coffee)

When it's finally up, Vagrant will report on how to reach the ZMON web interface:

.. code-block:: bash

    ==> default: ZMON installation is done!
    ==> default: Goto: http://localhost:38080/
    ==> default: User: admin Password: admin

Create your first alert
=======================

Log in
------

Open your web browser and navigate to the URL reported by Vagrant (e.g. http://localhost:38080/).
In the top right corner click on *LogIn* and enter the credentials (also reported by Vagrant: *admin*/*admin*).

Checks and alerts
-----------------

In ZMON an alert shown on the dashboard typically consists of two parts: the check-definition, which is responsible for 
fetching the underlying data, and the alert-definition, which defines the condition under which the situation is 
considered critical. There can be multiple alerts with different conditions, operating on the same check.

To explore this concept, we'll create a simple check and define some alerts on it.

Create a check-definition
-------------------------

Since it's currently not possible to create a check via web interface, you need to use the zmon-cli for that 
(see: :ref:`cli-usage`).

Create an alert
---------------

To create the alert, we can use the web interface. In the top navigation select 
`Check defs <http://localhost:38080/#/check-definitions>`_ and click on *Website HTTP status* from the list. 
Clicking the button *"Add New Alert Definition"* lets you create a new Alert for this check-definition.
Fill the form like this, and hit *"Save"*:

==================== ==========================
**Name**             Oops.. website is gone!
-------------------- --------------------------
**Description**      website was not reachable.
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

View dashboard
--------------

If the alerts condition evaluates true the alert will show up on the dashboard. Currently there is only one dashboard 
and it's configured to show all present alerts. To view the dashboard, select 
`Dashboards <http://localhost:38080/#/dashboards>`_ from the main menu and click on *Example Dashboard* from the list.

In order to see the alert, you need to simulate the error condition. The alert should show up, if you modify its 
condition or if you modify the check-definition to return an error code. You can simply achieve this by setting the url
in the check command to http://httpstat.us/500. (The number in the url represents the HTTP error code you will get.)

To see the actual error code in the alert you might want to create/modify it like this:

==================== ================================
**Name**             Website gone with status {code}
-------------------- --------------------------------
**Description**      website was not reachable.
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

CLI usage
=========

The ZMON Vagrant box comes preinstalled with *zmon-cli*. In order to use the CLI log in to the running Vagrant box with:

.. code-block:: bash

   $ vagrant ssh

Our Vagrant box also contains some example yaml files for the creation of entities, checks and alerts. You will find 
those in */vagrant/examples*.

As a basic example fo CLI usage we'll create a definition, to check if google.com is reachable.
Therefore *cd* to */vagrant/examples/check-definitions* and create a new check-definition by using the 
zmon-cli.

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

To push the updated check definition to ZMON run:

.. code-block:: bash

   $ zmon check-definitions update site-availability.yaml
   Updating check definition... http://localhost:8080/#/check-definitions/view/2

More information on using the command line client can be found here: :ref:`zmon-cli`.