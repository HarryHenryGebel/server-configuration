
# Table of Contents

1.  [About this document](#org5c89293)
2.  [Acquiring a domain name](#org4f4ccb0)
    1.  [Registering your own domain name](#orgf8d2ab7)
    2.  [Using a free domain name service](#org564f13c)
    3.  [Static and dynamic IP addresses](#org8953749)
3.  [Acquiring a Debian server](#orgf1dc5c5)
    1.  [Digital Ocean](#org2642bb2)
    2.  [Google Compute Cloud](#orgc27ab70)
4.  [Getting started](#org3af7568)
    1.  [Etckeeper](#org99d8eda)
    2.  [fish](#orgbae1ab2)
    3.  [sudo](#org203ea79)
        1.  [The editor variable](#orga0e6853)
    4.  [SSH server setup](#org3717bdb)
    5.  [NTP server setup](#orge906fff)
        1.  [NTP service - sync time only](#orga545540)
        2.  [NTP service - join the pool of public NTP servers](#org6e96e5c)
        3.  [Checking the status of the NTP service](#org2b993aa)
    6.  [Unattended upgrades setup](#orgb45e4ca)
5.  [Email server](#org383d197)
    1.  [Full email service](#orgc9aa45a)
        1.  [Implement encrypted passwords in Dovecot / Postfix](#org56c8397)
    2.  [Local/forwarded email](#orgb44ae6c)



<a id="org5c89293"></a>

# About this document

This document is an opinionated description of how to set up a server
from scratch, including the reasons for certain decisions. It is
intended primarily for the author's own benefit, both as a writing
exercise and as a reference, but is made available on a public
repository in the hope that others may find it useful, or at least
educational or exasperating. It assumes the user knows how to use ssh
and has already generated a ssh key pair for logins<sup><a id="fnr.1" class="footref" href="#fn.1">1</a></sup>, and that the user already possesses
at least a rudimentary knowledge of working from the command line and
has their local system set up with a Unix compatible shell if it does
not come with one (Windows<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup>). Patches or suggestions for
improvement, especially regarding security or best practices, are
welcome at all times, as are differing opinions. This document is
released into the public domain where legally permissible, and all
rights are permanently and irrevocably waived, to the extent permitted
by law.


<a id="org4f4ccb0"></a>

# Acquiring a domain name


<a id="orgf8d2ab7"></a>

## Registering your own domain name


<a id="org564f13c"></a>

## Using a free domain name service


<a id="org8953749"></a>

## Static and dynamic IP addresses


<a id="orgf1dc5c5"></a>

# Acquiring a Debian server


<a id="org2642bb2"></a>

## Digital Ocean


<a id="orgc27ab70"></a>

## Google Compute Cloud


<a id="org3af7568"></a>

# Getting started

These steps are necessary to get from a fresh, unconfigured server to one
that can be securely logged into and left running.


<a id="org99d8eda"></a>

## Etckeeper

Etckeeper is the first package to install, it will create a git
repository that will control all changes made in the /etc directory.


<a id="orgbae1ab2"></a>

## fish


<a id="org203ea79"></a>

## sudo


<a id="orga0e6853"></a>

### The editor variable


<a id="org3717bdb"></a>

## SSH server setup


<a id="orge906fff"></a>

## NTP server setup

> The man with one watch knows what time it is. The man with two watches
> is never sure. &#x2014; Segal's law

Many internet services depend on the clocks on both sides of a
connection to being accurate. The NTP<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup>
service allows clocks to be synchronized to UTC<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup> with a high degree of accuracy with and
minimal configuration. If a server exists on a permanent, publicly
accessible IP address, it can optionally be set up to give back to the
internet time community by becoming part of the pool of public time
servers.

Debian servers come with their local time set to UTC, and should
generally be left with UTC set as their local timezone. In this way,
logs can easily be compared even though the servers are physically
located in different time zones than the administrator or other
managed servers. UTC is the standard time zone of the internet, and
has many advantages over local time including being at all times the
same at all locations worldwide, and it lacks daylight savings time which
renders questions involving time calculations across the jump between
standard time and daylight savings time moot.


<a id="orga545540"></a>

### NTP service - sync time only

Debian comes configured to automatically keep it's clock synced to the
current time using the existing NTP network. No additional
configuration is necessary. Systems which do not function as pool
servers do not require their time to be as accurate as those that do,
therefore systems that are configured only as a client of the NTP
network can safely be operated with a single time source, as if the
source is lost the default configuration can usually correct for clock
drift sufficiently until the source becomes available again.


<a id="org6e96e5c"></a>

### NTP service - join the pool of public NTP servers

The NTP network is organized into a series of layers called
stratum. Stratum 0 are the hyper accurate time sources that are the
source for the time provided by all other stratum. Sources in stratum
0 include the various satellite positioning networks, radio clocks
such as those provide by the NIST<sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup>, or atomic clocks that may be available if the
server is located at a facility that has such a thing. These sources
are also know as reference clocks, and are the ultimate source of time
not just for the NTP network, but for most time used all over the
world.

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


<a id="org2b993aa"></a>

### Checking the status of the NTP service


<a id="orgb45e4ca"></a>

## Unattended upgrades setup

You may wish to delay this until you have email set up, but in any
case should not delay longer than necessary.


<a id="org383d197"></a>

# Email server


<a id="orgc9aa45a"></a>

## Full email service

This will configure the server to manage all email for a domain,
including securely sending email both with local origin and through a
remote client such as Thunderbird or K-9 Mail, receiving emails sent
from other domains, and providing IMAP services to remote clients.


<a id="org56c8397"></a>

### TODO Implement encrypted passwords in Dovecot / Postfix


<a id="orgb44ae6c"></a>

## TODO Local/forwarded email

This will configure the server to direct locally originating emails
(emails sent from various server administrative tools) to be stored in
a local mailbox or forwarded to the server handling mail for the domain.


# Footnotes

<sup><a id="fn.1" href="#fnr.1">1</a></sup> Users of
Github may have already set up a ssh key pair, and may safely use the
same key pair they use to interact with their github repositories to
log into any servers they create

<sup><a id="fn.2" href="#fnr.2">2</a></sup> Some examples of Windows projects that
include Unix compatible shells are Git for Windows (Gitbash), the
Windows Subsystem for Linux, Cygwin, and MinGW. There are other
options as well, these are the ones the author is aware of. PuTTY is
also an option to use ssh without having a Unix compatible shell
installed on the local system.

<sup><a id="fn.3" href="#fnr.3">3</a></sup> Network Time Protocol

<sup><a id="fn.4" href="#fnr.4">4</a></sup> Coordinated
Universal Time. UTC is the time zone of London, United Kingdom, but
does not have daylight savings time. It is essentially similar to
Greenwich Mean Time, one major difference being that in GMT, owing to
its use in astronomy and navigation, the day starts at noon, while in
UTC the day starts at midnight. Consequently, while the time will
normally be identical in UTC and GMT, the date will be different
between midnight and noon.

<sup><a id="fn.5" href="#fnr.5">5</a></sup> The United States National
Institute of Standards and Technology, formerly known as the National
Bureau of Standards
