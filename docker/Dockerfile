ARG DEBIAN_VER_NAME=bookworm
FROM debian:${DEBIAN_VER_NAME}

ARG GID=1000
ARG UID=1000
ARG UNAME=deveng
ARG HOME=/home/${UNAME}

# add the user group
RUN groupadd --gid ${GID} ${UNAME}
# add a new user and change the root password
RUN useradd \
    -d ${HOME} --create-home \
    # this option is important to prevent the command from creating huge files (which make the docker builder
    # hang) when the GID/UID values are big.
    --no-log-init \
    --gid ${GID} --uid ${UID} \
    --skel /etc/skel --shell /bin/bash \
    ${UNAME}
# set password(s)
RUN echo "${UNAME}:${UNAME}" | chpasswd && echo "root:root" | chpasswd

# install SDK required tools
RUN apt-get update && apt-get install -y \
    git g++ cmake gcc-arm-none-eabi libnewlib-arm-none-eabi libstdc++-arm-none-eabi-newlib build-essential \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*
RUN apt-get update && apt-get install -y \
    python3 python3-pip python3-virtualenv \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

# needed for C/C++ LSP server
RUN apt-get update && apt-get install -y \
    clangd \
    && apt-get clean \
    && rm -rf /var/lib/apt/lists/*

#--------------------------------------------------------------------------------------------------------------
# user actions
USER ${UNAME}

# install PlatformIO
ARG PLATFORMIO_PENV_PATH=${HOME}/.platformio/penv
ENV PLATFORMIO_PENV_ACTIVATE=${PLATFORMIO_PENV_PATH}/bin/activate
# NOTE: make sure to use PY to invoke the python instance in the virtual environment
RUN virtualenv ${PLATFORMIO_PENV_PATH} \
    && . ${PLATFORMIO_PENV_PATH}/bin/activate \
    && pip install platformio

# configure environment & get SDK
ENV PICO_SDK_PATH=${HOME}/pico-sdk
ENV PICO_PLATFORM=rp2040
RUN git clone --recurse-submodules https://github.com/raspberrypi/pico-sdk.git ${PICO_SDK_PATH}

# ensure the virtual environment is always enabled
RUN echo "alias ll='ls -lah '\nsource ${PLATFORMIO_PENV_ACTIVATE}" >>~/.bashrc

USER root
# give user access to keyboard w/o being sudo
RUN usermod -aG dialout ${UNAME}
USER ${UNAME}

WORKDIR /code
