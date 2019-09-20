BOSH Release for tmate
======================

[![Built by pipes.starkandwayne.com][badge]][pipes]

[pipes]: https://pipes.starkandwayne.com/teams/main/pipelines/tmate-boshrelease
[badge]: https://pipes.starkandwayne.com/api/v1/teams/main/pipelines/tmate-boshrelease/badge

[tmate][1] is a terminal collaboration utility built atop tmux and
SSH.  Canonically, tmate sessions transit the [tmate.io][1]
service, hosted on the public Internet.

Sometimes, that's just not desirable.

This repository packages the server components of tmate up into an
easy-to-use BOSH release for deploying a tmate server on your
terms.


Deploying tmate to your BOSH Director
-------------------------------------

We have a sample manifest, in `manifests/tmate.yml` that you can
tweak and deploy to get started:

    bosh -e your-bosh-director deploy manifests/tmate.yml \
         -v public_hostname_or_ip=your.tmate.server.example.com

Once that's deployed, you can pull down the `~/.tmate.conf`
configuration for this tmate server:

    curl http://your.tmate.server.example.com > ~/.tmate.conf
    tmate

You should see something like this:

![tmate Session][screen1]


Configuring tmate
-----------------

Configuration should be pretty straightforward.  There are only
three properties to set:

  - `debug` - If set to true, enables extra debugging from
     the tmate processs (which you can find in the logs, at
     `/var/vcap/sys/log/tmate/tmate.log`)

  - `port` - The TCP port to listen to for inbound
    connection requests.  Because the VM already has SSH running,
    this needs to be something other than "22".  We settled on
    a default of "2222".

  - `host` - The IP address or hostname to advertise to
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
