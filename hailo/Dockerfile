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
RUN apt-get install -y --no-install-recommends hailo-dkms python3-hailort

# Copy our wrapper script that sets up hailo_service process
COPY hailort_service_wrapper /usr/local/bin/hailort_service_wrapper
RUN chmod +x /usr/local/bin/hailort_service_wrapper

# Copy in the startup script that runs from ENTRYPOINT
COPY startup /usr/local/bin/startup
# Make startup executable
RUN chmod +x /usr/local/bin/startup

# Make sure supervisord has the config to start all the specific scripts
COPY supervisord.conf /etc/supervisor/supervisord.conf

############ Replace these with real inference code ######################

# Copy our AI models into docker context for `test_hailo_infer` to use.
COPY models/ /root/models

# Copy in our test inference script (Replace with real code)
COPY test_hailo_infer /usr/local/bin/test_hailo_infer
RUN chmod +x /usr/local/bin/test_hailo_infer

############ Replace these with real inference code ######################

# Set our ENTRYPOINT that ensures `/dev/hailo0` gets created
ENTRYPOINT ["/usr/local/bin/startup"]

# CMD now starts supervisord
CMD ["supervisord", "-c", "/etc/supervisor/supervisord.conf"]


#CMD ["bash", "-c", "while true; do echo 'idle...'; sleep 30; done"]