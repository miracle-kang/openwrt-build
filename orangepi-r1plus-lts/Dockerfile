FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

# Add dependencies
RUN apt-get update -y && \
    apt-get install -y build-essential asciidoc binutils bzip2 gawk gettext git \
      libncurses5-dev patch python3 python2.7 unzip zlib1g-dev lib32gcc1 \
      libc6-dev-i386 subversion flex node-uglify git gcc-multilib p7zip \
      p7zip-full msmtp libssl-dev texinfo libglib2.0-dev xmlto qemu-utils \
      upx-ucl libelf-dev autoconf automake libtool autopoint device-tree-compiler \
      g++-multilib antlr3 gperf wget curl swig rsync coreutils gcc-8 gcc++-8 \
      gcc-8-multilib libreadline-dev ccache curl wget vim nano python python3 \
      python-pip python3-pip python-ply python3-ply haveged lrzsz scons ecj \
      fastjar re2c xz-utils tar && \
    apt-get -y autoremove purge && \
    apt-get clean

# OpenWRT build root
RUN git clone --depth 1 https://github.com/orangepi-xunlong/openwrt.git -b openwrt-21.02 openwrt \

# Update the feeds
RUN cd openwrt && ./scripts/feeds update -a \
  && ./scripts/feeds install -a \
  && ./scripts/feeds install -a

# Import external feeds
RUN cd openwrt && \
  git clone https://github.com/kuoruan/openwrt-v2ray.git package/v2ray-core

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
