FROM elasticsearch:7.9.3
MAINTAINER Hermann Mayer "hermann.mayer@hausgold.de"

# You can change this environment variable on run's with -e
ENV MDNS_HOSTNAME=elasticsearch.local

# Keep the settings low for default
ENV ES_JAVA_OPTS='-Xms96m -Xmx96m'
ENV discovery.type='single-node'

# Install system packages
RUN yum install -y epel-release && \
  yum install -y \
    dbus avahi avahi-tools nss-mdns haproxy supervisor

# Copy avahi.sh
COPY config/avahi.sh /usr/local/bin/
RUN chmod +x /usr/local/bin/avahi.sh

# Configure haproxy
COPY config/haproxy.conf /etc/haproxy/haproxy.cfg

# Configure supervisord
COPY config/supervisor/* /etc/supervisord.d/

# Define the command to run per default
CMD /usr/bin/supervisord -nc /etc/supervisord.conf
