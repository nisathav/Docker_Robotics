FROM ros:foxy

# Example of installing programs
RUN apt-get update \
    && apt-get install -y \
    python3-argcomplete \
    nano \
    libgazebo11 \
    gazebo11-common \
    gazebo11 \
    ros-foxy-gazebo-dev \
    ros-foxy-gazebo-ros \
    ros-foxy-gazebo-ros-pkgs \
    ros-foxy-ros2-control \
    ros-foxy-ros2-controllers \
    ros-foxy-gazebo-ros2-control \
    ros-foxy-hardware-interface \
    ros-foxy-xacro \
    vim \
    ros-foxy-rviz2 \
    curl \
    apt-transport-https \
    ca-certificates \
    gnupg \
    lsb-release \
    && rm -rf /var/lib/apt/lists/*

# Set environment variables
#ENV GAZEBO_MASTER_URI http://localhost:11345

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