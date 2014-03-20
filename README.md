test-analytics
==============

A fork of http://code.google.com/p/test-analytics/

License
=======
Apache License 2.0 (as specified at http://code.google.com/p/test-analytics/)

How to: single-node KVM AppScale cluster + Test Analytics
=========================================================

* download KVM image of AppScale-ready VM http://download.appscale.com/apps/AppScale%201.13.0%20KVM%20Image
* `tar xvzf appscale-1.13.0-kvm.tar.gz` and go enjoy a cup of tea/coffee
  * do not bother using your pc for anything else while this is running (IMMV, depends on how bad your IO scheduler is)
* import the .img file as a new VM in virt-manager or whatever, at least 3gb ram recommended
* start the VM
* login as `root` with password `appscale`
* run `appscale init cluster`
* modify cluster config file `vi AppScalefile`
  * change the IPs in ips_layout to the VM IP
  * to make your life easier, consider adding these lines:

    ```
    admin_user: root@localho.st
    admin_pass: 123456
    ```

* `appscale up`, enter admin e-mail and password (eg. root@localho.st and 123456) when/if prompted
* you should now have an appscale cluster up and running
* `git clone https://github.com/saironiq/test-analytics.git`
* `cd test-analytics/testanalytics_frontend && mvn gae:unpack && mvn gae:run` + another cup of your favorite hot drink
* press ^C after the dev servers starts up (no maven target to build the package o.O)
* `mv target/{test-analytics-1.0-SNAPSHOT,war}` to make appscale happy
* `cd && appscale deploy test-analytics/testanalytics_frontend/target/` and enter some e-mail address again (no idea what it's good for)
* after the app starts up successfully `appscale relocate test-analytics 80 443` to make the app available on standard ports
* ???
* profit! (or not, continue reading below)


NOTES
=====
* appscale 1.14.0 does NOT work, so do not bother trying... (at least the KVM image)
  * hangs at waiting for dashboard app to come up or smth like that
* cluster does not autostart after reboot BY DESIGN - you have to manually log into the head node and do `appscale down && appscale up` (cause apparently something is running o.O)
  * irc convo log with some relevant info: http://meetbot.eucalyptus.com/channel-logs/freenode/%23appscale/%23appscale.2013-06-24.log
* app and its data SHOULD persist across reboots (fingers crossed)
* you need to manually relocate the app AGAIN if you changed the ports it should be listening on (dafuq?! >.<)
* SSL certs are different after each cluster start-up (minor issue compared to the ones listed above \*_grumblegrumble_\*)

TL;DR: appscale sucks


Y U NO USE RHEL?!
=================

![Y U NO guy](http://i3.kym-cdn.com/entries/icons/original/000/004/006/y-u-no-guy.jpg)

“Ubuntu is an ancient african word, meaning 'I can't configure Debian.'”
  ~ definition of Ubuntu from urbandictionary.com

rhel6.5
-------
  * out-of-date python (2.7 required for appscale, 2.6 in rhel6)
  * requires external repositories/specfiles for multiple packages (eg. rabbitmq, cassandra, zookeeper)
  * incompatible version of monit in repos, had to be built from source
  * almost every package has a version different from the corresponding ubuntu pkg - possibly incompatible versions
  * various smaller "bugs" not present in ubuntu like missing files and directories during cluster start-up (possibly due to failing python scripts)
  * rake unit tests for appscale failing due to outdated python
  * when attempting to start appscale cluster anyway, the appcontroller can't run/reach the cassandra db (it should start it automatically but doesn't and even when it's started manually nothing happens)
  * all the cluster core services are getting killed wihout being started again (nginx, memcached, monit, ...)
  * the whole thing just doesn't work -.-"

rhel7
-----
  * missing erlang, mock, django, ejabberd, rabbitmq, python-pip, etc...
  * incompatible ruby (1.8 required, has 2.0) - dependencies fail to build ("rcov" gem)
    * this is a serious problem as the whole appcontroller server is written in ruby
