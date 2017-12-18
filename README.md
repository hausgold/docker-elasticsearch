![mDNS enabled official/elasticsearch](https://raw.githubusercontent.com/hausgold/docker-elasticsearch/master/docs/assets/project.png)

This Docker images provides the [official/elasticsearch](https://hub.docker.com/_/elasticsearch/) image as base
with the mDNS/ZeroConf stack on top. So you can enjoy [elasticsearch](https://www.elastic.co/products/elasticsearch) while
it is accessible by default as *elasticsearch.local*. (Port 6379)

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

## Other top level domains

By default *.local* is the default mDNS top level domain. This images does not
force you to use it. But if you do not use the default *.local* top level
domain, you need to [configure your host avahi][custom_mdns] to accept it.

## Further reading

* Docker/mDNS demo: https://github.com/Jack12816/docker-mdns
* Archlinux howto: https://wiki.archlinux.org/index.php/avahi
* Ubuntu/Debian howto: https://wiki.ubuntuusers.de/Avahi/

[custom_mdns]: https://wiki.archlinux.org/index.php/avahi#Configuring_mDNS_for_custom_TLD
