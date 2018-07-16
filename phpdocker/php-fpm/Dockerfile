FROM phpdockerio/php72-fpm:latest
WORKDIR "/application"

# Install selected extensions and other stuff
RUN apt-get update \
    && apt-get -y --no-install-recommends install  php-memcached php7.2-mysql php7.2-pgsql php-redis php7.2-sqlite3 php-xdebug php7.2-bcmath php7.2-bz2 php7.2-dba php7.2-enchant php7.2-gd php7.2-gmp php-igbinary php-imagick php7.2-imap php7.2-interbase php7.2-intl php7.2-ldap php-mongodb php-msgpack php7.2-odbc php7.2-phpdbg php7.2-pspell php-raphf php7.2-recode php7.2-snmp php7.2-soap php-ssh2 php7.2-sybase php-tideways php7.2-tidy php7.2-xmlrpc php7.2-xsl php-yaml php-zmq \
    && apt-get clean; rm -rf /var/lib/apt/lists/* /tmp/* /var/tmp/* /usr/share/doc/* \
    && apt-get install cairo \
       cups-libs \
       fontconfig \
       java-1.8.0-openjdk \
       libICE \
       libSM \
       libXext \
       libXinerama \
       libXrender \
       mesa-libGLU \
       patch \
       rsync \
       supervisor

# install alfresco
COPY assets/install_alfresco.sh /tmp/install_alfresco.sh
RUN /tmp/install_alfresco.sh && \
    rm -rf /tmp/* /var/tmp/*
# install mysql connector for alfresco
COPY assets/install_mysql_connector.sh /tmp/install_mysql_connector.sh
RUN /tmp/install_mysql_connector.sh && \
    rm -rf /tmp/* /var/tmp/*
# this is for LDAP configuration
RUN mkdir -p /alfresco/tomcat/shared/classes/alfresco/extension/subsystems/Authentication/ldap/ldap1/
RUN mkdir -p /alfresco/tomcat/shared/classes/alfresco/extension/subsystems/Authentication/ldap-ad/ldap1/
COPY assets/ldap-authentication.properties /alfresco/tomcat/shared/classes/alfresco/extension/subsystems/Authentication/ldap/ldap1/ldap-authentication.properties
COPY assets/ldap-ad-authentication.properties /alfresco/tomcat/shared/classes/alfresco/extension/subsystems/Authentication/ldap-ad/ldap1/ldap-ad-authentication.properties

# backup alf_data so that it can be used in init.sh if necessary
ENV ALF_DATA /alfresco/alf_data
RUN rsync -av $ALF_DATA /alf_data.install/

# adding path file used to disable tomcat CSRF
COPY assets/disable_tomcat_CSRF.patch /alfresco/disable_tomcat_CSRF.patch

# install scripts
COPY assets/init.sh /alfresco/init.sh
COPY assets/supervisord.conf /etc/supervisord.conf

RUN mkdir -p /alfresco/tomcat/webapps/ROOT
COPY assets/index.jsp /alfresco/tomcat/webapps/ROOT/

VOLUME /alfresco/alf_data
VOLUME /alfresco/tomcat/logs
VOLUME /content

EXPOSE 21 137 138 139 445 7070 8009 8080
CMD /usr/bin/supervisord -c /etc/supervisord.conf -n
