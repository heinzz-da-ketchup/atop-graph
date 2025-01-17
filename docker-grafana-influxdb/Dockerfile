FROM    ubuntu:22.04

ENV GRAFANA_VERSION  9.2.3
ENV INFLUXDB_VERSION 1.8.10
ENV GOSU_VERSION 1.14

# Prevent some error messages
ENV DEBIAN_FRONTEND noninteractive

ENV     GRAFANA_PORT            3000
ENV     PRE_CREATE_DB           data grafana
ENV     INFLUXDB_URL            http://localhost:8086
ENV     INFLUXDB_DATA_USER      data
ENV     INFLUXDB_DATA_PW        data
ENV     INFLUXDB_GRAFANA_USER   grafana
ENV     INFLUXDB_GRAFANA_PW     grafana
ENV     ROOT_PW                 root

RUN     apt-get -y update && apt-get -y upgrade ; \
        apt-get -y install supervisor curl libfontconfig netcat jq ;\
        curl -Ls https://dl.grafana.com/oss/release/grafana_${GRAFANA_VERSION}_amd64.deb --output grafana_${GRAFANA_VERSION}_amd64.deb && \
            dpkg -i grafana_${GRAFANA_VERSION}_amd64.deb && rm grafana_${GRAFANA_VERSION}_amd64.deb ; \
	curl -Ls https://dl.influxdata.com/influxdb/releases/influxdb_${INFLUXDB_VERSION}_amd64.deb --output influxdb_${INFLUXDB_VERSION}_amd64.deb && \
	    dpkg -i influxdb_${INFLUXDB_VERSION}_amd64.deb && rm influxdb_${INFLUXDB_VERSION}_amd64.deb ; \
        curl -s -q -L https://github.com/tianon/gosu/releases/download/${GOSU_VERSION}/gosu-amd64 > /usr/sbin/gosu && \
        chmod +x /usr/sbin/gosu

# ----------------- #
#   Configuration   #
# ----------------- #

# Configure InfluxDB
ADD     influxdb/config.toml /etc/influxdb/config.toml
ADD     influxdb/run.sh      /usr/local/bin/run_influxdb

# Configure Grafana
ADD     ./grafana/grafana.ini /etc/grafana/grafana.ini
ADD     ./grafana/run.sh      /usr/local/bin/run_grafana
#ADD    ./grafana/scripted.json /src/grafana/app/dashboards/default.json

ADD     ./configure.sh /configure.sh
ADD     ./set_grafana.sh /set_grafana.sh
ADD     ./set_influxdb.sh /set_influxdb.sh
ADD     ./import_dashboards.sh /import_dashboards.sh

# Set dashboards
ADD    ./dashboards/ /var/lib/grafana/dashboards/

RUN     /configure.sh

# Configure nginx and supervisord
ADD     ./supervisord.conf /etc/supervisor/conf.d/supervisord.conf

RUN     apt-get autoremove -y jq netcat && \
            apt-get -y clean && \
            rm -rf /var/lib/apt/lists/* && rm /*.sh

# Grafana
EXPOSE  3000

# InfluxDB Admin server
EXPOSE  8083

# InfluxDB HTTP API
EXPOSE  8086

# InfluxDB HTTPS API
EXPOSE  8084

CMD     ["/usr/bin/supervisord"]
