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
* `cd && appscale deploy test-analytics/testanalytics_frontend/target/` and enter some e-mail address again (no idea what is it good for)
* after the app starts up successfully `appscale relocate test-analytics 80 443` to make the app available on standard ports
* ???
* profit! (or not, continue reading below)


NOTES
=====
* appscale 1.14.0 does NOT work, so do not bother trying... (at least the KVM image)
* cluster does not autostart after reboot BY DESIGN - you have to manually log into the head node and do `appscale down && appscale up` (cause apparently something is running o.O)
  * irc convo log with some relevant info: http://meetbot.eucalyptus.com/channel-logs/freenode/%23appscale/%23appscale.2013-06-24.log
* app and its data SHOULD persist across reboots (fingers crossed)
* you need to manually relocate the app AGAIN if you changed the ports it should be listening on (dafuq?! >.<)

TL;DR: appscale sucks
