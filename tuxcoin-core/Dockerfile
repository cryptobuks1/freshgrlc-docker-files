FROM debian:sid

RUN groupadd -r tuxcoind && useradd -r -g tuxcoind -d /var/lib/tuxcoind tuxcoind
RUN install -d -m 0700 -o tuxcoind -g tuxcoind /var/lib/tuxcoind

RUN apt-get update \
    && apt-get install -y --no-install-recommends \
        ca-certificates curl build-essential pkg-config \
        automake autoconf libtool git \
        gpg gpg-agent dirmngr \
        libssl-dev libevent-dev \
        libboost-dev libboost-system-dev \
        libboost-thread-dev libboost-filesystem-dev \
        libboost-program-options-dev \
    && rm -rf /var/lib/apt/lists/*

RUN curl -o /usr/local/bin/gosu -SL "https://github.com/tianon/gosu/releases/download/1.6/gosu-$(dpkg --print-architecture)" \
    && curl -o /usr/local/bin/gosu.asc -SL "https://github.com/tianon/gosu/releases/download/1.6/gosu-$(dpkg --print-architecture).asc" \
    && mkdir ~/.gnupg && echo "disable-ipv6" >> ~/.gnupg/dirmngr.conf \
    && gpg --batch --keyserver ha.pool.sks-keyservers.net --recv-keys B42F6819007F00F88E364FD4036A9C25BF357DD4 \
    && gpg --verify /usr/local/bin/gosu.asc \
    && rm /usr/local/bin/gosu.asc \
    && chmod +x /usr/local/bin/gosu

RUN cd /tmp \
    && git clone 'https://github.com/bleach86/tuxcoin-V2' tuxcoin-V2-2.0 \
    && cd tuxcoin-V2-2.0 \
    && git checkout c5011576cc746a5d86bb6d0a0a0cd7f6b47c1959

COPY increase-blocksize-reservations.patch /tmp/increase-blocksize-reservations.patch
COPY increase-max-connections.patch /tmp/increase-max-connections.patch
COPY relayinfo.patch /tmp/relayinfo.patch

RUN cd /tmp/tuxcoin-V2-2.0 \
    && patch -p1 < /tmp/increase-blocksize-reservations.patch \
    && patch -p1 < /tmp/increase-max-connections.patch \
    && patch -p1 < /tmp/relayinfo.patch \
    && rm -f /tmp/*.patch

RUN cd /tmp/tuxcoin-V2-2.0 \
    && autoreconf --install --force --warnings=all \
    && ./configure --disable-tests --disable-bench --disable-zmq --disable-wallet \
    && make -j16 \
    && make install \
    && cd / \
    && rm -Rf /tmp/tuxcoin-V2-2.0

CMD [ "tuxcoind", "-nodaemon", "-printtoconsole" ]

COPY entrypoint.sh /entrypoint.sh
ENTRYPOINT [ "/entrypoint.sh" ]

VOLUME /var/lib/tuxcoind

EXPOSE 42071 42072

HEALTHCHECK CMD su - tuxcoind -c 'tuxcoin-cli getblocktemplate'
