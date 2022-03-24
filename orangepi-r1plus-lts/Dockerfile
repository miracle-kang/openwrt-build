FROM ubuntu:latest

ENV DEBIAN_FRONTEND=noninteractive
ENV TZ=Asia/Shanghai

# Add dependencies
RUN apt update -y && \
    apt install -y sudo build-essential ccache ecj fastjar file g++ gawk \
      gettext git java-propose-classpath libelf-dev libncurses5-dev \
      libncursesw5-dev libssl-dev python python2.7-dev python3 unzip wget \
      python3-distutils python3-setuptools python3-dev rsync subversion \
      swig time xsltproc zlib1g-dev upx curl xz-utils && \
    apt -y autoremove --purge && \
    apt clean && \
    useradd -m user && \
    echo 'user ALL=NOPASSWD: ALL' > /etc/sudoers.d/user

USER user
WORKDIR /home/user

# OpenWRT build root
RUN git clone --depth 1 https://github.com/orangepi-xunlong/openwrt.git -b openwrt-21.02 openwrt

# Update the feeds
RUN cd openwrt && ./scripts/feeds update -a \
  && ./scripts/feeds install -a \
  && ./scripts/feeds install -a

# Import external feeds
RUN cd openwrt && \
  git clone https://github.com/kuoruan/openwrt-v2ray.git package/v2ray-core

# Configuration Customization
COPY --chown=user .config openwrt/.config
COPY --chown=user customize.sh customize.sh
RUN chmod +x ./customize.sh && ./customize.sh \
  && cd openwrt && make defconfig

# Download package
RUN cd openwrt \
  && make download -j$(nproc) \
  && find dl -size -1024c -exec ls -l {} \; \
  && find dl -size -1024c -exec rm -f {} \;

# Post Package
COPY --chown=user ./build.sh openwrt/
RUN chmod +x openwrt/build.sh
VOLUME /home/user/openwrt/bin/
