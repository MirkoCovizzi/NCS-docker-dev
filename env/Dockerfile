# Developer Image (zephyr-build)

ARG BASE_IMAGE
FROM ${BASE_IMAGE:-zephyrprojectrtos/ci:latest} as development

# Install packages
RUN apt-get -y update && \
	apt-get -y upgrade && \
	apt-get install --no-install-recommends -y \
    git cmake ninja-build gperf \
    ccache dfu-util device-tree-compiler wget \
    python3-dev python3-pip python3-setuptools python3-tk python3-wheel xz-utils file \
    make gcc gcc-multilib g++-multilib libsdl2-dev libmagic1

# Clean up stale packages
RUN apt-get clean -y && \
	apt-get autoremove --purge -y && \
	rm -rf /var/lib/apt/lists/*

# Get NCS
RUN mkdir ${HOME}/ncs && cd ${HOME}/ncs && \
    west init -m https://github.com/nrfconnect/sdk-nrf --mr v2.3.0 && \
    west update && \
    west zephyr-export

# Install additional Python dependencies
RUN pip3 install --user -r ${HOME}/ncs/zephyr/scripts/requirements.txt && \
    pip3 install --user -r ${HOME}/ncs/nrf/scripts/requirements.txt && \
    pip3 install --user -r ${HOME}/ncs/bootloader/mcuboot/scripts/requirements.txt

# Get Zephyr
RUN cd ~ && \
    wget https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.15.2/zephyr-sdk-0.15.2_linux-x86_64.tar.gz && \
    wget -O - https://github.com/zephyrproject-rtos/sdk-ng/releases/download/v0.15.2/sha256.sum | shasum --check --ignore-missing && \
    tar xvf zephyr-sdk-0.15.2_linux-x86_64.tar.gz --directory ${HOME}
RUN ./${HOME}/zephyr-sdk-0.15.2/setup.sh -h -c

# Setup udev
# RUN sudo cp ~/zephyr-sdk-0.15.2/sysroots/x86_64-pokysdk-linux/usr/share/openocd/contrib/60-openocd.rules /etc/udev/rules.d && \
#     sudo udevadm control --reload
