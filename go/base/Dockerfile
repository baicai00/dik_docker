FROM centos:7

ENV GOROOT /usr/local/go
ENV GOPATH /root/gopath
ENV PATH ${PATH}:${GOROOT}/bin:${GOPATH}/bin

RUN mkdir -p /tmp/build && \
    yum install -y git wget vim &&\
    # install go1.15.13
    cd /tmp/build && \
    wget https://dl.google.com/go/go1.15.13.linux-amd64.tar.gz && \
    rm -rf /usr/local/go && tar -zxf go1.15.13.linux-amd64.tar.gz -C /usr/local && \
    go env -w GO111MODULE=on && \
    go env -w GOPROXY=https://goproxy.cn && \
    mkdir -p ${GOPATH}/bin && \
    # clean
    cd / && rm -rf /tmp/build && yum clean all && \
    echo 'Install Finish'
