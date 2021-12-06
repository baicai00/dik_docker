FROM centos:7

WORKDIR /root

ENV PATH ${PATH}:/usr/local/tars/cpp/tools

RUN yum install -y glibc-devel gcc gcc-c++ bison flex which psmisc ncurses-devel zlib-devel vim wget curl automake autoconf libtool make git openssl-devel net-tools bzip2 && \
    mkdir -p /tmp/build && \
    # install cmake
    cd /tmp/build && \
    wget https://cmake.org/files/v3.21/cmake-3.21.0-linux-x86_64.tar.gz && \
    tar -zxf cmake-3.21.0-linux-x86_64.tar.gz -C /usr/local && \
    cd /usr/bin && \
    rm -f ccmake && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/ccmake ccmake && \
    rm -f cmake && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/cmake cmake && \
    rm -f cmake-gui && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/cmake-gui cmake-gui && \
    rm -f cpack && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/cpack cpack && \
    rm -f ctest && ln -s /usr/local/cmake-3.21.0-linux-x86_64/bin/ctest ctest && \
    # install tars-cpp
    cd /tmp/build && \
    git clone https://github.com/TarsCloud/TarsCpp.git --recursive && \
    cd TarsCpp && cmake . && make && make install && \
    # clean
    cd / && rm -rf /tmp/build && yum clean all && \
    echo 'Install Finish'

COPY entrypoint.sh /sbin/
RUN chmod 755 /sbin/entrypoint.sh

ENTRYPOINT ["/sbin/entrypoint.sh"]