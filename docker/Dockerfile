# FROM ros:iron-ros-base
FROM nvidia/cuda:11.8.0-runtime-ubuntu22.04

ARG PROJECT_NAME
RUN sudo DEBIAN_FRONTEND=noninteractive apt update  &&\
    # apt Dependencies
    sudo DEBIAN_FRONTEND=noninteractive apt install -y nano \
    git &&\
    # Disable error of dubious git ownership
    git config --global --add safe.directory /${PROJECT_NAME} &&\
    # apt-get Dependencies
    sudo DEBIAN_FRONTEND=noninteractive apt-get install htop -y \
    curl \
    software-properties-common \
    net-tools \
    unzip \
    wget \
    python3-pip

ENV DEBIAN_FRONTEND=noninteractive
ENV LANG C.UTF-8

# Install virtual display
RUN sudo apt-get update &&\
    # Package dependencies
    sudo apt-get install x11-apps -y &&\
    sudo apt-get update && \
    sudo apt-get install libx11-6 libxext6 libxau6 libxxf86vm1 libxdmcp6 libxcb1 libxrender1 libxrandr2 libxfixes3 libxi6 libxinerama1 libxkbcommon-x11-0 libgl1-mesa-glx libgl1-mesa-dri mesa-utils -y &&\
    sudo apt-get install x11-xserver-utils -y &&\
    # command "xeyes" for testing if X11 connection working
    sudo apt-get install x11-apps -y &&\
    sudo apt-get install libx11-6 libxext6 libxau6 libxxf86vm1 libxdmcp6 libxcb1 libxrender1 libxrandr2 libxfixes3 libxi6 libxinerama1 libxkbcommon-x11-0 libgl1-mesa-glx libgl1-mesa-dri mesa-utils -y &&\
    sudo apt-get install x11-xserver-utils -y &&\
    # Virtual display packages
    sudo DEBIAN_FRONTEND=noninteractive apt-get install -y \
    novnc \
    supervisor \
    x11vnc \
    xvfb \
    xfce4 &&\
    # Firefox
    sudo apt-get install -y software-properties-common && \
    yes '' | sudo add-apt-repository ppa:mozillateam/ppa &&\
    echo 'Package: *\nPin: release o=LP-PPA-mozillateam\nPin-Priority: 1001\n' | sudo tee /etc/apt/preferences.d/mozilla-firefox &&\
    sudo apt install firefox -y &&\
    # Bug fixes
    sudo mkdir /tmp/.X11-unix &&\
    # Manual install for virtual desktop update (xfce v4.18)
    sudo DEBIAN_FRONTEND=noninteractive add-apt-repository ppa:xubuntu-dev/staging &&\
    sudo apt install awesome -y &&\
    sudo apt-get install xterm &&\
    sudo apt update &&\
    sudo apt upgrade -y



# Install Sublime Text
RUN wget -qO - https://download.sublimetext.com/sublimehq-pub.gpg | gpg --dearmor | sudo tee /etc/apt/trusted.gpg.d/sublimehq-archive.gpg > /dev/null &&\
    echo "deb https://download.sublimetext.com/ apt/stable/" | sudo tee /etc/apt/sources.list.d/sublime-text.list &&\
    sudo apt-get update &&\
    sudo apt-get install sublime-text -y

# Install GitHub CLI
RUN (type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) &&\
    sudo mkdir -p -m 755 /etc/apt/keyrings &&\
    wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null &&\
    sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg &&\
    echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null &&\
    sudo apt update &&\
    sudo apt install gh -y


# Define ARG for build-time variables
ARG USER_NAME
ARG GROUP_NAME
ARG USER_ID
ARG PROJECT_NAME
ARG HOST_IP
ARG VNC_PORT
# Set ENV if these need to be available at runtime
ENV USER_NAME=${USER_NAME}
ENV GROUP_NAME=${GROUP_NAME}
ENV USER_ID=${USER_ID}
ENV PROJECT_NAME=${PROJECT_NAME}
ENV HOST_IP=${HOST_IP}
ENV VNC_PORT=${VNC_PORT}

RUN sudo addgroup --gid ${USER_ID} ${GROUP_NAME} && sudo adduser --uid ${USER_ID} --gid ${USER_ID} ${USER_NAME} && sudo usermod -p '' ${USER_NAME} && sudo adduser ${USER_NAME} sudo &&\
    echo 'export PATH="$PATH:/home/${USER_NAME}/.local/bin"' >> ~/.bashrc  &&\
    echo "export PS1='\u@\$(id -gn):\w $ '" >> /home/${USER_NAME}/.bashrc
USER ${USER_NAME}


ARG PROJECT_NAME
COPY ./entrypoint.sh /entrypoint.sh
#COPY ./entrypoint.sh /ros_entrypoint.sh
RUN sudo chmod 777 /entrypoint.sh

# Update the Python PATH in .bashrc, relevant for tensorboard
RUN echo 'export PATH=$PATH:$HOME/.local/bin' >> $HOME/.bashrc 

# # Switch to the workspace and run rosdep commands
# WORKDIR /${PROJECT_NAME}/ws/ros_ws/src
# RUN git clone https://github.com/UniversalRobots/Universal_Robots_Client_Library.git
# RUN git clone -b iron https://github.com/UniversalRobots/Universal_Robots_ROS2_Driver.git
# RUN git clone -b iron https://github.com/UniversalRobots/Universal_Robots_ROS2_Ignition_Simulation.git ur_simulation_gz


# WORKDIR /${PROJECT_NAME}/ws/ros_ws
# RUN rosdep update && rosdep install --ignore-src --from-paths src -y


WORKDIR /${PROJECT_NAME}
CMD ["/entrypoint.sh"]