FROM centos:7

WORKDIR /root/
ARG FRAMEWORK_TAG=master
ARG WEB_TAG=master

ENV TARS_INSTALL  /usr/local/tars/cpp/deploy

RUN yum install -y glibc-devel gcc gcc-c++ bison flex which psmisc ncurses-devel zlib-devel vim wget curl automake autoconf libtool make git openssl-devel net-tools bzip2
RUN yum clean all && curl --silent --location https://rpm.nodesource.com/setup_14.x | bash && yum install -y nodejs
RUN mkdir -p /tmp/cmake && cd /tmp/cmake && wget https://cmake.org/files/v3.21/cmake-3.21.0-linux-x86_64.tar.gz && \
    tar -zxf cmake-3.21.0-linux-x86_64.tar.gz -C /usr/local && \
    cd /usr/bin && \
    rm -f ccmake && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/ccmake ccmake && \
    rm -f cmake && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/cmake cmake && \
    rm -f cmake-gui && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/cmake-gui cmake-gui && \
    rm -f cpack && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/cpack cpack && \
    rm -f ctest && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/ctest ctest

RUN mkdir -p /tmp/Tars && cd /tmp/Tars && git clone https://github.com/TarsCloud/TarsFramework framework --recursive && cd framework && git checkout $FRAMEWORK_TAG && git submodule update --init --recursive
RUN cd /tmp/Tars && git clone https://github.com/TarsCloud/TarsWeb web && cd web && git checkout $WEB_TAG
RUN mkdir -p /data \
    && chmod u+x /tmp/Tars/framework/build/build.sh \
    && cd /tmp/Tars/framework/build/ \
    && cmake .. && make -j4 && make install \
    && cp -rf /tmp/Tars/web /usr/local/tars/cpp/deploy/ \
    && rm -rf /tmp 

RUN ${TARS_INSTALL}/tar-server.sh

ENTRYPOINT [ "/usr/local/tars/cpp/deploy/docker-init.sh" ]

#web
EXPOSE 3000
#user system
EXPOSE 3001
#tarslog
EXPOSE 18993
#tarspatch
EXPOSE 18793
#tarsqueryproperty
EXPOSE 18693
#tarsconfig
EXPOSE 18193
#tarsnotify
EXPOSE 18593
#tarsproperty
EXPOSE 18493
#tarsquerystat
EXPOSE 18393
#tarsstat
EXPOSE 18293
#tarsAdminRegistry
EXPOSE 12000
#tarsnode
EXPOSE 19385
#tarsregistry
EXPOSE 17890
EXPOSE 17891
