##############
# Base image #
##############
FROM ros:iron-ros-base as base

ARG AMENT_WORKSPACE_DIR="/ament_ws"

LABEL org.opencontainers.image.authors="orcil.lionel@gmail.com"
LABEL description="ROS 2 Iron Docker template"
LABEL version="1.0"

WORKDIR ${AMENT_WORKSPACE_DIR}
SHELL ["/bin/bash", "-c"]

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
 && apt-get -y install \
    git \
    python3-colcon-clean \
    python3-osrf-pycommon \
 && rm -rf /var/lib/apt/lists/*

RUN apt-get update \
 && apt-get -y install \
    ros-${ROS_DISTRO}-rmw-cyclonedds-cpp \
 && rm -rf /var/lib/apt/lists/*

# Add additional installation instructions here...

ENV DEBIAN_FRONTEND=dialog


#####################
# Development image #
#####################
FROM base as dev

ARG USERNAME="developer"
ARG UID=1000
ARG GID=1000

ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update \
 && apt-get install -y \
    iperf3 \
    iputils-ping \
    net-tools \
    python3-vcstool \
    wget \
 && rm -rf /var/lib/apt/lists/*

RUN apt-get update \
 && apt-get install -y \
    ros-${ROS_DISTRO}-rviz2 \
 && rm -rf /var/lib/apt/lists/*

# Install additional developer tools here...

ENV DEBIAN_FRONTEND=dialog

RUN apt-get update \
 && apt-get install -y sudo \
 && rm -rf /var/lib/apt/lists/* \
 && addgroup --gid ${GID} ${USERNAME} \
 && adduser --disabled-password --gecos '' --uid ${GID} --gid ${GID} ${USERNAME} \
 && echo ${USERNAME} ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/${USERNAME} \
 && chown -R ${UID}:${GID} /home/${USERNAME} \
 && chown -R ${UID}:${GID} ${AMENT_WORKSPACE_DIR}

USER ${USERNAME}


############################
# Visual Studio Code image #
############################
FROM dev as vsc

COPY ".vscode" "${AMENT_WORKSPACE_DIR}/.vscode"
RUN echo "alias rsource='source ${AMENT_WORKSPACE_DIR}/.vscode/ros2_source.bash ${ROS_DISTRO}'" >> /home/${USERNAME}/.bash_aliases \
 && echo "alias rbuild='(cd ${AMENT_WORKSPACE_DIR} && colcon build)'" >> /home/${USERNAME}/.bash_aliases \
 && echo "alias rclean='(cd ${AMENT_WORKSPACE_DIR} && colcon clean workspace -y)'" >> /home/${USERNAME}/.bash_aliases \
 && echo "rsource" >> /home/${USERNAME}/.bashrc