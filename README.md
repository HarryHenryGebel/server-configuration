
# Table of Contents

1.  [Acquiring a domain name](#orgdb66936)
    1.  [Registering your own domain name](#orgeb7b512)
    2.  [Using a free domain name service](#orgdeebeba)
2.  [Acquiring a Debian server](#org7fdac50)
    1.  [Digital Ocean](#org60daf51)
    2.  [Google Compute Cloud](#org3c38372)
3.  [Getting started](#orgbf4ccd2)
    1.  [Etckeeper](#org3ad3e0b)
    2.  [SSH server setup](#org9697487)
    3.  [NTP server setup](#org29d4815)
        1.  [NTP service - sync time only](#orgc946278)
        2.  [NTP service - join the pool of public NTP servers](#org385ae74)
        3.  [Checking the status of the NTP service](#org4881905)
    4.  [Unattended upgrades setup](#org562cf70)
4.  [Email server](#org1b7e8e7)
    1.  [Full email service](#org6f5052f)
        1.  [Implement encrypted passwords in Dovecot / Postfix](#org14bf65b)
    2.  [Local/forwarded email](#org03b4c43)



<a id="orgdb66936"></a>

# Acquiring a domain name


<a id="orgeb7b512"></a>

## Registering your own domain name


<a id="orgdeebeba"></a>

## Using a free domain name service


<a id="org7fdac50"></a>

# Acquiring a Debian server


<a id="org60daf51"></a>

## Digital Ocean


<a id="org3c38372"></a>

## Google Compute Cloud


<a id="orgbf4ccd2"></a>

# Getting started

These steps are necessary to get from a fresh, unconfigured server to one
that can be securely logged into and left running.


<a id="org3ad3e0b"></a>

## Etckeeper

Etckeeper is the first package to install, it will create a git
repository that will control all changes made in the /etc directory.


<a id="org9697487"></a>

## SSH server setup


<a id="org29d4815"></a>

## NTP server setup

> The man with one watch knows what time it is. The man with two watches
> is never sure. &#x2014; Segal's law

Many internet services depend on the clocks on both sides of a
connection to being accurate. The NTP <sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>
service allows clocks to be synchronized to UTC<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup> with a high degree of accuracy with
minimal configuration. If a server exists on a permanent, publicly
accessible IP address, it can optionally be set up to give back to the
internet time community by becoming part of the pool of public time
servers.

Servers should generally be left using UTC as their local timezone. In
this way, logs can easily be compared even though the servers are
physically located in different time zones than the administrator or
other managed servers. UTC is the standard time zone of the internet.


<a id="orgc946278"></a>

### NTP service - sync time only

Debian comes configured to automatically keep it's clock synced to the
current time using the existing NTP network. No additional
configuration is necessary. Systems which do not function as pool
servers do not require their time to be as accurate as those that do,
therefore systems that are configured only as a client of the NTP
network can safely be operated with a single time source, as if the
source is lost the default configuration can usually correct for clock
drift sufficiently until the source becomes available again.


<a id="org385ae74"></a>

### NTP service - join the pool of public NTP servers

The NTP network is organized into a series of layers called
stratum. Stratum 0 are the hyper accurate time sources that are the
source for the time provided by all other stratum. Sources in stratum
0 include the various satellite positioning networks, radio clocks
such as those provide by the NIST <sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup>, or atomic clocks that may be
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
being overwhelmed. The worst case number is 2, since there is no way
to determine which is likely to be correct if they disagree. The ideal
number is generally acknowledged as being between 5 and 7. A server
that has fewer than five sources risks losing enough sources that it
is left with two. A server that exceeds seven sources puts far too
much load on the network.

Additional stratum proceed along the same plan up until
stratum 14. Pool servers should be located at stratum 3 or 4.


<a id="org4881905"></a>

### Checking the status of the NTP service


<a id="org562cf70"></a>

## Unattended upgrades setup

You may wish to delay this until you have email set up, but in any
case should not delay longer than necessary.


<a id="org1b7e8e7"></a>

# Email server


<a id="org6f5052f"></a>

## Full email service

This will configure the server to manage all email for a domain,
including securely sending email both with local origin and through a
remote client such as Thunderbird or K-9 Mail, receiving emails sent
from other domains, and providing IMAP services to remote clients.


<a id="org14bf65b"></a>

### TODO Implement encrypted passwords in Dovecot / Postfix


<a id="org03b4c43"></a>

## TODO Local/forwarded email

This will configure the server to direct locally originating emails
(emails sent from various server administrative tools) to be stored in
a local mailbox or forwarded to the server handling mail for the domain.


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> Network Time Protocol

<sup><a id="fn.2" href="#fnr.2">2</a></sup> Coordinated
Universal Time. UTC is the time zone of London, United Kingdom, but
does not have daylight savings time. It is essentially similar to
Greenwich Mean Time, but in GMT the day starts at noon, while in UTC
the day starts at midnight

<sup><a id="fn.3" href="#fnr.3">3</a></sup> The United States National
Institute of Standards and Technology
