# See here for image contents: https://github.com/microsoft/vscode-dev-containers/tree/v0.245.2/containers/ubuntu/.devcontainer/base.Dockerfile

# [Choice] Ubuntu version (use ubuntu-22.04 or ubuntu-18.04 on local arm64/Apple Silicon): ubuntu-22.04, ubuntu-20.04, ubuntu-18.04
ARG VARIANT="jammy"
FROM ubuntu:20.04

ARG USERNAME=rollingbee
ARG USER_UID=1001
ARG USER_GID=$USER_UID
# Create the user
RUN groupadd --gid $USER_GID $USERNAME \
    && useradd --uid $USER_UID --gid $USER_GID -m $USERNAME \
    #
    # [Optional] Add sudo support. Omit if you don't need to install software after connecting.
    && apt-get update \
    && apt-get install -y sudo \
    && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME \
    && chmod 0440 /etc/sudoers.d/$USERNAME

# ********************************************************
# *                     Setup ROS2                       *
# ********************************************************
ENV ROS2_WS_DIR=/home/${USERNAME}/ros2_rolling_ws
ENV SRC_DIR=${ROS2_WS_DIR}/src
ENV ROSDISTRO=rolling
ENV ROS_VERSION=2
ENV ROS_PYTHON_VERSION=3

SHELL ["/bin/bash", "-c"]
ENV DEBIAN_FRONTEND=noninteractive
RUN locale  # check for UTF-8 \
    && apt-get update && apt-get install locales \
    && locale-gen en_US en_US.UTF-8 \
    && update-locale LC_ALL=en_US.UTF-8 LANG=en_US.UTF-8 \
    && export LANG=en_US.UTF-8

RUN apt-get update \
    && apt-get -y install --no-install-recommends software-properties-common \
    && add-apt-repository universe \
    && apt-get update \ 
    && apt-get install -y curl gnupg lsb-release python3 python3-pip wget lsb-release gnupg curl \
    && curl -sSL https://raw.githubusercontent.com/ros/rosdistro/master/ros.key -o /usr/share/keyrings/ros-archive-keyring.gpg \
    && echo "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/ros-archive-keyring.gpg] http://packages.ros.org/ros2/ubuntu $(source /etc/os-release && echo $UBUNTU_CODENAME) main" | tee /etc/apt/sources.list.d/ros2.list > /dev/null

RUN apt-get update && apt-get install -y \
    build-essential \
    cmake \
    git \
    python3-colcon-common-extensions \
    python3-flake8 \
    python3-flake8-docstrings \
    python3-pip \
    python3-pytest \
    python3-pytest-cov \
    python3-rosdep \
    python3-setuptools \
    python3-vcstool \
    wget

RUN python3 -m pip install -U \
    flake8-blind-except \
    flake8-builtins \
    flake8-class-newline \
    flake8-comprehensions \
    flake8-deprecated \
    flake8-import-order \
    flake8-quotes \
    pytest-repeat \
    pytest-rerunfailures

USER ${USERNAME}
RUN sudo mkdir -p ${SRC_DIR}
WORKDIR ${ROS2_WS_DIR}
RUN sudo chown ${USERNAME}:${USERNAME} -R ${ROS2_WS_DIR}
RUN vcs import --input https://raw.githubusercontent.com/ros2/ros2/rolling/ros2.repos src
RUN sudo apt-get -y upgrade

RUN sudo rosdep init
RUN rosdep update
RUN rosdep install --from-paths src --ignore-src -y --skip-keys "fastcdr rti-connext-dds-6.0.1 urdfdom_headers"
RUN colcon build --symlink-install
RUN echo "source ${ROS2_WS_DIR}/install/setup.bash" >> /home/${USERNAME}/.bashrc

CMD ["/bin/bash", "--login", "-c"]


# [Optional] Set the default user. Omit if you want to keep the default as root.
# USER $USERNAME

# [Optional] Uncomment this section to install additional OS packages.
# RUN sudo apt-get update


