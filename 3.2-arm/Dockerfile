FROM balenalib/rpi-raspbian

RUN groupadd -r mongodb \
    && useradd -r -g mongodb mongodb

RUN apt-get update; \
    apt-get install -y gosu; \
    rm -rf /var/lib/apt/lists/*; \
    gosu nobody true

ARG MONGO_VERSION=3.2.22
RUN apt-get update \
    && apt-get purge ca-certificates \
    && apt-get install -y \
       scons \
       wget \
       build-essential \
       ca-certificates \
       libboost-filesystem-dev \
       libboost-program-options-dev \
       libboost-system-dev \
       libboost-thread-dev \
       python-dev \
    && curl -SL https://fastdl.mongodb.org/src/mongodb-src-r$MONGO_VERSION.tar.gz | tar xzv \
    && cd mongodb-src-r$MONGO_VERSION \
    && cd src/third_party/mozjs-38/ \
    && ./get_sources.sh \
    && export SHELL=/bin/bash \
    && ./gen-config.sh arm linux \
    && cd ../../.. \
    && scons mongod \
       --wiredtiger=off \
       --mmapv1=on \
       --disable-warnings-as-errors=DISABLE-WARNINGS-AS-ERRORS \
    && strip -s build/opt/mongo/mongod \
    && cp build/opt/mongo/mongod /usr/local/bin \
    && cd .. \
    && rm -rf mongodb-src-* \
    && apt-get purge \
       scons \
       wget \
       build-essential \
       libboost-filesystem-dev \
       libboost-program-options-dev \
       libboost-system-dev \
       libboost-thread-dev \
       python-dev \
    && rm -rf /var/lib/apt/lists/*

RUN mkdir -p /data/db /data/configdb \
    && chown -R mongodb:mongodb /data/db /data/configdb
VOLUME /data/db /data/configdb

COPY docker-entrypoint.sh /usr/local/bin/
ENTRYPOINT ["docker-entrypoint.sh"]

EXPOSE 27017
CMD ["mongod"]
