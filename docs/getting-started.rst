***************
Getting Started
***************

In order to easily get started use our preconfigured vagrant box.

You can obtain the vagrant box from https://github.com/zalando/zmon. Either clone the repository or download it as zip archive.

You need to have Vagrant and a Vagrant Provider (e.g. VirtualBox) installed on your machine.

From within the cloned repository (or extracted zip archive) run:

.. code-block:: bash

    vagrant up

Bootstrapping the image for the first time will take quite long. ( Time to grab some coffee ;) )

When it's finally up, vagrant will report on how to reach the zmon web interface:

.. code-block:: bash

    ==> default: ZMON installation is done!
    ==> default: Goto: http://localhost:38080/
    ==> default: User: admin Password: admin

Create your first alert
=======================

Log in

Open your web browser and navigate to the url reported by vagrant (e.g. http://localhost:38080/ ).
In the top right corner click on LogIn and enter the credentials (also reported by vagrant: admin/admin).

