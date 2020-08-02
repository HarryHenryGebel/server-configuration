
# Table of Contents

1.  [About this document](#org34d41fb)
2.  [Acquiring a domain name](#org829685f)
    1.  [A brief introduction to the Domain Name Service](#orgd4b3815)
        1.  [The components of a domain name](#org4bd54c7)
        2.  [DNS servers](#org8550f9e)
    2.  [Registering your own domain name](#orgb86034a)
    3.  [Obtaining a free domain name.](#org9098e03)
    4.  [Static and dynamic IP addresses](#orgbc1b1b0)
3.  [Acquiring a Debian server](#org3becc05)
    1.  [DigitalOcean](#org4d3b512)
    2.  [Google Cloud Platform](#org70ab2f6)
4.  [Getting started](#orgc25bd80)
    1.  [Etckeeper](#org94ea0be)
    2.  [fish](#org94fa025)
    3.  [sudo](#orgc0c8a68)
        1.  [The editor variable](#orgf226df2)
    4.  [SSH server setup](#org90cb67a)
    5.  [NTP server setup](#org6750833)
        1.  [NTP service - sync time only](#org5f70335)
        2.  [NTP service - join the pool of public NTP servers](#orgc551ab8)
        3.  [Checking the status of the NTP service](#org377af7c)
    6.  [Unattended upgrades setup](#orgd77fef7)
5.  [Email server](#org5064a99)
    1.  [Full email service](#orgeb1cab5)
        1.  [Implement encrypted passwords in Dovecot / Postfix](#orgb33182b)
    2.  [Local/forwarded email](#org20d7963)



<a id="org34d41fb"></a>

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
times, as are differing opinions.

This document is released into the public domain where legally
permissible, and all rights are permanently and irrevocably waived, to
the extent permitted by law.


<a id="org829685f"></a>

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


<a id="orgd4b3815"></a>

## A brief introduction to the Domain Name Service


<a id="org4bd54c7"></a>

### The components of a domain name

The domain name service organizes domains into levels, each level is
separated buy a dot, and the levels are organized from right to left
with the rightmost name being the most general and the left most being
the most specific.

The rightmost domain is called the Top Level Domain or TLD. TLDs are
assigned by IANA<sup><a id="fnr.6" class="footref" href="#fn.6">6</a></sup> and their
management is delegated to a network of domain registrars who manage
the leasing of domain names to users, and provide data on the domains
to IANA for use in the root name servers that are ultimately
responsible for making sure any name can be looked up and associated
with the appropriate IP address. A TLD cannot be assigned on its own
and function solely as a way of organizing the lower level domains.

In most cases, the next level down is the domain that can be leased by
an individual or organization for their own use. The exception to that
rule is the two letter country domains such as `us`, `ca`, and `uk`,
which function as a second, level of TLD before leasing domain names
further down to end users.

The leased domain name can be referred to as the domain, and it's owner
can manage it as they see fit, setting up additional levels, assigning
address directly under the domain, or both.

In domain name `central.gebel.tech`, `gebel.tech` is the domain and
`central` is the name of a specific computer located in that domain.

In the domain `www.bbc.co.uk`, bbc.co.uk is the domain and `www`
refers to a specific computer in that domain. It is interesting
because `www.bbc.co.uk` maps to numerous IP address, and every time it
is looked up a different address is provided. This prevents any one of the
domain owner's computers from being overloaded by traffic.


<a id="org8550f9e"></a>

### DNS servers

The DNS requires the owner of each domain to provide at least three
name servers. The IP addresses of these servers are given to the
registrar which leased out the domain and eventually reach the root
name servers that govern all names on the internet. When your computer
wishes to look up the IP address associated with a domain name, it
contacts either a DNS server provided by your ISP, or a public server
such as 8.8.8.8<sup><a id="fnr.7" class="footref" href="#fn.7">7</a></sup>. Note that
name servers are listed in network settings by IP addresses instead of
by name. This is because of a chicken-and-egg problem: you must be
able to contact a name server without knowing it's name, because you
can't look up it's name until you contact it.

The name server contacted sends a request through higher level servers
on the network to discover the name servers provided by the domain
owner. Once the addresses of these servers is determined, one of them
is contacted, and provides the IP address associated with the full
name, or an error if the name does not exist.

For example, if you try to lookup `central.gebel.tech`, your local
name server first queries the DNS network to find the addresses the
name servers provided by the owner of the `gebel.tech` domain. Your
name server then asks one of the `gebel.tech` name servers for the
address of `central.gebel.tech`. The `gebel.tech` name server then
consults it's DNS records and responds with the address. Finally, your
name server returns the address (or error) to your computer. The types
and formats of DNS records are standardized and are discussed in the
next section.

Although the domain owner must provide three name servers, they do not
have to operate those servers themselves. Indeed, when just starting
out with one server they cannot as they have no way of satisfying the
three server minimum. Generally, the name servers will be provided by
the same service that hosts the server, and each service will provide
an interface to manage any domains owned by their customers. This
document will describe how to manage a domain on DigitalOcean, but
other services will be similar.

Operating a server on the DNS network is an advanced topic and well
beyond the scope of this document. In addition to the challenges of
operating the servers themselves, it also involves having a block of
addresses set aside for your domain. This is also outside the scope of
this document, and not practical for the operator of a small network
of just one or a handful of systems.


<a id="orgb86034a"></a>

## Registering your own domain name


<a id="org9098e03"></a>

## Obtaining a free domain name.


<a id="orgbc1b1b0"></a>

## Static and dynamic IP addresses


<a id="org3becc05"></a>

# Acquiring a Debian server


<a id="org4d3b512"></a>

## DigitalOcean


<a id="org70ab2f6"></a>

## Google Cloud Platform

Google Compute Platform (GCP) provides a singe free Debian<sup><a id="fnr.8" class="footref" href="#fn.8">8</a></sup> server to any
user with a Google account.

The hardware specifications for this free server are essentially equal
to the resources of the $5/month server from Digital Ocean, but the
bandwidth limitation is much lower<sup><a id="fnr.9" class="footref" href="#fn.9">9</a></sup>. Owing to the extreme speeds available from
Google's network, bandwidth from Google is generally more expensive
than other providers. While the bandwidth limitations place some
restrictions on the use of the server, it can still be useful for
learning purposes or to provide simple backup services to a server on
another network. Setting up a server using GCP is outside of the scope
of this document, but is similar in many ways to Digital Ocean and
Google provides copious documentation.


<a id="orgc25bd80"></a>

# Getting started

These steps are necessary to get from a fresh, unconfigured server to one
that can be securely logged into and left running.


<a id="org94ea0be"></a>

## Etckeeper

Etckeeper is the first package to install, it will create a git
repository that will control all changes made in the /etc directory.


<a id="org94fa025"></a>

## fish


<a id="orgc0c8a68"></a>

## sudo


<a id="orgf226df2"></a>

### The editor variable


<a id="org90cb67a"></a>

## SSH server setup


<a id="org6750833"></a>

## NTP server setup

> The man with one watch knows what time it is. The man with two watches
> is never sure. &#x2014; Segal's law

Many internet services depend on the clocks on both sides of a
connection to being accurate. The NTP<sup><a id="fnr.10" class="footref" href="#fn.10">10</a></sup>
service synchronizes the system clock to UTC<sup><a id="fnr.11" class="footref" href="#fn.11">11</a></sup> to a high degree of accuracy with minimal configuration. If
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


<a id="org5f70335"></a>

### NTP service - sync time only

Debian comes configured to automatically keep it's clock synced to the
current time using the existing NTP network. No additional
configuration is necessary. Systems which do not function as pool
servers do not require their time to be as accurate as those that do,
therefore systems that are configured only as a client of the NTP
network can safely be operated with a single time source, as if the
source is lost the default configuration can usually correct for clock
drift sufficiently until the source becomes available again.


<a id="orgc551ab8"></a>

### NTP service - join the pool of public NTP servers

The NTP network is organized into a series of layers called
stratum. Stratum 0 are the hyper accurate time sources that are the
source for the time provided by all other stratum. Sources in stratum
0 include the various satellite positioning networks, radio clocks
such as those provide by the NIST<sup><a id="fnr.12" class="footref" href="#fn.12">12</a></sup>, or atomic clocks that may be available if the
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


<a id="org377af7c"></a>

### Checking the status of the NTP service


<a id="orgd77fef7"></a>

## Unattended upgrades setup

You may wish to delay this until you have email set up, but in any
case should not delay longer than necessary.


<a id="org5064a99"></a>

# Email server


<a id="orgeb1cab5"></a>

## Full email service

This will configure the server to manage all email for a domain,
including securely sending email both with local origin and through a
remote client such as Thunderbird or K-9 Mail, receiving emails sent
from other domains, and providing IMAP services to remote clients.


<a id="orgb33182b"></a>

### Implement encrypted passwords in Dovecot / Postfix


<a id="org20d7963"></a>

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

<sup><a id="fn.6" href="#fnr.6">6</a></sup> The Internet Assigned Numbers Authority, part of
ICANN, the Internet Corporation for Assigned Names and Numbers, a
non-profit corporation founded by the US Department of Commerce to
manage worldwide assignment of IP address and domain names

<sup><a id="fn.7" href="#fnr.7">7</a></sup> Provided as a public service by Google

<sup><a id="fn.8" href="#fnr.8">8</a></sup> Like
Digital Ocean there are many other options as well.

<sup><a id="fn.9" href="#fnr.9">9</a></sup> 1GB/month for a free GCP
server, versus 1TB/month for a $5 server from DigitalOcean. On the
other hand, Google only charges for outgoing traffic, while
DigitalOcean charges for both incoming and outgoing, which makes it
attractive for use cases that require significantly more incoming than
outgoing bandwidth

<sup><a id="fn.10" href="#fnr.10">10</a></sup> Network Time Protocol

<sup><a id="fn.11" href="#fnr.11">11</a></sup> Coordinated Universal
Time. UTC is the time zone of London, United Kingdom, but does not
have daylight savings time. It is essentially similar to Greenwich
Mean Time, one major difference being that in GMT, owing to its use in
astronomy and navigation, the day starts at noon, while in UTC the day
starts at midnight. Consequently, while the time will normally be
identical in UTC and GMT, the date will be different between midnight
and noon.

<sup><a id="fn.12" href="#fnr.12">12</a></sup> The United States National
Institute of Standards and Technology, formerly known as the National
Bureau of Standards
