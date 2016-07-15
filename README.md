BOSH Release for tmate
======================

[tmate][1] is a terminal collaboration utility built atop tmux and
SSH.  Canonically, tmate sessions transit the [tmate.io][1]
service, hosted on the public Internet.

Sometimes, that's just not desirable.

This repository packages the server components of tmate up into an
easy-to-use BOSH release for deploying a tmate server on your
terms.

Getting Started on BOSH-lite
----------------------------

Before you can deploy tmate, you're going to need to upload this
BOSH release to your BOSH-lite, using the CLI:

    bosh target https://192.168.50.4:25555
    bosh upload release http://genesis.starkandwayne.com/v1/release/tmate/latest.tgz

You can create a small, working manifest file from this git
repository:

    git clone https://github.com/cloudfoundry-community/tmate-boshrelease
    cd tmate-boshrelease
    ./templates/make_manifest warden
    bosh -n deploy

Once that's deployed, you can pull down the `~/.tmate.conf`
configuration for this tmate server (10.244.141.2 will be the IP
of the deployed tmate server):

    curl http://10.244.141.2 > ~/.tmate.conf
    tmate

You should see something like this:

![tmate Session][screen1]


Configuring tmate
-----------------

Configuration should be pretty straightforward.  There are only
three properties to set:

  - `tmate.debug` - If set to true, enables extra debugging from
     the tmate processs (which you can find in the logs, at
     `/var/vcap/sys/log/tmate/tmate.log`)
  - `tmate.port` - The TCP port to listen to for inbound
    connection requests.  Because the VM already has SSH running,
    this needs to be something other than "22".  We settled on
    a default of "2222".
  - `tmate.host` - The IP address or hostname to advertise to
    connecting clients.

That last one is the most important.

For ease-of-use, you're going to want to assign a static IP
address to your tmate server job, and then configure DNS to point
a memorable name at that address.  For example:

    $ dig IN A tmate.example.com
    tmate.example.com.      9999    IN      A       10.10.10.10

This DNS name is what you want to put in your deployment manifest
for the `tmate.host` property.  Then, when clients connect, the
connection string for others to connect up to the tmate session
will be based on that hostname, and not the IP (or worse,
"localhost").


[1]:       https://tmate.io
[screen1]: doc/session.png
