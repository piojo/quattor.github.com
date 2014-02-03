---
layout: article
title: Getting Started with Quattor
category: documentation
---

Quattor is a complete, but somewhat complex, set of tools for
automated machine management.  This document provides a gentle
introduction that highlights the main capabilities of Quattor by
showing you how to install a server and take partial control of
another machine.  Production use will require a good knowledge of the
toolset and your site configuration.

## Scenario

This mini-tutorial will show you how to make an Aquilon server, which
we'll call `mrburns` and then take partial control of another machine,
which we'll call `homer`.  To demonstrate the basic machine
configuration workflow, you will add a user account to the client
machine.

## Prerequisities

There are some prerequisites to complete this tutorial.  As far as
hardware/virtual machines go you will need:

* One host to act as the Aquilon server.  We'll call it `mrburns`.
  This machine should be running a minimal, updated version of a RHEL
  6 (or compatible).
* One host to act as the client.  We'll call it `homer`.

Through this tutorial, we'll follow the process to make homer receive
and obey basic instructions from mrburns.

You will need to have root access to both of these machines.

You will also need some basic information about these machines:

* DNS hostnames for the machines
* IP addresses of the machines
* An open network between the two machines
* Access from these machines to external repositories

The access to the external repositories are critical to allow the
Quattor software to be installed easily on the tutorial machines.

## Aquilon

After installing mrburns, please refer to
[Aquilon's pre-requisites][prereq], the
[Aquilon installation guide][install] and the
[introduction to an Aquilon site][site].

We'll continue from there.

## Taking control of the client machine

Please set up the [Yum repositories](http://yum.quattor.org) in both
mrburns and homer.  To do so, put the following in
`/etc/yum.repos.d/quattor.repo` in homer:

```ini
[quattor]
name=quattor
baseurl=http://yum.quattor.org/$QUATTOR_VERSION
enabled=1
```

As of this writing, the recommended Quattor version is 13.12. Next,
we'll install the appropriate client packages into homer:

```bash
yum -y install ncm-ncd ncm-spma
```

<dl>
<dt>ncm-ncd</dt>
<dd>is the program that applies configurations into the node. Installing in
will pull in all the appropriate dependencies.</dd>
<dt>ncm-spma</dt>
<dd>is the Quattor plugin that manages software installations. We'll use
it to install the software that changes user accounts.</dd>
</dl>

Finally, we'll tell homer where it should get its configurations
from.  To do so, we'll edit `/etc/ccm.conf` and add this line:

```apache
profile         http://mrburns/profiles/homer.json.gz
```

Now we can check the contents of our profile.  To do so, we'll run
`ncm-query`:

```bash
ncm-query /
```

Or, if we want to see only the configuration for the `accounts`
component:

```bash
ncm-query --component accounts
```

If we like these configurations, it's time to do the deployment.  The
`ncm-ncd` tool will execute the plugins we tell it to, and their
dependencies.  Now, it's as simple as:

```bash
ncm-ncd --configure accounts
```

Or, to run all components:

```bash
ncm-ncd --configure --all
```

Next Steps
----------

Now you have seen how the Quattor server and client software are
installed and used.  Running Quattor in production will require more
detailed knowledge about the Quattor Toolkit and Quattor best
practices.

In particular, you will need to investigate further the following
topics:

* Using Quattor to install a machine from scratch.
* Configuring the security for the Quattor system.

You can find additional documentation concerning these topics and
others in the [documentation][qdocs] part of the Quattor web site.

Feedback
--------

Comments, bug reports, and feature requests are welcome.  Please
subscribe to the Quattor discussion list and raise issues there.  See
the [contact][qcontacts] area of the Quattor web site.

[qdocs]: http://quattor.org/documentation/
[qcontacts]: http://quattor.org/contacts/
[aqyum]: http://yum.quattor.org/aquilon/
[prereq]: /documentation/2012/10/31/aquilon-prerequisites.html
[mainyum]: http://yum.quattor.org/
[install]: /documentation/2012/10/31/install-aquilon.html
[site]: /documentation/2013/10/25/aquilon-site.html
