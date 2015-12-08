# Docker file to create Logstash container.
FROM philcryer/min-jessie-jre8

# Setup environment
ENV LS_VERSION 1.5.4
ENV LS_HOME /opt/logstash
ENV LS_CFG_BASE_DIR /etc/logstash
ENV LS_CFG_SSL_DIR ${LS_CFG_BASE_DIR}/.ssl
ENV LS_CFG_FILE_DIR ${LS_CFG_BASE_DIR}/conf.d
ENV LS_USER logstash
ENV LS_GROUP logstash

# Install requirements and Logstash
RUN export DEBIAN_FRONTEND=noninteractive &&\
    apt-get update &&\
    apt-get upgrade -y &&\
    apt-get install -y \
      curl \
      python \
      bash &&\
    mkdir -p \
      ${LS_CFG_SSL_DIR} \
      ${LS_CFG_FILE_DIR}  \
      /opt &&\
    /usr/sbin/update-ca-certificates -f &&\
    curl -sSL --insecure --location https://download.elasticsearch.org/logstash/logstash/logstash-${LS_VERSION}.tar.gz | tar zxf - -C /opt &&\
    ln -s /opt/logstash-${LS_VERSION} ${LS_HOME} &&\
    addgroup ${LS_GROUP} --uid 1000 &&\
    adduser --home ${LS_HOME} --disabled-login --shell /bin/bash -gid 1000 ${LS_USER} &&\
    chown -R ${LS_USER}:${LS_GROUP} ${LS_HOME}/ ${LS_CFG_BASE_DIR} &&\
    chmod 700 ${LS_CFG_SSL_DIR} &&\
    export SSL_CERT_FILE=$LS_HOME/vendor/bundle/jruby/1.9/gems/ftw-0.0.44/lib/ftw/cacert.pem &&\
    $LS_HOME/bin/plugin install logstash-output-amazon_es

# Configure environment
COPY src/ /

# Listen for defaults: 5000/tcp:udp (syslog), 5002/tcp (logstash-forwarder), 5004/tcp (journald), 5006/udp (Logspout), 4560/tcp (log4j)
EXPOSE 5000 5002 5004 5006 4560

# Expose volumes
VOLUME ["${LS_CFG_BASE_DIR}"]

ENTRYPOINT ["/usr/local/bin/docker-entrypoint.sh"]
CMD [""]
