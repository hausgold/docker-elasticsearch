FROM elasticsearch:7.10.1
LABEL org.opencontainers.image.authors="containers@hausgold.de"

# You can change this environment variable on run's with -e
ENV MDNS_HOSTNAME=elasticsearch.local

# Keep the settings low for default
ENV ES_JAVA_OPTS='-Xms96m -Xmx96m'
ENV discovery.type='single-node'

# Install system packages
# while fixing EL8 repositories base URLs
# and installing the OKay EL repository
RUN \
  sed -i 's/mirrorlist/#mirrorlist/g' /etc/yum.repos.d/CentOS-* && \
  sed -i \
    's|#baseurl=http://mirror.centos.org|baseurl=http://vault.centos.org|g' \
    /etc/yum.repos.d/CentOS-* && \
  yum install -y epel-release && \
  export OKAY_BASE='http://repo.okay.com.mx/centos/8/x86_64/release' && \
  rpm -ivh "${OKAY_BASE}/okay-release-1-5.el8.noarch.rpm" && \
  yum install -y --nobest --nogpgcheck \
    dbus avahi avahi-tools nss-mdns haproxy supervisor

# Copy custom scripts
COPY config/*.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/*

# Configure haproxy
COPY config/haproxy.conf /etc/haproxy/haproxy.cfg

# Configure supervisord
COPY config/supervisor/* /etc/supervisord.d/

# Define the command to run per default
CMD ["/usr/bin/supervisord", "-nc", "/etc/supervisord.conf"]
