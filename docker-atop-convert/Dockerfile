FROM    ubuntu:22.04

ENV DEBIAN_FRONTEND  noninteractive
ENV INFLUXDB_VERSION 1.8.10

RUN \
    apt-get -y update && apt-get -y upgrade ; \
    apt-get -y install curl libfontconfig ;\
    curl -Ls https://dl.influxdata.com/influxdb/releases/influxdb_${INFLUXDB_VERSION}_amd64.deb --output influxdb_${INFLUXDB_VERSION}_amd64.deb && \
    dpkg -i influxdb_${INFLUXDB_VERSION}_amd64.deb && rm influxdb_${INFLUXDB_VERSION}_amd64.deb

#building extra version of atop
RUN \
    set -x -e; \
    apt-get install -y build-essential zlib1g-dev make libncurses-dev ;\
    curl -Ls http://www.atoptool.nl/download/atop-2.4.0.tar.gz --output atop-2.4.0.tar.gz && tar -xvzf atop-2.4.0.tar.gz  && rm atop-2.4.0.tar.gz  ;\
    curl -Ls http://www.atoptool.nl/download/atop-2.6.0.tar.gz --output atop-2.6.0.tar.gz && tar -xvzf atop-2.6.0.tar.gz  && rm atop-2.6.0.tar.gz  ;\
    D=$(pwd); ls -la / /root ; \
    cd /atop-2.4.0  &&  make all genericinstall clean && cd / rm -R /atop-2.4.0  ; \
    cd /usr/bin; ln -fs atop-2.4.0 /usr/bin/atop-2.4    ;\
    cd /usr/bin; ln -fs atop-2.4.0 /usr/bin/atopsar-2.4 ;\
    cd /atop-2.6.0  &&  make all genericinstall clean && cd / rm -R /atop-2.6.0  ; \
    cd /usr/bin; ln -fs atop-2.6.0 /usr/bin/atop-2.6    ;\
    cd /usr/bin; ln -fs atop-2.6.0 /usr/bin/atopsar-2.6 ;\
    rm -f /etc/atop/atop.daily /etc/rc.d/init.d/atop /etc/cron.d/atop ; \
    ls -la /usr/bin/a* /usr/sbin/a* ;\
    apt-get remove -y build-essential make; apt-get autoremove -y

RUN rm -f /etc/systemd/system/influxd.service /etc/systemd/system/multi-user.target.wants/influxdb.service

ADD     parse.awk       /
ADD     convert.sh      /

CMD     ["/convert.sh"]
