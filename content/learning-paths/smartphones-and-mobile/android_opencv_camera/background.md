---
# User change
title: "Background"

weight: 2

layout: "learningpathall"
---
OpenCV (Open Source Computer Vision Library) was designed for real-time computer vision applications. It is cross-platform by default and can be used in a variety of applications, including mobile. Modern mobile devices are equipped with advanced cameras and powerful processors, making them well-suited for performing complex computer vision solutions. When it comes to Android, OpenCV provides robust tools and interfaces to incorporate vision capabilities into applications, enhancing mobile technology with sophisticated image and video analysis features. This makes OpenCV aa great resource for developers aiming to leverage the full potential of mobile devices for real-time computer vision applications.

The cross-platform feature of OpenCV allows developers to create versatile applications that can run on multiple devices without significant modifications to the code. Specifically, OpenCV can be seamlessly integrated with Kotlin, a preferred language for Android development. Kotlin’s concise syntax and interoperability with Java make it easier to use OpenCV libraries and functions, resulting in more readable and maintainable code.

OpenCV offers a wide range of functions for different aspects of computer vision and image processing, including:
- Image and video capture
- Image filtering and transformations
- Feature detection and matching
- Object detection and recognition
- Machine learning integration

OpenCV is optimized for performance, utilizing native C++ code to ensure efficient processing. This is crucial for real-time applications, especially on mobile devices where resources can be limited. The library also supports multi-core processing to leverage modern processor architectures.

The OpenCV SDK for Android is designed to be easily integrated with Android Studio, the primary IDE for Android development. This integration includes tools for adding the OpenCV library to your project, managing dependencies, and configuring the development environment.

OpenCV simplifies the process of capturing and processing images and videos from the device’s camera. This includes:
- Accessing the camera feed
- Capturing frames in real-time
- Applying computer vision algorithms directly to the camera feed

Recently, OpenCV became available for arm64 through the Maven repository, enabling developers to further improve the performance of their applications. Here, you will learn how to use this to create and configure an Android project that uses OpenCV to retrieve images from the camera and process them.

The learning path is divided into three sections:
1. Create and configure your project to use OpenCV.
2. Extend the project to access the device’s camera and retrieve camera frames with OpenCV.
3. Process camera images using OpenCV.

You can find all the code used in this learning path [here](https://github.com/dawidborycki/Arm64OpenCVCamera.git)