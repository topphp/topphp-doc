### 安装
在安装PHPOpenCV首先需要安装OpenCV

### 安装OpenCV
##### 本文仅介绍Ubuntu系统下的环境搭建,其他Linux系统和Ubuntu类似,请自行安装
```shell
sudo apt-get install gcc-4.8 g++-4.8
sudo apt-get install build-essential
sudo apt-get install cmake git libgtk2.0-dev pkg-config libavcodec-dev libavformat-dev libswscale-dev
sudo apt-get install libtbb2 libtbb-dev libjpeg-dev libpng-dev libtiff-dev libjasper-dev libdc1394-22-dev re2c
sudo apt install qt5-default
sudo apt install python3-pip
```
### 下载opencv_contrib模块
> 该模块主要用户人脸识别等扩展功能。

```shell
git clone https://github.com/opencv/opencv_contrib.git
cd opencv_contrib
git checkout 3.3.0-rc
cd ..
```

### 下载opencv
```shell
git clone https://github.com/opencv/opencv.git
cd opencv
git checkout 3.3.0-rc

# 编译安装opencv
mkdir build
cd build
cmake -D CMAKE_BUILD_TYPE=RELEASE -D CMAKE_INSTALL_PREFIX=/usr/local -D WITH_TBB=ON -D BUILD_NEW_PYTHON_SUPPORT=ON -D WITH_V4L=ON -D INSTALL_C_EXAMPLES=ON -D INSTALL_PYTHON_EXAMPLES=ON -D BUILD_EXAMPLES=ON -D WITH_QT=ON -D WITH_OPENGL=ON -D OPENCV_EXTRA_MODULES_PATH=../../opencv_contrib/modules ..
make -j6
sudo make install
sudo sh -c 'echo "/usr/local/lib" > /etc/ld.so.conf.d/opencv.conf'
sudo ldconfig
```
### 检测
> 编译安装完后，可以使用pkg-config来检测是否安装成功

```shell
pkg-config --libs opencv

# 显示以下信息表示安装成功
-L/usr/local/lib -lopencv_stitching -lopencv_superres -lopencv_videostab -lopencv_photo -lopencv_aruco -lopencv_bgsegm -lopencv_bioinspired -lopencv_ccalib -lopencv_cvv -lopencv_dpm -lopencv_face -lopencv_freetype -lopencv_fuzzy -lopencv_img_hash -lopencv_line_descriptor -lopencv_optflow -lopencv_reg -lopencv_rgbd -lopencv_saliency -lopencv_stereo -lopencv_structured_light -lopencv_phase_unwrapping -lopencv_surface_matching -lopencv_tracking -lopencv_datasets -lopencv_text -lopencv_dnn -lopencv_plot -lopencv_ml -lopencv_xfeatures2d -lopencv_shape -lopencv_video -lopencv_ximgproc -lopencv_calib3d -lopencv_features2d -lopencv_highgui -lopencv_videoio -lopencv_flann -lopencv_xobjdetect -lopencv_imgcodecs -lopencv_objdetect -lopencv_xphoto -lopencv_imgproc -lopencv_core
```

### 安装PHPOpenCV扩展
> 下载并安装PHPOpenCV扩展库,编译过程很慢,请耐心等待.

```shell
git clone https://github.com/hihozhou/php-opencv.git
cd php-opencv
git checkout 3.3.0
phpize
./configure --with-php-config=/usr/bin/php-config --enable-debug
sudo make && sudo make install
```
### 配置php.ini
```shell
extension="opencv.so"
```


### 卸载OpenCV
```shell
# 进入build文件夹执行
cd build
make uninstall
#以下是options
cd ..
sudo rm -r build
sudo rm -r /usr/local/include/opencv2 /usr/local/include/opencv /usr/include/opencv /usr/include/opencv2 /usr/local/share/opencv /usr/local/share/OpenCV /usr/share/opencv /usr/share/OpenCV /usr/local/bin/opencv* /usr/local/lib/libopencv*
```