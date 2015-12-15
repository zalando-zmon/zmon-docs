***************
Getting Started
***************

In order to easily get started use our preconfigured Vagrant box.

You need to have Vagrant and a Vagrant Provider (e.g. VirtualBox) installed on your machine.

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