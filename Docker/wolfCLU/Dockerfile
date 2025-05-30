ARG DOCKER_BASE_IMAGE=ubuntu
FROM ubuntu AS builder

ARG DEPS_WOLFSSL="build-essential autoconf libtool zlib1g-dev libuv1-dev libpam0g-dev git libpcap-dev libcurl4-openssl-dev bsdmainutils netcat-traditional iputils-ping bubblewrap"
RUN DEBIAN_FRONTEND=noninteractive apt update && apt install -y apt-utils \
                    && apt install -y ${DEPS_WOLFSSL} \
                    && apt clean -y && rm -rf /var/lib/apt/lists/*

ARG NUM_CPU=16

# This arg is to force a rebuild starting from this line
ARG DUMMY=date
# install wolfssl
RUN DUMMY=${DUMMY} git clone --depth=1 --single-branch --branch=master http://github.com/wolfssl/wolfssl && cd wolfssl && ./autogen.sh && ./configure --enable-all && make -j $NUM_CPU && make install && ldconfig

# install wolfCLU
RUN git clone --depth=1 --single-branch --branch=main http://github.com/wolfssl/wolfCLU && cd wolfCLU && ./autogen.sh && ./configure && make -j $NUM_CPU && make install

FROM ${DOCKER_BASE_IMAGE}
USER root
COPY --from=builder /usr/local/lib/libwolfssl.so /usr/local/lib/
COPY --from=builder /usr/local/bin/wolfssl* /usr/local/bin/
RUN ldconfig
ENTRYPOINT ["/usr/local/bin/wolfssl"]
LABEL org.opencontainers.image.source=https://github.com/wolfssl/wolfssl
LABEL org.opencontainers.image.description="Simple wolfCLU in a container"
