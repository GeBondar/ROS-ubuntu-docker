ARG from

FROM ${from}


RUN apt update && apt install -y    apt-utils \
                                    lsb-release \
                                    mesa-utils \
                                    gnupg2 \
                                    net-tools \
                                    build-essential \
                                    wget \
                                    unzip \
                                    curl \
                                    git \
                                    mc \
                                    vim \
                                    nano

# Timezone Configuration
ENV TZ=Europe/Moscow
RUN ln -snf /usr/share/zoneinfo/$TZ /etc/localtime && echo $TZ > /etc/timezone

# Install ROS desktop and Gazebo

RUN sh -c 'echo "deb http://packages.ros.org/ros/ubuntu $(lsb_release -sc) main" > /etc/apt/sources.list.d/ros-latest.list' && \
    apt-key adv --keyserver 'hkp://keyserver.ubuntu.com:80' --recv-key C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654 && \
    apt update && DEBIAN_FRONTEND=noninteractive \
                      apt install -y ros-melodic-desktop-full

RUN apt update && apt install -y    ros-melodic-joy \
                                    python-rosdep \
                                    python-rosinstall \
                                    python-rosinstall-generator \
                                    python-wstool \
                                    python-catkin-tools \
                                    libcanberra-gtk-module \
                                    libcanberra-gtk3-module \
                                    ros-melodic-pid \
                                    ros-melodic-visp* \
                                    ros-melodic-rosbridge-server 
RUN rosdep init && rosdep update && \
    echo "source /opt/ros/melodic/setup.bash"  >> ~/.bashrc && \
    echo "source /catkin_ws/devel/setup.bash"  >> ~/.bashrc




RUN apt update && apt install -y    libopencv-dev \
                                    libx11-dev \
                                    liblapack-dev \
                                    libv4l-dev \
                                    libzbar-dev \
                                    libpthread-stubs0-dev \
                                    libsdl-dev \
                                    libsdl-image1.2-dev \
                                    libeigen3-dev \
                                    libudev-dev 

# updating cmake to 3.13
RUN wget https://github.com/Kitware/CMake/releases/download/v3.13.4/cmake-3.13.4.tar.gz && tar zxvf cmake-3.13.4.tar.gz
WORKDIR /cmake-3.13.4
RUN ./bootstrap && make -j4 && make install

# Updating python3.6 to python3.8
RUN apt update && apt install -y software-properties-common && \
    sudo add-apt-repository -y ppa:deadsnakes/ppa && \
    apt install -y python3.8 python3-setuptools && \
    ln -sfn /usr/bin/python3.8 /usr/bin/python3 && \
    ln -sfn /usr/bin/python3.8 /usr/bin/python
RUN apt update && apt install -y python-pip python3-pip python3.8-dev
RUN pip3 install --upgrade pip && \
    pip3 install --upgrade wheel && \
    pip3 install setuptools
RUN pip3 install --upgrade cython
RUN pip3 install pythran
RUN pip3 install pybind11 packaging utils
RUN apt update && apt install -y gfortran libffi-dev
RUN pip3 install rosdep rospkg catkin_tools catkin_pkg 

RUN pip3 install numpy scipy==1.8.1
RUN pip3 install packaging scikit-learn scikit-build scikit-image 
RUN python3 -m pip install --no-cache-dir torch==1.10 torchfile torchvision
RUN pip3 install pathlib statistics termcolor
RUN pip3 install Pillow==9.1.1
RUN pip3 install rapidfuzz-capi rapidfuzz pyfastnoisesimd shapely
RUN pip3 install git+https://github.com/facebookresearch/detectron2
RUN pip3 install defusedxml netifaces open3d
RUN pip3 install opencv-contrib-python

# Realsense
RUN apt-key adv --keyserver keyserver.ubuntu.com --recv-key F6E65AC044F831AC80A06380C8B3A55A6F3EFCDE && \
    python3.6 /usr/bin/apt-add-repository -y "deb https://librealsense.intel.com/Debian/apt-repo $(lsb_release -cs) main" -u && \
    apt update && apt install -y librealsense2-dkms librealsense2-utils librealsense2-dev librealsense2-dbg ros-melodic-realsense2-camera && \
    pip3 install pyrealsense2

# RUN git clone https://github.com/aim-uofa/AdelaiDet.git && \
#     cd AdelaiDet && \
#     python3 setup.py build develop

# CV-bridge install
WORKDIR /cv-bridge_ws/src
RUN git clone -b melodic https://github.com/ros-perception/vision_opencv.git
WORKDIR /cv-bridge_ws
RUN /bin/bash -c 'source /opt/ros/melodic/setup.bash; catkin config -DPYTHON_EXECUTABLE=/usr/bin/python3 -DPYTHON_INCLUDE_DIR=/usr/include/python3.8 -DPYTHON_LIBRARY=/usr/lib/x86_64-linux-gnu/libpython3.8.so; /usr/bin/python3.8-config --includes; catkin build cv_bridge' && \
    echo "source /cv-bridge_ws/devel/setup.bash --extend"  >> ~/.bashrc
RUN pip3 install Polygon3
RUN apt update && apt install -y terminator
ADD docker/terminator.config /root/.config/terminator/config

# RUN cp -r foreground_segmentation/ ros_nodes/ && \
#     pip3 install .
# WORKDIR /ros_add_ws
# RUN /bin/bash -c 'source /opt/ros/melodic/setup.bash; catkin_make' && \


WORKDIR /catkin_ws
ENTRYPOINT /bin/bash -c "export DBUS_SESSION_BUS_ADDRESS=`dbus-daemon --fork --config-file=/usr/share/dbus-1/session.conf --print-address` && python2 /usr/bin/terminator"
