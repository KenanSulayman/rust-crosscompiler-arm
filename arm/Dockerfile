FROM debian:jessie
MAINTAINER Damien Lecan <dev@dlecan.com>

#ENV USER root
ENV CHANNEL nightly

ENV CC_DIR /opt/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin
ENV REAL_CC $CC_DIR/arm-linux-gnueabihf-gcc
ENV CC arm-linux-gnueabihf-gcc-with-link-search
ENV CXX arm-linux-gnueabihf-g++-with-link-search
ENV PATH $CC_DIR:$PATH:/root/.cargo/bin
ENV ROOT_FS /

COPY include/config /tmp/.cargo/
COPY include/arm-linux-gnueabihf-gcc-with-link-search /usr/local/sbin/
COPY include/arm-linux-gnueabihf-g++-with-link-search /usr/local/sbin/
COPY include/fixQualifiedLibraryPaths.sh /usr/local/sbin/
COPY include/cargo /usr/local/sbin/
COPY include/sources.list /etc/apt/
#COPY include/sources-armhf.list /etc/apt/sources.list.d/

RUN mv /tmp/.cargo $HOME && \
  dpkg --add-architecture armhf && \
  apt-key adv --recv-keys --keyserver keys.gnupg.net 9165938D90FDDD2E && \
  apt-get update
RUN DEBIAN_FRONTEND=noninteractive apt-get install -y --no-install-recommends \
    build-essential \
    ca-certificates \
    file \
    curl \
    libssl-dev \
    libssl-dev:armhf
RUN curl https://sh.rustup.rs -sSf | sh -s -- --default-toolchain nightly -y
#sh /dev/stdin -y

RUN PATH=$PATH:$HOME/.cargo/bin && \
  rustup target add arm-unknown-linux-gnueabihf

RUN curl -sSL https://github.com/raspberrypi/tools/archive/master.tar.gz \
  | tar -zxC /opt tools-master/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64 --strip=2 && \
  fixQualifiedLibraryPaths.sh $ROOT_FS $REAL_CC

RUN DEBIAN_FRONTEND=noninteractive apt-get remove --purge -y curl

RUN DEBIAN_FRONTEND=noninteractive apt-get autoremove -y

RUN rm -rf \
    /var/lib/apt/lists/* \
    /tmp/* \
    /var/tmp/*

RUN mkdir -p /source

VOLUME ["/root/.cargo/git", "/root/.cargo/registry"]

VOLUME ["/source"]
WORKDIR /source

CMD ["cargo", "build", "--release"]

