# moveit/moveit:kinetic-ci
# Sets up a base image to use for running Continuous Integration on Travis

FROM ros:kinetic-ros-base
MAINTAINER Dave Coleman dave@picknik.ai

ENV TERM xterm

# Setup (temporary) ROS workspace
WORKDIR /root/ws_moveit

# Commands are combined in single RUN statement with "apt/lists" folder removal to reduce image size
# https://docs.docker.com/develop/develop-images/dockerfile_best-practices/#minimize-the-number-of-layers
RUN \
    mkdir src && \
    cd src && \
    #
    # Download moveit source, so that we can get necessary dependencies
    wstool init --shallow . https://raw.githubusercontent.com/ros-planning/moveit/${ROS_DISTRO}-devel/moveit.rosinstall && \
    #
    # Update apt package list as previous containers clear the cache
    apt-get -qq update && \
    apt-get -qq dist-upgrade && \
    #
    # Install some base dependencies
    apt-get -qq install -y \
        # Some source builds require a package.xml be downloaded via wget from an external location
        wget \
        # Required for rosdep command
        sudo \
        # Required for installing dependencies
        python-rosdep \
        # Preferred build tools
        python-catkin-tools \
        clang-6.0 clang-format-3.9 clang-tidy-6.0 clang-tools-6.0 \
        ccache && \
    #
    # Download all dependencies of MoveIt!
    rosdep update && \
    rosdep install -y --from-paths . --ignore-src --rosdistro ${ROS_DISTRO} --as-root=apt:false && \
    #
    # Remove the source code from this container
    cd .. && \
    rm -rf src/ && \
    #
    # Clear apt-cache to reduce image size
    rm -rf /var/lib/apt/lists/*

# Continous Integration Setting
ENV IN_DOCKER 1
