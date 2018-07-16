FROM registry.access.redhat.com/rhscl/php-71-rhel7

USER root

ENV PHP_VERSION=7.1 \
    PHP_VER_SHORT=71 \
    NAME=php-oci8 \
    ORACLE_HOME=/opt/app-root/oracle/instantclient_12_2 \
    LD_LIBRARY_PATH=/opt/app-root/oracle/instantclient_12_2 

LABEL io.k8s.display-name="Apache 2.4 with PHP ${PHP_VERSION} and OCI8" \
      io.openshift.expose-services="8080:http" \
      io.openshift.tags="builder,${NAME},${NAME}${PHP_VER_SHORT},rh-${NAME}${PHP_VER_SHORT}" \
      name="rhscl/${NAME}-${PHP_VER_SHORT}-rhel7" 

COPY ./oracle /opt/app-root/oracle/

RUN yum-config-manager --disable rhel-7-server-htb-rpms && \
    yum install -y yum-utils && \
    prepare-yum-repositories rhel-server-rhscl-7-rpms && \
    INSTALL_PKGS="rh-php71-php-devel libaio" && \
    yum install -y --setopt=tsflags=nodocs $INSTALL_PKGS && \
    rpm -V $INSTALL_PKGS && \
    yum clean all -y && \
    cd /opt/app-root/oracle && \
    unzip -q instantclient-sdk-linux.x64-12.2.0.1.0.zip && \
    unzip -q instantclient-basic-linux.x64-12.2.0.1.0.zip && \
    rm *.zip  && \
    cd /opt/app-root/oracle/instantclient_12_2 && \
    ln -s libclntsh.so.12.1 libclntsh.so && \
    ln -s libclntshcore.so.12.1 libclntshcore.so && \
    ln -s libocci.so.12.1 libocci.so && \
    cd .. && \
    pecl bundle oci8 && \
    cd oci8 && phpize && ./configure --with-oci8=instantclient,$ORACLE_HOME && \
    make install && \
    mkdir /opt/app-root/php.d && \
    cp /opt/app-root/oracle/oci8/modules/* /opt/rh/rh-php71/root/usr/lib64/php/modules/

COPY oci8.ini /etc/opt/rh/rh-php71/php.d
    

USER 1001
