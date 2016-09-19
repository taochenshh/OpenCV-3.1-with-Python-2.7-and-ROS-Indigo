# OpenCV-3.1-with-Python-2.7-and-ROS-Indigo


## OpenCV 3.1 Installation with Python 2.7 and ROS Indigo on Ubuntu 14.04

## OpenCV 3.1 Installation
Simply ctrl + c and shift+ctrl + v(Remember to change some path based on your own computer)
```bash
sudo apt-get -y update
sudo apt-get -y upgrade
sudo apt-get -y dist-upgrade
sudo apt-get -y autoremove


sudo apt-get install build-essential git cmake pkg-config
sudo apt-get install libjpeg8-dev libtiff4-dev libjasper-dev libpng12-dev
sudo apt-get install libilmbase-dev libilmbase6 libopenexr6
sudo apt-get install libavcodec-dev libavformat-dev libswscale-dev 
sudo apt-get install libv4l-dev libjpeg-dev libopenexr-dev
sudo apt-get install libgtk2.0-dev libeigen3-dev
sudo apt-get install libatlas-base-dev gfortran  libtbb-dev qt5-default libvtk6-dev
sudo apt-get install -y libdc1394-22-dev libavcodec-dev libavformat-dev 
sudo apt-get install libswscale-dev libtheora-dev 
sudo apt-get install libvorbis-dev libxvidcore-dev libx264-dev 
sudo apt-get install yasm libopencore-amrnb-dev 
sudo apt-get install libopencore-amrwb-dev libv4l-dev libxine2-dev
sudo apt-get install -y doxygen
sudo apt-get install -y python-dev python-tk python-numpy
```
```bash
cd ~/<my_working_directory>
git clone https://github.com/Itseez/opencv.git
git clone https://github.com/Itseez/opencv_contrib.git
cd opencv
mkdir build
cd build
```
```bash
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D OPENCV_EXTRA_MODULES_PATH=/home/chentao/software/opencv/opencv_contrib/modules  -DINSTALL_C_EXAMPLES=ON -DINSTALL_PYTHON_EXAMPLES=ON -DBUILD_EXAMPLES=ON -DWITH_IPP=ON -DBUILD_NEW_PYTHON_SUPPORT=ON -D WITH_TBB=ON -D WITH_V4L=ON -D WITH_QT=ON -D WITH_OPENGL=ON -DWITH_OPENCL=ON -DWITH_VTK=ON -DBUILD_TIFF=ON -DWITH_EIGEN=ON  -DPYTHON_EXECUTABLE=$(which python) -DPYTHON_INCLUDE_DIR=$(python -c "from distutils.sysconfig import get_python_inc; print(get_python_inc())") -DPYTHON_PACKAGES_PATH=$(python -c "from distutils.sysconfig import get_python_lib; print(get_python_lib())")  ..
```

**Note:** Please change the `OPENCV_EXTRA_MODULES_PATH` according to your own environment
And you may not be able to finish this step because it failed to download `ippicv_linux_20151201.tgz`. To solve this, you can manually download `ippicv_linux_20151201.tgz`(https://raw.githubusercontent.com/Itseez/opencv_3rdparty/81a676001ca8075ada498583e4166079e5744668/ippicv/ippicv_linux_20151201.tgz), and put it under `.../opencv/opencv/3rdparty/ippicv/downloads/linux-808b791a6eac9ed78d32a7666804320e`.


```bash
make -j8
sudo make install
```
If you find that cv2.so was not generated after installation, you may need to comment the `export PATH="/home/chentao/software/anaconda2/bin:$PATH"` in ~/.bashrc to use the python in /usr/bin/python

```bash
sudo gedit /etc/ld.so.conf.d/opencv.conf
```
Add the following line at the end of the file(it may be an empty file, that is ok) and then save it:
```bash
/usr/local/lib
```

Run the following code to configure the library:
```bash
sudo ldconfig
```

Then,
```bash
sudo gedit /etc/bash.bashrc
```

Add these two lines at the end of the file and save it:
```bash
PKG_CONFIG_PATH=$PKG_CONFIG_PATH:/usr/local/lib/pkgconfig
export PKG_CONFIG_PATH
```


If you have anaconda installed in your computer, you might need to create a symbolic link for cv2.so so as to let the python in anaconda find this file. Here is an example(you might need to change the directory according to where you install anaconda).
```bash
cd ~/software/anaconda2/lib/python2.7/site-packages
sudo ln -s /usr/local/lib/python2.7/dist-packages/cv2.so cv2.so
```

## To work with ROS Indigo
To recompile cv_bridge so as to make it fit with OpenCV 3.1, do the following commands:

```bash
cd ~/Downloads
git clone https://github.com/ros-perception/vision_opencv.git
cd vision_opencv
mkdir -p ~/catkin_cv/src
cd ~/catkin_cv/src
catkin_init_workspace
cp -rf cv_bridge ~/catkin_cv/src
mv cv_bridge cv_bridge_new
cd cv_bridge_new
gedit CMakeLists.txt
```
in CMakeLists.txt, change the project name `project(cv_bridge)` into `project(cv_bridge_new)`, save it.
```bash
gedit package.xml
```
In package.xml, change the second line into `<name>cv_bridge_new</name>`, save it.
```bash
cd ..
cd ..
catkin_make
```
Modify ~/.bashrc to make it contain these lines:
```
source /opt/ros/indigo/setup.bash
source /home/chentao/catkin_cv/devel/setup.bash
source /home/chentao/catkin_ws/devel/setup.bash
```

When you want to use cv_bridge in the future, use cv_bridge_new package instead,
just add `cv_bridge_new` inside `find_package` in CMakeLists.txt in your project, 
and add ```#include <cv_bridge/cv_bridge.h>```  in your cpp file.







