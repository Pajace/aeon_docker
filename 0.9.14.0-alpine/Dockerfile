FROM alpine:3.2

RUN apk add --no-cache ca-certificates

RUN set -ex \
        && apk add --no-cache --virtual .fetch-deps gnupg tar gzip \
        && wget https://dl.bintray.com/boostorg/release/1.66.0/source/boost_1_66_0.tar.gz  \
        && wget  https://github.com/aeonix/aeon/archive/v0.9.14.0.tar.gz \
        && mkdir -p /usr/src/boost \
        && mkdir -p /usr/src/aeon \
        && tar -xzC /usr/src/boost --strip-components=1 -f boost_1_66_0.tar.gz \
        && tar -xzC /usr/src/aeon --strip-components=1 -f v0.9.14.0.tar.gz \
        && rm boost_1_66_0.tar.gz \
        && rm v0.9.14.0.tar.gz \
        \
        && apk add --no-cache --virtual .build-deps  \
            bzip2-dev \
            coreutils \
            dpkg-dev dpkg \
            expat-dev \
            gcc \
            g++ \
            gdbm-dev \
            libc-dev \
            libffi-dev \
            ncurses-dev \
            pax-utils \
            readline-dev \
            sqlite-dev \
            tcl-dev \
            xz-dev \
            zlib-dev \
            make \
            cmake \
            linux-headers \
# add build deps before removing fetch deps in case there's overlap
        && apk del .fetch-deps \
        \
        && cd /usr/src/boost \
        && ./bootstrap.sh --prefix=/usr/local \
        && ./b2 -j "$(nproc)" \
        && ./b2 install \
# for aeon
        && cd /usr/src/aeon \
        && make -j "$(nproc)" \
        && cp ./build/release/src/aeond /usr/local/bin \
        && cp ./build/release/src/connectivity_tool /usr/local/bin \
        && cp ./build/release/src/simpleminer /usr/local/bin \
        && cp ./build/release/src/simplewallet /usr/local/bin \
        && runDeps="$( \
            scanelf --needed --nobanner --format '%n#p' --recursive /usr/local \
                | tr ',' '\n' \
                | sort -u \
                | awk 'system("[ -e /usr/local/lib/" $1 " ]") == 0 { next } { print "so:" $1 }' \
            )" \
		&& apk add --virtual .aeon-rundeps $runDeps \
		&& apk del .build-deps \
        \
		&& rm -rf /usr/src/aeon /usr/src/boost

WORKDIR /aeon


