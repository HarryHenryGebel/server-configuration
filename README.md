
# Table of Contents

1.  [Acquiring a domain name](#org91cb3d2)
    1.  [Registering your own domain name](#orgf684c57)
    2.  [Using a free domain name service](#orgab6d3a7)
2.  [Acquiring a Debian server](#org1a7c236)
    1.  [Digital Ocean](#orgd806625)
    2.  [Google Compute Cloud](#orgf585ce9)
3.  [Getting started](#org90727a3)
    1.  [Etckeeper](#org53b5343)
    2.  [SSH server setup](#orgd96dc28)
    3.  [NTP server setup](#org17905fe)
        1.  [NTP service - sync time only](#orge281725)
        2.  [NTP service - join the pool of public NTP servers](#org9b4827a)
        3.  [Checking the status of the NTP service](#orgcfc873c)
    4.  [Unattended upgrades setup](#orge4a202f)
4.  [Email server](#orgb6a31b8)
    1.  [Full email service](#orgf279fb7)
        1.  [Implement encrypted passwords in Dovecot / Postfix](#orgddb94b1)
    2.  [Local/forwarded email](#org17ac65e)



<a id="org91cb3d2"></a>

# Acquiring a domain name


<a id="orgf684c57"></a>

## Registering your own domain name


<a id="orgab6d3a7"></a>

## Using a free domain name service


<a id="org1a7c236"></a>

# Acquiring a Debian server


<a id="orgd806625"></a>

## Digital Ocean


<a id="orgf585ce9"></a>

## Google Compute Cloud


<a id="org90727a3"></a>

# Getting started

These steps are necessary to get from a non-existing server to one
that can be safely logged into and left running.


<a id="org53b5343"></a>

## Etckeeper

Etckeeper is the first package to install, it will create a git
repository that will control all changes made in the /etc directory.


<a id="orgd96dc28"></a>

## SSH server setup


<a id="org17905fe"></a>

## NTP server setup

Many internet services depend on the clocks on both sides of a
connection to be set accurately. The NTP (Network Time Protocol)
service allows this to be done with a high degree of accuracy with
minimal configuration. If a server exists on a permanent, publicly
accessible IP address, it can optionally be set up to give back to the
internet time community by becoming part of the pool of public time
servers.

Servers should generally be left using UTC as their local timezone. In
this way, logs can easily be compared even though the servers are
physically located in different time zones that the administrator or
other managed servers. UTC is the standard time of the internet.


<a id="orge281725"></a>

### NTP service - sync time only

Debian comes configured to automatically keep it's clock synced to the
current time using the existing NTP network. No additional
configuration is necessary. Systems that are configured only as a
client of the NTP network can safely be operated with a single time
source, as if the source is lost the default configuration can usually
correct for clock drift until the source becomes available again.


<a id="org9b4827a"></a>

### NTP service - join the pool of public NTP servers

The NTP network is organized into a series of layers called
stratum. Stratum 0 are the hyper accurate time sources that are the
source for the time provided by all other stratum. Sources in stratum
0 include the various satellite positioning networks, radio clocks
such as those provide by the NIST <sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>, or atomic clocks that may be
available if the server is located at a facility that has such a
thing. These sources are also know as reference clocks, and are the
ultimate source of time not just for the NTP network, but for most
time used all over the world.

Stratum 1 servers synchronize their time directly from a reference clock.

Stratum 2 servers synchronize their time by tracking several stratum 1
servers. Several stratum 1 sources are necessary to correct for
stratum 1 servers that may be inoperative, malfunctioning, or
temporarily out of sync.

Stratum 3 servers synchronize their time by tracking several stratum 2
servers. Like stratum 2 servers they require several sources to
maintain accuracy. The ideal number to track must balance the need of
the stratum 3 server to provide an accurate time with the need of the
stratum 2 servers, which suffer extremely heavy traffic, to avoid
being overwhelmed. The worst case number is 2, a fact pointed out by
the famous observation that the man with one watch always knows the
time, but the man with two is never sure. The ideal number is
generally acknowledge as being between 5 and 7. A server that has
fewer than five sources risks losing enough sources that it is left
with two. A server that exceeds seven sources puts far too much load
on the network.

Additional stratum proceed along the same plan up until
stratum 14. Pool servers should be located at stratum 3 or 4.


<a id="orgcfc873c"></a>

### Checking the status of the NTP service


<a id="orge4a202f"></a>

## Unattended upgrades setup

You may wish to delay this until you have email set up, but in any
case should not delay longer than necessary.


<a id="orgb6a31b8"></a>

# Email server


<a id="orgf279fb7"></a>

## Full email service

This will configure the server to manage all email for a domain,
including securely sending email both with local origin and through a
remote client such as Thunderbird or K-9 Mail, receiving emails sent
from other domains, and providing IMAP services to remote clients.


<a id="orgddb94b1"></a>

### TODO Implement encrypted passwords in Dovecot / Postfix


<a id="org17ac65e"></a>

## TODO Local/forwarded email

This will configure the server to direct locally originating emails
(emails sent from various server administrative tools) to be stored in
a local mailbox or forwarded to another email address.


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> The United States National
Institute of Standards and Technology
