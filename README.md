
# Table of Contents

1.  [Acquiring a domain name](#org2d2529d)
    1.  [Registering your own domain name](#orgac2dc83)
    2.  [Using a free domain name service](#org750053d)
2.  [Acquiring a Debian server](#orgf8712d9)
    1.  [Digital Ocean](#orgdebf3cb)
    2.  [Google Compute Cloud](#org0249e65)
3.  [Getting started](#org9a69c4d)
    1.  [Etckeeper](#org1bd2609)
    2.  [SSH server setup](#org635631b)
    3.  [NTP server setup](#org34cb6f2)
        1.  [NTP service - sync time only](#org62737e1)
        2.  [NTP service - join the pool of public NTP servers](#org03d8137)
        3.  [Checking the status of the NTP service](#orgc7d6255)
    4.  [Unattended upgrades setup](#org05bfcec)
4.  [Email server](#org0691c4c)
    1.  [Full email service](#org2635a07)
        1.  [Implement encrypted passwords in Dovecot / Postfix](#org5d84483)
    2.  [Local/forwarded email](#org0f3f094)



<a id="org2d2529d"></a>

# Acquiring a domain name


<a id="orgac2dc83"></a>

## Registering your own domain name


<a id="org750053d"></a>

## Using a free domain name service


<a id="orgf8712d9"></a>

# Acquiring a Debian server


<a id="orgdebf3cb"></a>

## Digital Ocean


<a id="org0249e65"></a>

## Google Compute Cloud


<a id="org9a69c4d"></a>

# Getting started

These steps are necessary to get from a fresh, unconfigured server to one
that can be securely logged into and left running.


<a id="org1bd2609"></a>

## Etckeeper

Etckeeper is the first package to install, it will create a git
repository that will control all changes made in the /etc directory.


<a id="org635631b"></a>

## SSH server setup


<a id="org34cb6f2"></a>

## NTP server setup

Many internet services depend on the clocks on both sides of a
connection to be set accurately. The NTP <sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>
service allows this to be done with a high degree of accuracy with
minimal configuration. If a server exists on a permanent, publicly
accessible IP address, it can optionally be set up to give back to the
internet time community by becoming part of the pool of public time
servers.

Servers should generally be left using UTC as their local timezone. In
this way, logs can easily be compared even though the servers are
physically located in different time zones that the administrator or
other managed servers. UTC is the standard time of the internet.


<a id="org62737e1"></a>

### NTP service - sync time only

Debian comes configured to automatically keep it's clock synced to the
current time using the existing NTP network. No additional
configuration is necessary. Systems that are configured only as a
client of the NTP network can safely be operated with a single time
source, as if the source is lost the default configuration can usually
correct for clock drift until the source becomes available again.


<a id="org03d8137"></a>

### NTP service - join the pool of public NTP servers

The NTP network is organized into a series of layers called
stratum. Stratum 0 are the hyper accurate time sources that are the
source for the time provided by all other stratum. Sources in stratum
0 include the various satellite positioning networks, radio clocks
such as those provide by the NIST <sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup>, or atomic clocks that may be
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


<a id="orgc7d6255"></a>

### Checking the status of the NTP service


<a id="org05bfcec"></a>

## Unattended upgrades setup

You may wish to delay this until you have email set up, but in any
case should not delay longer than necessary.


<a id="org0691c4c"></a>

# Email server


<a id="org2635a07"></a>

## Full email service

This will configure the server to manage all email for a domain,
including securely sending email both with local origin and through a
remote client such as Thunderbird or K-9 Mail, receiving emails sent
from other domains, and providing IMAP services to remote clients.


<a id="org5d84483"></a>

### TODO Implement encrypted passwords in Dovecot / Postfix


<a id="org0f3f094"></a>

## TODO Local/forwarded email

This will configure the server to direct locally originating emails
(emails sent from various server administrative tools) to be stored in
a local mailbox or forwarded to the server handling mail for the domain.


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> Network Time Protocol

<sup><a id="fn.2" href="#fnr.2">2</a></sup> The United States National
Institute of Standards and Technology
