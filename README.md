# openCL-object-detection-in-android-studio
Implementation of an object detection algorithm in an android studio emulator using mobileNet deep neural network and OpenCV.

This tutorial was originally written by OpenCV developers (https://docs.opencv.org/3.4/d0/d6c/tutorial_dnn_android.html), but some tricks are added here in order to solve some installation problems and enable the use of an emulated device using Android Studio.

This tutorial was written for the following versions of corresponding software:

Android Studio 3.1.4
OpenCV 3.4.3
Ubuntu 18.04

# Requirements
Download and install Android Studio from https://developer.android.com/studio.
Get the latest pre-built OpenCV for Android release from https://github.com/opencv/opencv/releases and unpack it (for example, opencv-3.4.3-android-sdk.zip).

Download MobileNet object detection model from https://github.com/chuanqi305/MobileNet-SSD.


# Installation
## 1. Create an empty Android Studio project
Open Android Studio. Start a new project. Let's address it as mobilenet_openCV_v3 and set company domain to mobilenet_example.opencv.org, as could be seen in the following image. You can chose different app name and company domain, but then you will have to take more care to respect your own name when copy-pasting code to your app.

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/createAndroidProject.png)

For the next new project setting steps, just leave it with the default options.

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/createNewProject2.png)

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/cnp3.png)

![img](https://github.com/Montroigenc/openCL-object-detection-in-android-studio/blob/master/cnp4.png)

smartphone camera to pc
enable camera in emulator

