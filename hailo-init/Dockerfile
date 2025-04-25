# build for images at shaunmulligan995/rpi5-ai-kit

FROM balenalib/raspberrypi5-debian:bookworm-build

WORKDIR /root

# Install generic requirements
RUN install_packages software-properties-common wget

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

# We need to specifically target 4.20.0 as the OS driver is 4.20.1, any other version will break.
RUN install_packages hailo-all=4.20.0

COPY startup.sh startup.sh
RUN chmod +x startup.sh

CMD [ "./startup.sh" ]