FROM ubuntu:20.04

# Set timezone
ENV TZ=UTC
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Tooling
RUN	DEBIAN_FRONTEND=noninteractive apt-get update && \
	apt-get install -y \
		build-essential \
		devscripts \
		fakeroot \
		git \
		sudo \
		wget && \
	apt-get clean && \
	rm -rf /var/lib/apt/lists/*

# Envs for build description and so on
ENV FLAVOR=debuild OS=ubuntu DIST=focal

COPY buildscript /usr/local/bin
RUN chmod +x /usr/local/bin/buildscript

# add our sudo builder
#RUN useradd builder -m -G staff,sudo && \
#    echo "builder ALL=(ALL:ALL) NOPASSWD:ALL" >> /etc/sudoers && \
#    mkdir -p /home/builder/debuild && \
#    chown -R builder /home/builder
#USER builder
COPY devscripts-config /.devscripts

# No entrypoint as this will be used by jenkins
