************
Tests
************

Acceptance and Unit Tests
----------------------

These tests must be run from inside the vagrant box.::

       $ vagrant ssh
       vagrant@zmon:~$ cd /vagrant/vagrant/
       vagrant@zmon:/vagrant/vagrant$ sudo ./test.sh

An example output of the previous command can look similar to this::

       Starting Xvfb...
       [13:36:12] Using gulpfile /vagrant/zmon-controller/src/main/webapp/gulpfile.js
       [13:36:12] Starting 'test'...
       Starting selenium standalone server...
       Selenium standalone server started at http://10.0.2.15:47833/wd/hub
       Testing dashboard features
         should display the search form - pass

       Finished in 3.24 seconds
       1 test, 1 assertion, 0 failures

       Shutting down selenium standalone server.
       [13:36:22] Finished 'test' after 10 s

Only one single acceptance test and no unit tests are provided so far. This is still a work in progress.
