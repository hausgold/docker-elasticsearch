![mDNS enabled official/elasticsearch](https://raw.githubusercontent.com/hausgold/docker-elasticsearch/master/docs/assets/project.png)

[![Source Code](https://img.shields.io/badge/source-on%20github-blue.svg)](https://github.com/hausgold/docker-elasticsearch)
[![Docker Image](https://img.shields.io/badge/image-on%20docker%20hub-blue.svg)](https://hub.docker.com/r/hausgold/elasticsearch/)

This Docker images provides the [official/elasticsearch](https://hub.docker.com/_/elasticsearch/) image as base
with the mDNS/ZeroConf stack on top. So you can enjoy [elasticsearch](https://www.elastic.co/products/elasticsearch) while
it is accessible by default as *elasticsearch.local*. (Port 9200, 80 via proxy)

- [Requirements](#requirements)
- [Getting starting](#getting-starting)
- [docker-compose usage example](#docker-compose-usage-example)
- [Host configs](#host-configs)
- [Configure a different mDNS hostname](#configure-a-different-mdns-hostname)
- [Other top level domains](#other-top-level-domains)
- [Further reading](#further-reading)

## Requirements

* Host enabled Avahi daemon
* Host enabled mDNS NSS lookup

## Getting starting

You just need to run it like that, to get a working elasticsearch:

```bash
$ docker run --rm hausgold/elasticsearch
```

The port 9200 is proxied by haproxy to port 80 to make *elasticsearch.local*
directly accessible. The port 9200 and 9300 are untouched.

## docker-compose usage example

```yaml
elasticsearch:
  image: hausgold/elasticsearch
  environment:
    # Mind the .local suffix
    - MDNS_HOSTNAME=elasticsearch.test.local
  ports:
    # The ports are just for you to know when configure your
    # container links, on depended containers
    - "9200"
    - "9300"
```

## Host configs

Install the nss-mdns package, enable and start the avahi-daemon.service. Then,
edit the file /etc/nsswitch.conf and change the hosts line like this:

```bash
hosts: ... mdns4_minimal [NOTFOUND=return] resolve [!UNAVAIL=return] dns ...
```

## Configure a different mDNS hostname

The magic environment variable is *MDNS_HOSTNAME*. Just pass it like that to
your docker run command:

```bash
$ docker run --rm -e MDNS_HOSTNAME=something.else.local hausgold/elasticsearch
```

This will result in *something.else.local*.

You can also configure multiple aliases (CNAME's) for your container by
passing the *MDNS_CNAMES* environment variable. It will register all the comma
separated domains as aliases for the container, next to the regular mDNS
hostname.

```bash
$ docker run --rm \
  -e MDNS_HOSTNAME=something.else.local \
  -e MDNS_CNAMES=nothing.else.local,special.local \
  hausgold/elasticsearch
```

This will result in *something.else.local*, *nothing.else.local* and
*special.local*.

## Other top level domains

By default *.local* is the default mDNS top level domain. This images does not
force you to use it. But if you do not use the default *.local* top level
domain, you need to [configure your host avahi][custom_mdns] to accept it.

## Further reading

* Docker/mDNS demo: https://github.com/Jack12816/docker-mdns
* Archlinux howto: https://wiki.archlinux.org/index.php/avahi
* Ubuntu/Debian howto: https://wiki.ubuntuusers.de/Avahi/

[custom_mdns]: https://wiki.archlinux.org/index.php/avahi#Configuring_mDNS_for_custom_TLD
