FROM debian:jessie

ENV GN_FILE geonetwork.war
ENV DATA_DIR=/geonetwork-data
ENV JAVA_OPTS="-Djava.security.egd=file:/dev/./urandom -Djava.awt.headless=true -server -Xms512m -Xmx2024m"
ENV CATALINA_HOME /opt/tomcat7
ENV PATH $CATALINA_HOME/bin:$PATH

#Environment variables
ENV GN_VERSION 2.10.3
ENV GN_DOWNLOAD_MD5 6bcdebb552867ffeec585bee0179b8e0
ENV TOMCAT_VERSION 7.0.104
ENV TOMCAT_DOWNLOAD_SHA512=d14c4dd0b9f360030805ed6f59dacb212b86c90bcbc4e381364001cf97b12b581c880bfd8764cf12913864c825164b10d654752d20700d029f207b82f5bcd858

RUN apt-get update && apt-get install -yq unzip curl openjdk-7-jre-headless && apt-get clean && \
     apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false && \
     rm -rf /var/lib/apt/lists/*

RUN curl -fSL -o tomcat7.tar.gz \
          https://www-eu.apache.org/dist/tomcat/tomcat-7/v${TOMCAT_VERSION}/bin/apache-tomcat-${TOMCAT_VERSION}.tar.gz && \
     echo "$TOMCAT_DOWNLOAD_SHA512 *tomcat7.tar.gz" | sha512sum -c && \
     mkdir /opt/tomcat7 && \
     tar -xvzf tomcat7.tar.gz -C /opt/tomcat7 --strip-components=1 && \
     rm tomcat7.tar.gz && \
     curl -fSL -o $GN_FILE \
          https://sourceforge.net/projects/geonetwork/files/GeoNetwork_opensource/v${GN_VERSION}/geonetwork.war/download && \
     echo "$GN_DOWNLOAD_MD5 *$GN_FILE" | md5sum -c && \
     mkdir -p geonetwork && \
     unzip -e $GN_FILE -d /opt/tomcat7/webapps/geonetwork && \
     rm $GN_FILE && \
     groupadd tomcat7 && \
     useradd -s /bin/false -g tomcat7 -d /opt/tomcat7 tomcat7 && \
     chgrp -R tomcat7 /opt/tomcat7 && \
     cd /opt/tomcat7 && \
     chmod -R g+r conf && \
     chmod g+x conf && \
     chown -R tomcat7 webapps/ work/ temp/ logs/ && \
     mkdir $DATA_DIR && \
     chown -R tomcat7:tomcat7 $DATA_DIR && \
     chown -R tomcat7:tomcat7 /opt/tomcat7/webapps/geonetwork && \
     chown tomcat7:tomcat7 /opt/tomcat7




WORKDIR /opt/tomcat7
USER tomcat7
#Set geonetwork data dir
COPY ./docker-entrypoint.sh /entrypoint.sh
VOLUME [ "/opt/tomcat7/webapps/geonetwork/WEB-INF/db/data/", "${DATA_DIR}" ]

ENTRYPOINT ["/entrypoint.sh"]

CMD ["catalina.sh", "run"]