How to set up a single-node AppScale cluster in KVM and run Test Analytics on it
================================================================================

* download KVM image of AppScale-ready VM http://download.appscale.com/apps/AppScale%201.13.0%20KVM%20Image
* `tar xvzf appscale-1.13.0-kvm.tar.gz`
* import the .img file as a new VM in virt-manager or whatever
* start the VM
* login as `root` with password `appscale`
* run `appscale init cluster`
* modify cluster config file `vi AppScalefile` - change the IPs in ips_layout to the VM IP
* `appscale up`, enter admin e-mail and password (eg. root@localho.st and 123456)
* `git clone https://github.com/saironiq/test-analytics.git`
* `cd test-analytics/testanalytics_frontend && mvn gae:unpack && mvn gae:run`
* ^C after the dev servers starts up (no target for package, wtf)
* `mv target/{test-analytics-1.0-SNAPSHOT,war}`
* `cd && appscale deploy test-analytics/testanalytics_frontend/target/` and enter some e-mail address again (eg. root@localho.st)
