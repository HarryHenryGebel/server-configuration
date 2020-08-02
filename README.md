
# Table of Contents

1.  [About this document](#orgf1d2a20)
2.  [Acquiring a domain name](#org61943ed)
    1.  [A brief introduction to the Domain Name Service](#org444161e)
    2.  [Registering your own domain name](#org0bf9d4e)
    3.  [Obtaining a free domain name.](#org13e5325)
    4.  [Static and dynamic IP addresses](#orgc19c5a2)
3.  [Acquiring a Debian server](#org1f2a868)
    1.  [DigitalOcean](#orgcec6de8)
    2.  [Google Cloud Platform](#org95ab8f2)
4.  [Getting started](#org8015481)
    1.  [Etckeeper](#org8a6ba39)
    2.  [fish](#org06d843b)
    3.  [sudo](#org40e1425)
        1.  [The editor variable](#orgb20b1d0)
    4.  [SSH server setup](#orge31177a)
    5.  [NTP server setup](#orgc36d972)
        1.  [NTP service - sync time only](#org5a2cd10)
        2.  [NTP service - join the pool of public NTP servers](#orgb29d3a1)
        3.  [Checking the status of the NTP service](#orgf37a15c)
    6.  [Unattended upgrades setup](#orgb978582)
5.  [Email server](#org839e7e0)
    1.  [Full email service](#orgc5663e5)
        1.  [Implement encrypted passwords in Dovecot / Postfix](#orgd4b3aa9)
    2.  [Local/forwarded email](#org8ee0e75)



<a id="orgf1d2a20"></a>

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
not come with one (Windows<sup><a id="fnr.2" class="footref" href="#fn.2">2</a></sup>). Patches<sup><a id="fnr.3" class="footref" href="#fn.3">3</a></sup> or suggestions for improvement,
especially regarding security or best practices, are welcome at all
times, as are differing opinions. This document is released into the
public domain where legally permissible, and all rights are
permanently and irrevocably waived, to the extent permitted by law.


<a id="org61943ed"></a>

# Acquiring a domain name

Traffic over the internet is routed to numeric addresses known as IP
address which function similarly to phone numbers. These addresses are
difficult to remember, and so the DNS<sup><a id="fnr.4" class="footref" href="#fn.4">4</a></sup> service
was created to allow them to be looked up by name. DNS provides
various types of records that allow you to look up an IP address from
a domain name, find the services such as the mail server for a domain,
redirect certain names to other names, lookup information about a
domain, and lookup domain names from IP addresses. Purchasing,
or more accurately leasing<sup><a id="fnr.5" class="footref" href="#fn.5">5</a></sup>, your own domain name gives you complete
control of the domain on a yearly basis and, if you wish to host your
own email server, will help greatly in making emails originating from
your domain get the highest trust ratings, preventing emails you send
from being marked as spam or rejected outright.

Another option is to obtain a free name from one of several
sources. These names are subdomains to the domains the free domain
provider owns, and therefore lack some of the name recognition or
branding that can be associated with your own domain. However, they
also lack the yearly costs of purchasing a domain name of your
own. For reasons that will be gone into later, email sent from these
domains will be rejected as spam by many receivers. This is not a
concern if you choose not to host email from your domain, which in any
case is probably not recommended for a free domain.


<a id="org444161e"></a>

## A brief introduction to the Domain Name Service


<a id="org0bf9d4e"></a>

## Registering your own domain name


<a id="org13e5325"></a>

## Obtaining a free domain name.


<a id="orgc19c5a2"></a>

## Static and dynamic IP addresses


<a id="org1f2a868"></a>

# Acquiring a Debian server


<a id="orgcec6de8"></a>

## DigitalOcean


<a id="org95ab8f2"></a>

## Google Cloud Platform

Google Compute Platform (GCP) provides a singe free Debian<sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup> server to any
user with a Google account.

The hardware specifications for this free
server are essentially equal to the resources of the $5/month server
from Digital Ocean, but the bandwidth limitation is much
lower<sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup>. Owing to the extreme speeds available from
Google's network, bandwidth from Google is generally more expensive
than other providers. While the bandwidth limitations place some
limitations on the use of the server, it can still be useful for
learning purposes or to provide simple backup services to a server on
another network. Setting up a server using GCP is outside of the scope
of this document, but is similar in many ways to Digital Ocean and
Google provides copious documentation.


<a id="org8015481"></a>

# Getting started

These steps are necessary to get from a fresh, unconfigured server to one
that can be securely logged into and left running.


<a id="org8a6ba39"></a>

## Etckeeper

Etckeeper is the first package to install, it will create a git
repository that will control all changes made in the /etc directory.


<a id="org06d843b"></a>

## fish


<a id="org40e1425"></a>

## sudo


<a id="orgb20b1d0"></a>

### The editor variable


<a id="orge31177a"></a>

## SSH server setup


<a id="orgc36d972"></a>

## NTP server setup

> The man with one watch knows what time it is. The man with two watches
> is never sure. &#x2014; Segal's law

Many internet services depend on the clocks on both sides of a
connection to being accurate. The NTP<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup>
service synchronizes the system clock to UTC<sup><a id="fnr.9" class="footref" href="#fn.9">9</a></sup> to a high degree of accuracy with minimal configuration. If
a server exists on a permanent, publicly accessible IP address, it can
optionally be set up to give back to the internet time community by
becoming part of the pool of public time servers.

Debian servers come with their local time set to UTC, and should
generally be left with UTC set as their local timezone. In this way,
logs can easily be compared even though the servers are physically
located in different time zones than the administrator or other
managed servers. UTC is the standard time zone of the internet, and
has many advantages over local time including being at all times the
same at all locations worldwide, and it lacks daylight savings time which
renders questions involving time calculations across the jump between
standard time and daylight savings time moot.


<a id="org5a2cd10"></a>

### NTP service - sync time only

Debian comes configured to automatically keep it's clock synced to the
current time using the existing NTP network. No additional
configuration is necessary. Systems which do not function as pool
servers do not require their time to be as accurate as those that do,
therefore systems that are configured only as a client of the NTP
network can safely be operated with a single time source, as if the
source is lost the default configuration can usually correct for clock
drift sufficiently until the source becomes available again.


<a id="orgb29d3a1"></a>

### NTP service - join the pool of public NTP servers

The NTP network is organized into a series of layers called
stratum. Stratum 0 are the hyper accurate time sources that are the
source for the time provided by all other stratum. Sources in stratum
0 include the various satellite positioning networks, radio clocks
such as those provide by the NIST<sup><a id="fnr.10" class="footref" href="#fn.10">10</a></sup>, or atomic clocks that may be available if the
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


<a id="orgf37a15c"></a>

### Checking the status of the NTP service


<a id="orgb978582"></a>

## Unattended upgrades setup

You may wish to delay this until you have email set up, but in any
case should not delay longer than necessary.


<a id="org839e7e0"></a>

# Email server


<a id="orgc5663e5"></a>

## Full email service

This will configure the server to manage all email for a domain,
including securely sending email both with local origin and through a
remote client such as Thunderbird or K-9 Mail, receiving emails sent
from other domains, and providing IMAP services to remote clients.


<a id="orgd4b3aa9"></a>

### Implement encrypted passwords in Dovecot / Postfix


<a id="org8ee0e75"></a>

## Local/forwarded email

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
also available for Windows as an option to use ssh without having a
Unix compatible shell installed on the local
system.

<sup><a id="fn.3" href="#fnr.3">3</a></sup> Please submit any patches or pull requests
against the source file `server-configuration.org`, not against
generated files such as `README.md`

<sup><a id="fn.4" href="#fnr.4">4</a></sup> Domain Name System

<sup><a id="fn.5" href="#fnr.5">5</a></sup> Domains are public resources, and can
only be leased not owned

<sup><a id="fn.6" href="#fnr.6">6</a></sup> Like
Digital Ocean there are many other options as well.

<sup><a id="fn.7" href="#fnr.7">7</a></sup> 1GB/month for a free GCP server, versus 1TB/month for a $5
server from DigitalOcean

<sup><a id="fn.8" href="#fnr.8">8</a></sup> Network Time Protocol

<sup><a id="fn.9" href="#fnr.9">9</a></sup> Coordinated Universal
Time. UTC is the time zone of London, United Kingdom, but does not
have daylight savings time. It is essentially similar to Greenwich
Mean Time, one major difference being that in GMT, owing to its use in
astronomy and navigation, the day starts at noon, while in UTC the day
starts at midnight. Consequently, while the time will normally be
identical in UTC and GMT, the date will be different between midnight
and noon.

<sup><a id="fn.10" href="#fnr.10">10</a></sup> The United States National
Institute of Standards and Technology, formerly known as the National
Bureau of Standards
