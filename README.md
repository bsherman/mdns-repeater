# mdns-repeater

This is a fork of the [mdns-repeater][upstream] project, with some 
changes to run on [FreeBSD][freebsd] and [pfSense][pfsense]..

## Changes
Included are minor changes and this patch from a contributor to the original author:
http://irq5.io/2011/01/02/mdns-repeater-mdns-across-subnets/#comment-285

## Build Notes
I built on a FreeBSD 8.4 installation. 
This is a gmake Makefile, not the FreeBSD default.

## Deploy Notes
My target deployment is pfSense 2.1 embedded router running nanobsd (embeded 
FreeBSD)..

1) make storage writable
```
pfsense$ /etc/rc.conf_mount_rw
```

2) copy binary to pfsense
```
freebsd$ scp mdns-repeater root@pfsense:/conf/
```

3) create startup script so that this will start upon restart of pfsense
```
pfsense$ cat << EOF > /conf/mdns-repeater.sh
#!/bin/sh
## repeat across LAN and OPT1(wifi)
/usr/local/bin/mdns-repeater vr0 vr2
EOF
pfsense$ chmod 755 /conf/mdns-repeater.sh
pfsense$ cd /usr/local/etc/rc.d 
pfsense$ ln -s /conf/mdns-repeater.sh
```

4) make storage read only again
```
pfsense$ /etc/rc.conf_mount_ro
```

Note: the symlink may get lost on updates to pfsense, but the binary and 
script should remain, so all that may need to happen is re-symlinking.

## Upstream `README.txt`

```
mdns-repeater
==============
mdns-repeater is a Multicast DNS repeater for Linux. Multicast DNS uses the
224.0.0.51 address, which is "administratively scoped" and does not
leave the subnet.

This program re-broadcast mDNS packets from one interface to other interfaces.
It was written primarily to be run on my Linksys WRT54G which runs dd-wrt,
since my wireless network is on a different subnet from my wired network and
I would like my zeroconf devices to work properly across the two subnets.

Since the mDNS protocol sends the AA records in the packet itself, the
repeater does not need to forge the source address. Instead, the source
address is of the interface that repeats the packet.


USAGE
-----
mdns-repeater only requires the interface names and it will do the rest.
For example, the dd-wrt standard installation defines br0 for the wireless
interface and vlan1 as the WAN interface, I would use:

    mdns-repeater br0 vlan1

You can also specify the -f flag for debugging, which prints packets as they
are received.

You are free to modify the code to repeat whatever traffic you require, as
long as you abide by the software license.


LICENSE
--------
Copyright (C) 2011 Darell Tan

This program is free software; you can redistribute it and/or
modify it under the terms of the GNU General Public License
as published by the Free Software Foundation; either version 2
of the License, or (at your option) any later version.

This program is distributed in the hope that it will be useful,
but WITHOUT ANY WARRANTY; without even the implied warranty of
MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
GNU General Public License for more details.

You should have received a copy of the GNU General Public License
along with this program; if not, write to the Free Software
Foundation, Inc., 51 Franklin Street, Fifth Floor, Boston, MA  02110-1301, USA.
```

[upstream]: https://bitbucket.org/geekman/mdns-repeater
[freebsd]: http://freebsd.org
[pfsense]: https://www.pfsense.org
