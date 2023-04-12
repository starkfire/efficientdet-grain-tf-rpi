# efficientdet-grain-tf-rpi

### EfficientDet Object Detection for Raspberry Pi 3

## Motivation

Several existing repositories about setting up TensorFlow's Object Detection API in Raspberry Pi 3 have several compatibility issues with the latest Raspberry Pi OS (Bullseye).

This introduces a guide on how to set up the Raspberry Pi 3 (running Bullseye) to support TensorFlow's Object Detection API.

In addition, this may also serve as a guide on how camera devices such as the Pi Camera Module 2 can be made compatible with OpenCV, which needs to be used to display the inference. The Pi Camera Module 2 may not work with the legacy camera interface and the standard `picamera` module.

## Training the Model

Refer to [efficientdet-grain-tf](https://github.com/starkfire/efficient-grain-tf) on how you can train the model from a separate runtime. After cloning this repository, remember to pass/replace the contents of `/inference_graph` and `/training` to your copy of this repository after training the model.

## Pi Camera Setup

**Note: this guide was tested on the Pi Camera Module 2. This may not be an effective guide if you are using a USB camera.**

You may first need to check whether your camera device can be detected:

```sh
# vcgencmd
vcgencmd get_camera

# via V4L2 (apt install v4l-utils)
v4l2-ctl --list-devices

# libcamera
libcamera-still --list-cameras
```

Based on which one works for your camera device, you may want to check if images/videos can be captured. For example, if libcamera works well for your device, then a simple `libcamera-still` can help you confirm that.

**If your camera works well with libcamera, then this guide is for you.**

Camera devices such as the Pi Camera Module 2 may work well with libcamera. However, this is not supported by `picamera`. In this case, you want to use the `picamera2` module, which supports libcamera.

You need to set up the required development headers:

```sh
sudo apt install libcap2 libcap-dev
```

You also need to set up PyQt5, OpenGL, and other packages such as ffmpeg and prctl:
```sh
sudo apt install -y python3-pyqt5 python3-opengl python3-kms++ python3-prctl libatlas-base-dev ffmpeg
sudo apt install qtbase5-dev
pip install pyqt5
```

If you are having issues in installing PyQt5, you may try the following:

```sh
pip install pyqt5 --config-settings --confirm-license= --verbose
```

Then to set up picamera2:
```sh
sudo apt install -y python3-picamera2 python3-libcamera
pip install numpy picamera2 picamera2[gui]
```

Once you are done with picamera2, you want to set up [libcamera](https://github.com/raspberrypi/libcamera) from source:
```sh
# Meson
pip install meson

# libcamera core
apt install libyaml-dev python3-yaml python3-ply python3-jinja2
# if you are in a virtual environment:
pip install jinja2 ply pyyaml ninja

# IPA module signing
apt install libgnutls28-dev libssl-dev openssl

# lc-compliance
apt install libevent-dev

# libcamera build
git clone https://git.libcamera.org/libcamera/libcamera.git
cd libcamera
meson setup build
ninja -C build install
```

The following may also be helpful depending on your requirements:
```sh
# Debugging
apt install libdw-dev libunwind-dev

# Device Hotplug Enumeration
apt install libudev-dev

# Documentation (optional)
python3-sphinx doxygen graphviz texlive-latex-extra

# Gstreamer (if you want to use gstreamer)
libgstreamer1.0-dev libgstreamer-plugins-base1.0-dev

# Camera: KMS sink
apt install libdrm-dev
# Camera: MJPEG on SDL sink
apt install libjpeg-dev
# Camera: SDL link
apt install libsdl2-dev

# QCam
qtbase5-dev libqt5core5a libqt5gui5 libqt5widgets5 qttools5-dev-tools libtiff-dev

# Tracing with lttng
liblttng-ust-dev python3-jinja2 lttng-tools

# Android
libexif-dev libjpeg-dev
```

To verify your libcamera setup:
```sh
cam
```
