ARG ALPINE_VER=3.10
################################################################################
# Source
################################################################################
FROM arm64v8/alpine:$ALPINE_VER AS source
ENV SHADOWSOCKS_VER=3.3.3
ENV SHADOWSOCKS_REPO=https://github.com/shadowsocks/shadowsocks-libev.git
RUN export DEPS=" \
    autoconf automake pcre-dev asciidoc xmlto mbedtls-dev libsodium-dev c-ares-dev libev-dev \
    git build-base curl libtool linux-headers openssl-dev" \
    && apk add $DEPS
ENV SHADOWSOCKS_DIR=/shadowsocks
RUN mkdir $SHADOWSOCKS_DIR
WORKDIR $SHADOWSOCKS_DIR
RUN export CFLAGS="-march=armv8-a+crc -mtune=cortex-a72 -O2" \
  && git init \
  && git remote add origin $SHADOWSOCKS_REPO \
  && git fetch origin --depth 1 v$SHADOWSOCKS_VER \
  && git reset --hard FETCH_HEAD
RUN git submodule update --init --recursive
RUN ./autogen.sh && ./configure
RUN make && make install


################################################################################
# Runtime
################################################################################
FROM arm64v8/alpine:$ALPINE_VER
RUN export DEPS="bash libev libsodium mbedtls pcre c-ares" \
    && apk add $DEPS
COPY --from=source /usr/local/bin/* /usr/local/bin/
ADD ./entrypoint.sh /

ENTRYPOINT ["/entrypoint.sh"]
