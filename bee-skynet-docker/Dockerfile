FROM gcc:4.9.2

ARG TARGET_DIR=/app


COPY hiredis-1.0.0.tar.gz ${TARGET_DIR}/hiredis-1.0.0.tar.gz
COPY boost_1_55_0.tar.gz ${TARGET_DIR}/boost_1_55_0.tar.gz
# COPY mongocxx31.tar.gz ${TARGET_DIR}/mongocxx31.tar.gz
# COPY mongoc17.tar.gz ${TARGET_DIR}/mongoc17.tar.gz
# COPY protobuf34.tar.gz ${TARGET_DIR}/protobuf34.tar.gz


RUN cd ${TARGET_DIR} && tar -zxf hiredis-1.0.0.tar.gz && cd hiredis-1.0.0 && make && make install && \
    # mongoc17 mongocxx31 protobuf34 start
    # cd ${TARGET_DIR} && \
    # tar -zxf mongocxx31.tar.gz &&  mv -f mongocxx31 /usr/local/ && \
    # tar -zxf mongoc17.tar.gz && mv -f mongoc17 /usr/local/ && \
    # tar -zxf protobuf34.tar.gz && mv -f protobuf34 /usr/local/ && \
    # install boost start
    # cd ${TARGET_DIR} && tar -zxf boost_1_55_0.tar.gz && cd boost_1_55_0 && ./bootstrap.sh --prefix=/usr/ && ./b2 install && \
    # install glog start  todo!!!!!!
    # clean
    # rm -rf ${TARGET_DIR} && \
    apt-get clean && apt-get autoclean && apt-get autoremove && \
    rm -rf /tmp/* && \
    echo 'finish'