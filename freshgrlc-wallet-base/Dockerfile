FROM python:2

RUN pip install --no-cache-dir cachetools Flask Flask-Cors gevent greenlet PyMySQL python-bitcoinrpc requests SQLAlchemy uwsgi pycrypto base58 pycryptodome

RUN cd /usr/src \
    && git clone 'https://github.com/freshgrlc/freshgrlc-wallet' \
    && cd /usr/src/freshgrlc-wallet \
    && git submodule update --init -f

WORKDIR /usr/src/freshgrlc-wallet
ENV GNUPGHOME=/usr/src/freshgrlc-wallet/.gnupg

RUN gpg --keyserver hkps://pgp.mit.edu --receive-keys 8B42760F04F73EB62A13D62F2C01995BE64D11CF

ARG TAG=undefined

RUN    git fetch \
    && git verify-tag ${TAG} \
    && git checkout ${TAG} \
    && git submodule update --init -f

COPY indexer-config.py  indexer/config.py
COPY wallet-config.py  config.py
