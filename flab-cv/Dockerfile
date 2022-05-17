FROM nvidia/cudagl:11.3.1-devel-ubuntu20.04
ENV DEBIAN_FRONTEND=noninteractive

RUN apt-get update -y
RUN apt-get upgrade -y

RUN apt-get install -y  vim-gtk \
      	net-tools \
      	tmux \
      	dirmngr\
      	gnupg2 \
      	build-essential \
      	git \
      	libusb-1.0-0-dev \
      	cmake \
      	curl \
	mesa-common-dev \
	freeglut3-dev \
	udev
RUN apt-get install -y libcurl4-gnutls-dev libexpat1-dev gettext libz-dev libssl-dev iputils-ping xterm -y
RUN apt-get install -y xorg-dev libglu1-mesa-dev 
	
RUN cd /root && \
	mkdir source &&	\
	mkdir work && \
	cd source && \
	git clone https://github.com/IntelRealSense/librealsense.git && \
	cd librealsense && \
	git checkout -b 2.50.0 refs/tags/v2.50.0 && \
	cp config/99-realsense-libusb.rules /etc/udev/rules.d/ && \
	apt-get update -y && \
	mkdir build && \
	cd build &&\
	cmake .. && \
	make -j8 && \
	make install 

RUN apt-get install -y libeigen3-dev \
	libflann-dev \
	libboost-all-dev \
	qhull-bin \
	libvtk7-dev && \
	cd /root/source && \
	git clone https://github.com/PointCloudLibrary/pcl.git && \
	cd pcl && \
	git checkout -b pcl-1.10.1 refs/tags/pcl-1.10.1 && \
	mkdir build && \
	cd build && \
	cmake .. && \
	make -j8 && \
	make install

RUN cd /root/source && \
	git clone https://github.com/opencv/opencv.git && \
	git clone https://github.com/opencv/opencv_contrib.git && \
	cd opencv_contrib && \
	git checkout -b 4.5.5 refs/tags/4.5.5 && \
	cd ../opencv && \
	git checkout -b 4.5.5 refs/tags/4.5.5 && \
	mkdir build && \
	cd build && \
	cmake -DOPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules .. && \
	make -j8 && \
	make install

RUN apt-key adv --keyserver hkp://keyserver.ubuntu.com:80 --recv-keys C1CF6E31E6BADE8868B172B4F42ED6FBAB17C654
RUN echo "deb http://packages.ros.org/ros/ubuntu focal main" > /etc/apt/sources.list.d/ros1-latest.list
ENV LANG C.UTF-8
ENV LC_ALL C.UTF-8
ENV ROS_DISTRO noetic

RUN apt-get update && apt-get install -y --no-install-recommends \
      ros-noetic-desktop-full 
RUN echo "source /opt/ros/noetic/setup.bash" >> ~/.bashrc
RUN ["/bin/bash", "-c", "source ~/.bashrc"]
RUN apt install python3-pip python3-rosdep python3-rosinstall python3-rosinstall-generator python3-wstool -y

RUN rosdep init
RUN rosdep update

RUN mkdir -p /root/catkin_ws/src
WORKDIR /root/catkin_ws
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash; catkin_make"

RUN echo "source /root/catkin_ws/devel/setup.bash" >> /root/.bashrc
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash; catkin_make"
RUN apt-get install ros-noetic-ddynamic-reconfigure
RUN cd /root/catkin_ws/src && \
	git clone https://github.com/IntelRealSense/realsense-ros.git && \
	cd realsense-ros && \
	git checkout -b 2.3.2 refs/tags/2.3.2
WORKDIR /root/catkin_ws
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash; catkin_make"
RUN /bin/bash -c "source /opt/ros/noetic/setup.bash; catkin_make install"

WORKDIR /root/source/
RUN git clone https://github.com/CloudCompare/CloudCompare.git
RUN cd CloudCompare && \
	git checkout -b v2.12.1 refs/tags/v2.12.1 && \
	git submodule update --init --recursive

RUN apt-get install -y libqt5*-dev qt5-default qtscript5-dev libssl-dev qttools5-dev qttools5-dev-tools qtmultimedia5-dev libqt5svg5-dev libqt5webkit5-dev libsdl2-dev libasound2 libxmu-dev libxi-dev freeglut3-dev libasound2-dev libjack-jackd2-dev libxrandr-dev libqt5xmlpatterns5-dev libqt5xmlpatterns5 x11-apps mesa-utils

RUN mkdir /root/source/CloudCompare/build
WORKDIR /root/source/CloudCompare/build
RUN cmake -D PLUGIN_IO_QCSV_MATRIX=ON -D PLUGIN_STANDARD_QANIMATION=ON -D PLUGIN_STANDARD_QPCL=ON ..

WORKDIR /root/source/
RUN git clone https://github.com/Livox-SDK/Livox-SDK.git
WORKDIR /root/source/Livox-SDK
RUN cd /root/source/Livox-SDK/build && cmake .. && make -j8 && make install
WORKDIR /root/
