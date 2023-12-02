FROM ubuntu:22.04
LABEL maintainer="Benjamin Perseghetti <bperseghetti@rudislabs.com>"

# environment
ENV XDG_RUNTIME_DIR=/tmp/runtime-docker
ENV TERM=xterm-256color
ENV PATH="/home/user/bin:${PATH}"
ENV TZ=America/New_York

# Set default shell during Docker image build to bash
SHELL ["/bin/bash", "-l", "-c"]

RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install base packages
RUN DEBIAN_FRONTEND=noninteractive apt-get -y update && \
	apt-get -y upgrade && \
	apt-get install --no-install-recommends -y \
		sudo \
		bash-completion \
		build-essential \
		chrpath \
		cpio \
		debianutils \
		dfu-util \
		diffstat \
		file \
		gawk \
		gcc-multilib \
		git-core \
		iputils-ping \
		libbz2-dev \
		libusb-1.0.0-dev \
		libz-dev \
		python3 \
		python3-pip \
		python3-pexpect \
		python-is-python3 \
		socat \
		texinfo \
		unzip \
		xz-utils \
		libgnutls28-dev \
		efitools \
		git-lfs

RUN DEBIAN_FRONTEND=noninteractive apt-get -y update && \
	apt-get -y upgrade && \
	apt-get install --no-install-recommends -y \
		vim \
		wget \
		xterm \
		zstd \
		lzop \
		net-tools \
		bc \
		curl \
		git \
		liblz4-tool \
		libsdl1.2-dev \
		libssl-dev \
		rsync \
		tar \
		locales \
		ssh

RUN DEBIAN_FRONTEND=noninteractive locale-gen en_US.UTF-8 && \
    DEBIAN_FRONTEND=noninteractive update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8
ENV LANG en_US.UTF-8
ENV LC_ALL en_US.UTF-8


# Create a user to make sure install works without root
ARG UID_INSTALLER=2001
RUN useradd -l -u $UID_INSTALLER installer -G sudo,plugdev && \
 echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

USER installer

# add groups before we do anything that might add a new group
ARG GID_INPUT=107
RUN sudo groupadd -r -g $GID_INPUT input

# enable apt auto-completion by deleting autoclean task
RUN sudo rm /etc/apt/apt.conf.d/docker-clean

# create XDG runtime dir
RUN mkdir /tmp/runtime-docker && sudo chmod 700 /tmp/runtime-docker

# create a user for running the container
ARG UID_USER=1000
RUN sudo useradd --create-home -l -u $UID_USER -G sudo,plugdev,input user && \
 echo user: $UID_USER

USER user
# create ws, this is where the source code will be mounted

VOLUME /home/user/work
WORKDIR /home/user/work
RUN mkdir -p /home/user/work

# setup entry point
COPY install/entrypoint.sh /
RUN sudo chmod +x /entrypoint.sh
RUN sudo chsh -s /bin/bash user
RUN mkdir -p /home/user/bin && \
	curl https://storage.googleapis.com/git-repo-downloads/repo > /home/user/bin/repo && \
	chmod a+x /home/user/bin/repo

CMD ["/bin/bash"]
ENTRYPOINT ["/entrypoint.sh"]

LABEL org.opencontainers.image.source = "https://github.com/rudislabs/navqplus-image-docker"

# vim: set et fenc=utf-8 ff=unix ft=dockerfile sts=0 sw=2 ts=2 :
