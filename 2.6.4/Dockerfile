FROM debian:jessie

ENV GN_FILE geonetwork.war
ENV DATA_DIR=/geonetwork-data
ENV JAVA_OPTS="-Djava.security.egd=file:/dev/./urandom -Djava.awt.headless=true -server -Xms512m -Xmx2024m"

#Environment variables
ENV GN_VERSION 2.6.4
ENV GN_DOWNLOAD_MD5 ca37a5d6aac22c5a86aae99eb87797e3
ENV TOMCAT_DOWNLOAD_SHA512=a7ab7627ebafab8c5e5b1b09cff3a6e760f45dbc9b9ab235e14532357e47cf3bb6bbcfbdd92cd94561fe471296fd734d221ba8d59e1216a8369d2c39b1a80c98

RUN apt-get update && apt-get install -yq unzip curl openjdk-7-jre-headless && apt-get clean

RUN curl -fSL -o tomcat7.tar.gz \
     https://www-eu.apache.org/dist/tomcat/tomcat-7/v7.0.96/bin/apache-tomcat-7.0.96.tar.gz && \
     echo "$TOMCAT_DOWNLOAD_SHA512 *tomcat7.tar.gz" | sha512sum -c && \
     mkdir /opt/tomcat7 && \
     tar -xvzf tomcat7.tar.gz -C /opt/tomcat7 --strip-components=1 && \
     rm tomcat7.tar.gz


RUN curl -fSL -o $GN_FILE \
     https://sourceforge.net/projects/geonetwork/files/GeoNetwork_opensource/v${GN_VERSION}/geonetwork.war/download && \
     echo "$GN_DOWNLOAD_MD5 *$GN_FILE" | md5sum -c && \
     mkdir -p geonetwork && \
     unzip -e $GN_FILE -d ~/geonetwork && \
     rm $GN_FILE

RUN groupadd tomcat7 && \
     useradd -s /bin/false -g tomcat7 -d /opt/tomcat7 tomcat7 && \
     chgrp -R tomcat7 /opt/tomcat7 && \
     cd /opt/tomcat7 && \
     chmod -R g+r conf && \
     chmod g+x conf && \
     chown -R tomcat7 webapps/ work/ temp/ logs/ && \
     mv ~/geonetwork /opt/tomcat7/webapps/ && \
     mkdir $DATA_DIR && \
     chown -R tomcat7:tomcat7 $DATA_DIR && \
     chown -R tomcat7:tomcat7 /opt/tomcat7/webapps/geonetwork



WORKDIR /opt/tomcat7
USER tomcat7
#Set geonetwork data dir
COPY ./docker-entrypoint.sh /entrypoint.sh
VOLUME [ "/opt/tomcat7/webapps/geonetwork/WEB-INF/db/data/", "${DATA_DIR}" ]

ENTRYPOINT ["/entrypoint.sh"]

CMD ["bin/catalina.sh", "run"]