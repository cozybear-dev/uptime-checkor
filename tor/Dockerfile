#Credits; https://github.com/reverse-hash/bitcoin-full-node-with-docker/blob/main/tor/Dockerfile
#TOR_VERSION=$(curl -s https://gitlab.torproject.org/tpo/applications/tor-browser-build/-/raw/maint-12.0/projects/browser/Bundle-Data/Docs/ChangeLog.txt | grep 'Tor Browser [0-9][0-9].[0-9].' | head -1 | grep -oP '(?<=Tor Browser ).*(?=-)' | xargs) \
FROM alpine:latest AS build
ARG TOR_VERSION
ARG TOR_PLATFORM
RUN apk add --update --no-cache dirmngr gpg gpg-agent wget \
  && cd /tmp \
  && wget https://www.torproject.org/dist/torbrowser/${TOR_VERSION}/tor-expert-bundle-${TOR_VERSION}-${TOR_PLATFORM}.tar.gz.asc \
  && wget https://www.torproject.org/dist/torbrowser/${TOR_VERSION}/tor-expert-bundle-${TOR_VERSION}-${TOR_PLATFORM}.tar.gz \
  && gpg --auto-key-locate nodefault,wkd --locate-keys torbrowser@torproject.org \
  && gpg --verify tor-expert-bundle-${TOR_VERSION}-${TOR_PLATFORM}.tar.gz.asc \
  && if [ "1" -lt $(gpg --verify SHA256SUMS.asc 2>&1 >/dev/null | grep 'Good Signature' -c) ]; then exit 1; fi \
  && tar -xzf *.tar.gz -C . \
  && rm -rf tor/debug tor/data tor/pluggable_transports *.tar.gz \
  && chmod -R 755 tor \
  && apk del dirmngr gpg gpg-agent wget


FROM debian:stable-slim
COPY --from=build /tmp/tor /opt/tor
ENV PATH=$PATH:/opt/tor
ENV LD_LIBRARY_PATH=$LD_LIBRARY_PATH:/opt/tor

RUN adduser --disabled-password tor

USER tor

COPY --chown=tor:tor ./torrc.default /home/tor/torrc.default

CMD tor -f ~/torrc.default