# Use the official image as a parent image
FROM ros:jazzy-perception

# Default configuration
ENV ROS_ENABLE_AUTOSTART=0
ENV ROS_BUILD_ON_STARTUP=on-failure

# Dockerfile for ROS-JAZZY
ARG USERNAME='rex'
ARG PASSWORD='changeme'
ARG GID=1000
ARG UID=1000

# Update the system
RUN apt update && apt upgrade -y

# Install random stuff
RUN DEBIAN_FRONTEND=noninteractive apt install -y git gdb nano curl wget python3 python3-pip net-tools apt-utils nano ssh less iputils-ping bc rapidjson-dev iproute2

# Random Stuff
ENV NOTVISIBLE "in users profile"
RUN echo "export VISIBLE=now" >> /etc/profile
RUN ln -sf /usr/share/zoneinfo/Europe/Warsaw /etc/localtime

# Create user
RUN groupadd -r -o -f -g ${GID} ${USERNAME}
# ubuntu haters be like
RUN userdel ubuntu
RUN useradd -m -s /bin/bash -u ${UID} -g ${GID} ${USERNAME}
RUN echo "${USERNAME}:${PASSWORD}" | chpasswd
RUN usermod -aG sudo ${USERNAME}

# Disable sudo password auth
RUN echo '%sudo ALL=(ALL) NOPASSWD:ALL' >> /etc/sudoers

# Colorful prompts ah yee
RUN sed -i 's/#force_color_prompt=yes/force_color_prompt=yes/' /home/${USERNAME}/.bashrc

# Add important stuff to bashrc
RUN echo "\nsource /opt/ros/jazzy/setup.bash\nexport ROS_DOMAIN_ID=1\n" >> /home/${USERNAME}/.bashrc
RUN echo "source ~/raptor_ws/install/setup.bash\n" >> /home/${USERNAME}/.bashrc

RUN apt update && apt upgrade -y

# Environment setup
RUN mkdir /home/${USERNAME}/raptor_ws
# Add required ros packages here # ros-jazzy-rviz2 is optional but required dunno. installed rviz_common so anyway
RUN apt install -y ros-jazzy-ros2-socketcan libasio-dev libceres-dev ros-jazzy-diagnostic-updater ros-jazzy-rtcm-msgs ros-jazzy-nmea-msgs ros-jazzy-pcl-ros ros-jazzy-geographic-msgs ros-jazzy-backward-ros ros-jazzy-bond ros-jazzy-bondcpp ros-jazzy-test-msgs ros-jazzy-behaviortree-cpp ros-jazzy-map-msgs ros-jazzy-ompl ros-jazzy-rviz-common ros-jazzy-rviz-default-plugins ros-jazzy-nav2-minimal-tb3-sim ros-jazzy-rtabmap-ros libeigen3-dev libjsoncpp-dev libspdlog-dev libcurl4-openssl-dev libopencv-dev libpcl-dev libqt5svg5-dev libqt5opengl5-dev libsqlite3-dev libproj-dev libyaml-cpp-dev libfreenect-dev libvtk9-qt-dev libvtk9-dev libgeographiclib-dev libboost-all-dev libtbb-dev libgraphicsmagick++-dev libnanoflann-dev nlohmann-json3-dev libbenchmark-dev

# Fix github 'unsafe' directory
USER ${USERNAME}
RUN git config --global --add safe.directory /home/rex/raptor_ws
# Manage public ssh keys
RUN rm -rf /home/${USERNAME}/.ssh
RUN mkdir -m 0700 /home/${USERNAME}/.ssh
RUN touch /home/${USERNAME}/.ssh/authorized_keys
RUN chmod 640 /home/${USERNAME}/.ssh/authorized_keys
ADD --chown=${USERNAME}:${USERNAME} public_keys.d /home/${USERNAME}/.ssh/public_keys.d
RUN cat /home/${USERNAME}/.ssh/public_keys.d/*.pub >> /home/${USERNAME}/.ssh/authorized_keys || true
RUN rm -rf /home/${USERNAME}/.ssh/public_keys.d

USER root
COPY docker-entrypoint.sh /usr/local/bin/docker-entrypoint.sh
RUN chmod +x /usr/local/bin/docker-entrypoint.sh

CMD [ "/usr/local/bin/docker-entrypoint.sh" ]
