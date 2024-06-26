FROM osrf/ros:noetic-desktop-full

# Example of installing programs
RUN apt-get update \
    && apt-get install -y \
    python3-argcomplete \
    python3-rosdep \
    python3-pandas \
    nano \
    ros-noetic-gazebo-ros \
    ros-noetic-gazebo-ros-pkgs \
    ros-noetic-gazebo-dev \
    ros-noetic-gazebo-ros \
    ros-noetic-gazebo-ros-pkgs \
    ros-noetic-ros-control \
    ros-noetic-ros-controllers \
    ros-noetic-gazebo-ros-control \
    ros-noetic-hardware-interface \
    ros-noetic-xacro \
    vim \
    ros-noetic-rviz \
    ros-noetic-moveit \
    ros-noetic-cv-bridge \
    curl \
    apt-transport-https \
    ca-certificates \
    gnupg \
    ros-noetic-rosserial-arduino \
    ros-noetic-rosserial \
    ros-noetic-velodyne-gazebo-plugins \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Set environment variables
#ENV GAZEBO_MASTER_URI http://localhost:11345

# Install Arduino IDE
RUN apt-get update \
    && apt-get install -y \
    arduino \
    && rm -rf /var/lib/apt/lists/*

# Install Visual Studio Code
RUN curl -fsSL https://packages.microsoft.com/keys/microsoft.asc | gpg --dearmor -o /etc/apt/trusted.gpg.d/microsoft.gpg && \
    echo "deb [arch=amd64] https://packages.microsoft.com/repos/vscode stable main" | tee /etc/apt/sources.list.d/vscode.list && \
    apt-get update && \
    apt-get install -y code

# Example of copying a file
#USER root
#COPY mangobee_robot/ /mangobee_robot

# Create a non-root user
ARG USERNAME=ros
ARG USER_UID=1000
ARG USER_GID=$USER_UID

RUN groupadd --gid $USER_GID $USERNAME \
  && useradd -s /bin/bash --uid $USER_UID --gid $USER_GID -m $USERNAME \
  && mkdir /home/$USERNAME/.config && chown $USER_UID:$USER_GID /home/$USERNAME/.config

# Set up sudo
RUN apt-get update \
  && apt-get install -y sudo \
  && echo $USERNAME ALL=\(root\) NOPASSWD:ALL > /etc/sudoers.d/$USERNAME\
  && chmod 0440 /etc/sudoers.d/$USERNAME \
  && rm -rf /var/lib/apt/lists/*

# Copy the entrypoint and bashrc scripts so we have 
# our container's environment set up correctly
COPY entrypoint.sh /entrypoint.sh
COPY bashrc /home/${USERNAME}/.bashrc

# Expose ports for Gazebo and RViz
EXPOSE 11345
EXPOSE 9090

# Set up entrypoint and default command
ENTRYPOINT ["/bin/bash", "/entrypoint.sh"]
CMD ["bash"]

