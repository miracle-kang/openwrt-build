FROM alpine:latest

ARG NPROC
ENV NPROC=${NPROC:-$(nproc)}

# Add dependencies
RUN apk add asciidoc bash bc binutils bzip2 cdrkit coreutils diffutils \
findutils flex g++ gawk gcc gettext git grep intltool libxslt \
linux-headers make ncurses-dev openssl-dev patch perl python2-dev \
python3-dev rsync tar unzip util-linux wget zlib-dev

# OpenWRT build root
RUN git clone https://github.com/orangepi-xunlong/openwrt.git \
  && cd openwrt && git pull \
  && git checkout openwrt-21.02 \
  && git clone https://github.com/kuoruan/openwrt-v2ray.git package/v2ray-core

# Update the feeds
RUN cd openwrt && ./scripts/feeds update -a \
  && ./scripts/feeds install -a \
  && ./scripts/feeds install -a

# Configuration Customization
COPY .config openwrt/.config
COPY ./customize.sh /
RUN chmod +x ./customize.sh && ./customize.sh \
  && cd openwrt && make defconfig

# Download package
RUN cd openwrt \
  && make download -j${NPROC} \
  && find dl -size -1024c -exec ls -l {} \; \
  && find dl -size -1024c -exec rm -f {} \;

# Post Package
COPY ./build.sh /openwrt/
RUN chmod +x /openwrt/build.sh
VOLUME /openwrt/bin/
WORKDIR /openwrt