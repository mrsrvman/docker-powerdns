FROM debian:stretch-slim

MAINTAINER Fabio Rauber <fabiorauber@gmail.com>

ENV PDNSCONF_LAUNCH="gmysql" \
    PDNSCONF_GMYSQL_HOST="mysql" \
    PDNSCONF_GMYSQL_USER='pdns' \
    PDNSCONF_GMYSQL_DBNAME='pdns' \
    PDNSCONF_GMYSQL_PASSWORD='pdnspw' \
    PDNSCONF_INCLUDE_DIR="/etc/powerdns/pdns.d" \
    PDNSCONF_GMYSQL_DNSSEC="yes" \
    PDNSCONF_API_KEY="" \
    SECALLZONES_CRONJOB="no"

ADD pdns.list /etc/apt/sources.list.d/pdns.list
ADD pdns.preference /etc/apt/preferences.d/pdns 

ENV CLEANIMAGE_VERSION master
ENV CLEANIMAGE_URL https://raw.githubusercontent.com/mrsrvman/docker-cleanimage/$CLEANIMAGE_VERSION/cleanimage

ADD ["$CLEANIMAGE_URL", "/usr/local/bin/"]
RUN chmod +x "/usr/local/bin/cleanimage"

RUN apt-get update && DEBIAN_FRONTEND=noninteractive apt-get install -q -y curl gnupg && \
    curl https://repo.powerdns.com/FD380FBB-pub.asc | apt-key add - && apt-get update && \
    cleanimage

RUN DEBIAN_FRONTEND=noninteractive apt-get install -q -y pdns-server pdns-backend-mysql mysql-client && \
    rm /etc/powerdns/pdns.d/*.conf && rm /etc/powerdns/*.conf && \
    DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends cron jq && \
    rm /etc/cron.daily/* && \
    cleanimage

EXPOSE 53/udp 53/tcp

ADD start.sh /usr/local/bin/start.sh
ADD fixdsrrs.sh /usr/local/bin/fixdsrrs.sh
ADD secallzones.sh /usr/local/bin/secallzones.sh
ADD schema.mysql.sql /usr/share/doc/pdns-backend-mysql/
RUN chmod a+x /usr/local/bin/*.sh

CMD ["/usr/local/bin/start.sh"]
