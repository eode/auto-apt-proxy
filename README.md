# NAME

auto-apt-proxy - autodetect common APT proxy setups

## NOTE
This specific version uses MDNS via `avahi-utils` to automatically recognize 
(for example) `squid-deb-proxy` and `apt-cacher-ng`.  If the changes are 
merged upstream and pulled into debian, I'll delete or archive this repository. 

MDNS is not *required*, but if `avahi-browse` from the `avahi-utils` package
is present, it will be used to query MDNS for available apt caches.

### MDNS / Avahi Usage
 * Install `auto-apt-proxy` from this repository.
 * Install the `avahi-utils` package.
 * Install `apt-cacher-ng` or `squid-deb-proxy` on the server/system you want
   to host the cache.
 * Enjoy.

# USAGE

**auto-apt-proxy**

**auto-apt-proxy** *[COMMAND [ARGS ...]]*

# DESCRIPTION

**auto-apt-proxy** is an APT proxy autodetector, and detects common setups by
checking localhost, your gateway and other "interesting" machines on your
network for well-known APT proxies such as apt-cacher-ng and others.

When called with no arguments, **auto-apt-proxy** simply prints the address of
a detected proxy to the standard output. This package installs an APT
configuration file that makes APT use **auto-apt-proxy** to detect a proxy on
every invocation of APT.

When called with arguments, they are assumed to be a command. Such command will
be executed with the common environment variables used for specifying HTTP
proxies (*http_proxy*, *HTTP_PROXY*) set to the detected proxy. This way the
executed command will be able to transparently use any detected APT proxy. Note
that for this to work, any programs invoked by the given command must have
their own support for detecting HTTP proxies from those environment variables,
and for using them.

# CONFIGURATION

When your apt proxy is installed on localhost or your default gateway,
it should Just Work.  If you install it somewhere else, you can create
an explicit SRV record to tell auto-apt-proxy about it.

Suppose your corporate domain is "example.com", and
apt-cacher-ng is installed on "apt-cacher-ng.example.com", and
auto-apt-proxy is installed on "alices-laptop.example.com".

The appropriate SRV record in dnsmasq.conf would look like this:

    srv-host=_apt_proxy._tcp.example.com,apt-cacher-ng.example.com,3142

The appropriate nsd/bind zonefile entry would look like this (untested):

    _apt_proxy._tcp.@  IN SRV 0 0 3142 apt-cacher-ng.@

As an alternative to an SRV record, one can also define a special hostname
which needs to be resolved via DNS or local */etc/hosts* file, called
**apt-proxy**.  For example, if your network has a local apt proxy at 9.9.9.9,
then add this line to */etc/hosts*:

    9.9.9.9    apt-proxy

# CACHING

By default, auto-apt-proxy will cache its results for 60 seconds.

To disable the cache, set the `AUTO_APT_PROXY_NO_CACHE` environment variable to
any non-empty string.

# EXAMPLES

$ **auto-apt-proxy**

Just prints the detected APT proxy

$ **auto-apt-proxy** debootstrap sid /my/chroot

Creates a new Debian *chroot* downloading packages from the local proxy.

# COPYRIGHT

Copyright (C) 2016-2020 Antonio Terceiro

This program is free software: you can redistribute it and/or modify
it under the terms of the GNU General Public License as published by
the Free Software Foundation, either version 3 of the License, or
(at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program.  If not, see <http://www.gnu.org/licenses/>.
