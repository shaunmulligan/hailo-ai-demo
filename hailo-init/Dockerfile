# build for images at shaunmulligan995/rpi5-ai-kit

FROM python:3.11.9-slim-bookworm

WORKDIR /root

# Install generic requirements
RUN apt-get update -qq && \
    apt-get install -y --no-install-recommends \
    software-properties-common \
    wget \
    kmod \ 
    dirmngr \
    gnupg \
    supervisor

# Need to create a sources.list file for apt-add-repository to work correctly:
# https://groups.google.com/g/linux.debian.bugs.dist/c/6gM_eBs4LgE
RUN echo "# See sources.lists.d directory" > /etc/apt/sources.list

# Add Raspberry Pi repository, as this is where we will get the Hailo deb packages
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-keys 82B129927FA3303E && \
    apt-add-repository -y -S deb http://archive.raspberrypi.com/debian/ bookworm main

# Fake systemd so hailoRT will install in container:
RUN echo '#!/bin/sh\nexec "$@"' > /usr/bin/sudo && chmod +x /usr/bin/sudo
RUN echo '#!/bin/bash\nexit 0' > /usr/bin/systemctl && chmod +x /usr/bin/systemctl
RUN mkdir -p /run/systemd && echo 'docker' > /run/systemd/container

# We specifically target 4.20.0, other versions should work with the kernel driver but that is untested.
RUN apt-get install -y --no-install-recommends hailo-all=4.20.0

COPY hailort_service_wrapper /usr/local/bin/hailort_service_wrapper
RUN chmod +x /usr/local/bin/hailort_service_wrapper

COPY hailort_service /etc/default/hailort_service

# Copy files
COPY startup.sh /usr/local/bin/startup.sh
COPY supervisord.conf /etc/supervisor/supervisord.conf

# Make startup.sh executable
RUN chmod +x /usr/local/bin/startup.sh

COPY models/ /root/models

# Set our ENTRYPOINT that ensures `/dev/hailo0` gets created
ENTRYPOINT ["/usr/local/bin/startup.sh"]

# CMD now starts supervisord
CMD ["supervisord", "-c", "/etc/supervisor/supervisord.conf"]

#CMD ["bash", "-c", "while true; do echo 'idle...'; sleep 30; done"]