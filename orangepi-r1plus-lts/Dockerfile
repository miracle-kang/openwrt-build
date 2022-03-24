FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

# Add dependencies
RUN apt update -y && \
    apt install -y build-essential ccache ecj fastjar file g++ gawk \
        gettext git java-propose-classpath libelf-dev libncurses5-dev \
        libncursesw5-dev libssl-dev python python2.7-dev python3 unzip wget \
        python3-distutils python3-setuptools python3-dev rsync subversion \
        swig time xsltproc zlib1g-dev 

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
  && make download -j$(nproc) \
  && find dl -size -1024c -exec ls -l {} \; \
  && find dl -size -1024c -exec rm -f {} \;

# Post Package
COPY ./build.sh /openwrt/
RUN chmod +x /openwrt/build.sh
VOLUME /openwrt/bin/
WORKDIR /openwrt
