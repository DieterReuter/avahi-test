# avahi-test
Testing Avahi/Bonjour service discovery on a Raspberry Pi


## Using Avahi on a Raspberry Pi (Debian Wheezy)

Let's have a look what Debian packages are already available.
```bash
sudo apt-cache search avahi | grep -i '^avahi-'
avahi-autoipd - Avahi IPv4LL network address configuration daemon
avahi-daemon - Avahi mDNS/DNS-SD daemon
avahi-dbg - Avahi - debugging symbols
avahi-discover - Service discover user interface for avahi
avahi-dnsconfd - Avahi DNS configuration tool
avahi-ui-utils - Avahi GTK+ utilities
avahi-utils - Avahi browsing, publishing and discovery utilities
```

Let's look what packages we already have installed
```bash
dpkg --get-selections | grep -v deinstall | grep -i avahi
avahi-daemon					install
avahi-utils					install
libavahi-client3:armhf				install
libavahi-common-data:armhf			install
libavahi-common3:armhf				install
libavahi-core7:armhf				install
```

First we just install the basic Avahi tools.
```bash
sudo apt-get install avahi-tools
```

Next, let's look what commands we have
```bash
dpkg-query -L avahi-utils | grep '/usr/bin/' | sort
/usr/bin/avahi-browse
/usr/bin/avahi-browse-domains
/usr/bin/avahi-publish
/usr/bin/avahi-publish-address
/usr/bin/avahi-publish-service
/usr/bin/avahi-resolve
/usr/bin/avahi-resolve-address
/usr/bin/avahi-resolve-host-name
/usr/bin/avahi-set-host-name
```

## Discovering the basic services
```bash
avahi-browse -art
+ docker0 IPv4 black-pearl [9a:71:07:dc:8f:b3]               Workstation          local
+   eth0 IPv6 black-pearl [b8:27:eb:72:79:af]               Workstation          local
+   eth0 IPv4 black-pearl [b8:27:eb:72:79:af]               Workstation          local
= docker0 IPv4 black-pearl [9a:71:07:dc:8f:b3]               Workstation          local
   hostname = [black-pearl.local]
   address = [172.17.42.1]
   port = [9]
   txt = []
=   eth0 IPv6 black-pearl [b8:27:eb:72:79:af]               Workstation          local
   hostname = [black-pearl.local]
   address = [2003:65:c739:d630:ba27:ebff:fe72:79af]
   port = [9]
   txt = []
=   eth0 IPv4 black-pearl [b8:27:eb:72:79:af]               Workstation          local
   hostname = [black-pearl.local]
   address = [192.168.2.114]
   port = [9]
   txt = []
```

## Publishing a new service
To publish a new service you just have to create a new .service definition file and store at `/etc/avahi/services/*.service`.
