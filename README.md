# avahi-test
Testing Avahi/Bonjour service discovery on a Raspberry Pi

## Reference Docs
http://manpages.ubuntu.com/manpages/hardy/man5/avahi-daemon.conf.5.html


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

## After changing the hostname run
```bash
service avahi-daemon restart
```


## Install avahi-browse
```bash
sudo apt-get install -y avahi-utils
```

### Generate some UUID's
```bash
🐳  uuidgen
ABCA3DDC-227F-4F73-8F53-F71A589017EC
🐳  uuidgen
A84DD0FA-F9C7-4A76-A341-078C32894C3F
```

### Define some Services
```bash
scp services/* root@192.168.2.110:/etc/avahi/services/
scp services/* root@192.168.2.115:/etc/avahi/services/
```

### Now discover the services
```bash
avahi-browse _services._dns-sd._udp -t
+ docker0 IPv4 _ssh                                          _tcp                 local
+ docker0 IPv4 _hypriotos                                    _tcp                 local
+ docker0 IPv4 _docker                                       _tcp                 local
+ docker0 IPv4 _docker-host                                  _tcp                 local
+ docker0 IPv4 _docker-engine                                _tcp                 local
+ docker0 IPv4 _workstation                                  _tcp                 local
+   eth0 IPv6 _docker-engine                                _tcp                 local
+   eth0 IPv6 _docker-host                                  _tcp                 local
+   eth0 IPv6 _ssh                                          _tcp                 local
+   eth0 IPv6 _hypriotos                                    _tcp                 local
+   eth0 IPv6 _docker                                       _tcp                 local
+   eth0 IPv6 _workstation                                  _tcp                 local
+   eth0 IPv4 _docker-engine                                _tcp                 local
+   eth0 IPv4 _docker-host                                  _tcp                 local
+   eth0 IPv4 _ssh                                          _tcp                 local
+   eth0 IPv4 _hypriotos                                    _tcp                 local
+   eth0 IPv4 _docker                                       _tcp                 local
+   eth0 IPv4 _workstation                                  _tcp                 local
```

```bash
avahi-browse -d local _docker-host._tcp --resolve -t
+ docker0 IPv4 roaring-thunder Docker-Host                   _docker-host._tcp    local
+   eth0 IPv6 roaring-thunder Docker-Host                   _docker-host._tcp    local
+   eth0 IPv6 rusty-armada Docker-Host                      _docker-host._tcp    local
+   eth0 IPv4 roaring-thunder Docker-Host                   _docker-host._tcp    local
+   eth0 IPv4 rusty-armada Docker-Host                      _docker-host._tcp    local
= docker0 IPv4 roaring-thunder Docker-Host                   _docker-host._tcp    local
   hostname = [roaring-thunder.local]
   address = [172.17.42.1]
   port = [22]
   txt = ["ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"]
=   eth0 IPv6 roaring-thunder Docker-Host                   _docker-host._tcp    local
   hostname = [roaring-thunder.local]
   address = [2003:65:c739:d630:ba27:ebff:fe72:79af]
   port = [22]
   txt = ["ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"]
=   eth0 IPv6 rusty-armada Docker-Host                      _docker-host._tcp    local
   hostname = [rusty-armada.local]
   address = [2003:65:c739:d630:ba27:ebff:fe45:aba]
   port = [22]
   txt = ["ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"]
=   eth0 IPv4 roaring-thunder Docker-Host                   _docker-host._tcp    local
   hostname = [roaring-thunder.local]
   address = [192.168.2.115]
   port = [22]
   txt = ["ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"]
=   eth0 IPv4 rusty-armada Docker-Host                      _docker-host._tcp    local
   hostname = [rusty-armada.local]
   address = [192.168.2.110]
   port = [22]
   txt = ["ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"]

avahi-browse -d local _docker-host._tcp --resolve -t -p
+;docker0;IPv4;roaring-thunder\032Docker-Host;_docker-host._tcp;local
+;eth0;IPv6;roaring-thunder\032Docker-Host;_docker-host._tcp;local
+;eth0;IPv6;rusty-armada\032Docker-Host;_docker-host._tcp;local
+;eth0;IPv4;roaring-thunder\032Docker-Host;_docker-host._tcp;local
+;eth0;IPv4;rusty-armada\032Docker-Host;_docker-host._tcp;local
=;eth0;IPv6;rusty-armada\032Docker-Host;_docker-host._tcp;local;rusty-armada.local;2003:65:c739:d630:ba27:ebff:fe45:aba;22;"ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"
=;eth0;IPv4;rusty-armada\032Docker-Host;_docker-host._tcp;local;rusty-armada.local;192.168.2.110;22;"ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"
=;eth0;IPv6;roaring-thunder\032Docker-Host;_docker-host._tcp;local;roaring-thunder.local;2003:65:c739:d630:ba27:ebff:fe72:79af;22;"ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"
=;docker0;IPv4;roaring-thunder\032Docker-Host;_docker-host._tcp;local;roaring-thunder.local;172.17.42.1;22;"ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"
=;eth0;IPv4;roaring-thunder\032Docker-Host;_docker-host._tcp;local;roaring-thunder.local;192.168.2.115;22;"ssh-user=root,os-release=hypriot,docker-host-uuid=ABCA3DDC-227F-4F73-8F53-F71A589017EC"
```

```bash
avahi-browse -d local _docker-engine._tcp --resolve -t
+ docker0 IPv4 roaring-thunder Docker-Engine                 _docker-engine._tcp  local
+   eth0 IPv6 roaring-thunder Docker-Engine                 _docker-engine._tcp  local
+   eth0 IPv6 rusty-armada Docker-Engine                    _docker-engine._tcp  local
+   eth0 IPv4 roaring-thunder Docker-Engine                 _docker-engine._tcp  local
+   eth0 IPv4 rusty-armada Docker-Engine                    _docker-engine._tcp  local
= docker0 IPv4 roaring-thunder Docker-Engine                 _docker-engine._tcp  local
   hostname = [roaring-thunder.local]
   address = [172.17.42.1]
   port = [2376]
   txt = ["docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"]
=   eth0 IPv6 roaring-thunder Docker-Engine                 _docker-engine._tcp  local
   hostname = [roaring-thunder.local]
   address = [2003:65:c739:d630:ba27:ebff:fe72:79af]
   port = [2376]
   txt = ["docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"]
=   eth0 IPv6 rusty-armada Docker-Engine                    _docker-engine._tcp  local
   hostname = [rusty-armada.local]
   address = [2003:65:c739:d630:ba27:ebff:fe45:aba]
   port = [2376]
   txt = ["docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"]
=   eth0 IPv4 roaring-thunder Docker-Engine                 _docker-engine._tcp  local
   hostname = [roaring-thunder.local]
   address = [192.168.2.115]
   port = [2376]
   txt = ["docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"]
=   eth0 IPv4 rusty-armada Docker-Engine                    _docker-engine._tcp  local
   hostname = [rusty-armada.local]
   address = [192.168.2.110]
   port = [2376]
   txt = ["docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"]

avahi-browse -d local _docker-engine._tcp --resolve -t -p
+;docker0;IPv4;roaring-thunder\032Docker-Engine;_docker-engine._tcp;local
+;eth0;IPv6;roaring-thunder\032Docker-Engine;_docker-engine._tcp;local
+;eth0;IPv6;rusty-armada\032Docker-Engine;_docker-engine._tcp;local
+;eth0;IPv4;roaring-thunder\032Docker-Engine;_docker-engine._tcp;local
+;eth0;IPv4;rusty-armada\032Docker-Engine;_docker-engine._tcp;local
=;eth0;IPv6;roaring-thunder\032Docker-Engine;_docker-engine._tcp;local;roaring-thunder.local;2003:65:c739:d630:ba27:ebff:fe72:79af;2376;"docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"
=;docker0;IPv4;roaring-thunder\032Docker-Engine;_docker-engine._tcp;local;roaring-thunder.local;172.17.42.1;2376;"docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"
=;eth0;IPv4;roaring-thunder\032Docker-Engine;_docker-engine._tcp;local;roaring-thunder.local;192.168.2.115;2376;"docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"
=;eth0;IPv6;rusty-armada\032Docker-Engine;_docker-engine._tcp;local;rusty-armada.local;2003:65:c739:d630:ba27:ebff:fe45:aba;2376;"docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"
=;eth0;IPv4;rusty-armada\032Docker-Engine;_docker-engine._tcp;local;rusty-armada.local;192.168.2.110;2376;"docker-version=1.6.2,docker-api=1.18,docker-osarch=linux/arm,docker-engine-uuid=A84DD0FA-F9C7-4A76-A341-078C32894C3F"
```