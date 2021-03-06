FROM ubuntu:14.04
MAINTAINER hajma "tropikhajma@gmail.com"

ENV DEBIAN_FRONTEND noninteractive

# Grab all dependencies
RUN apt-get update && \
    apt-get -y upgrade && \
    apt-get -y install curl libx11-dev libxtst-dev libxcomposite-dev \
                       libxdamage-dev libxkbfile-dev python-all-dev \
                       python-gobject-dev python-gtk2-dev cython \
                       xvfb xauth x11-xkb-utils libx264-dev libvpx-dev \
                       libswscale-dev libavcodec-dev subversion websockify curl

# Grab sources
WORKDIR /usr/src
RUN svn co -r 8666 https://www.xpra.org/svn/Xpra/

# Build from sources
RUN cd Xpra/trunk/src && \
    python ./setup.py --without-csc_swscale install && \
    cd rencode && \
    python ./setup.py install && \
    curl https://bitbucket.org/pypa/setuptools/raw/bootstrap/ez_setup.py | sudo python

# Get our executable
RUN apt-get -y install thunderbird

# Declare ports. A real application would probably use the only one not listed: SSH

# expose raw TCP port
EXPOSE 10000

# Create a dedicated user
RUN useradd xpra --password '*' --create-home
USER xpra
ENV HOME /home/xpra

# Run! Application will be served both as WebSocket and Raw TCP. (+ disable all unsupported bits)
CMD ["xpra", "start", ":10", "--start-child=thunderbird", \
    "--exit-with-children", "--daemon=off", "--bind-tcp=0.0.0.0:10000", \
    "--no-mdns", "--no-notifications", "--no-pulseaudio"]
