---
title: Integrating OpenCV 2.4.6 with Qt 5.1
date: 2013-10-04 19:55:16
categories: OpenCV
---

In the previous post I’ve describe how to build opencv on a linux system. Let’s see how we can integrate Opencv with Qt. I’ll use a simple console application which will read and display an RGB image, to show the steps.
 1. Create a  new Qt console application.
 2. Add the following lines to the <application>.pro file
 ```
 INCLUDEPATH += /us/local/include/opencv
 LIBS += -L/usr/local/lib\
-lopencv_core\
-lopencv_imgproc\
-lopencv_highgui\
-lopencv_ml\
-lopencv_video\
-lopencv_features2d\
-lopencv_calib3d\
-lopencv_objdetect\
-lopencv_contrib\
-lopencv_legacy\
-lopencv_flann\
-lopencv_nonfree\
 ```
Here `INCLUDEPATH` points the compiler to fetch opencv header files.
`LIBS` points to the opencv libraries which our program make use of. We have to add all the required libraries depending on the functions that we use on our program. The built libs can be found under <path to opencv>/release/lib, which means we can put that as the libs reference location on the other hand we can use  /usr/local/include/opencv ; you might remember we added an install prefix option before executing make install command. It pre-pends CMAKE_INSTALL_PREFIX on to all install directories. Default value for this on linux is usr/local .
Now all the things are ready to use opencv in our application. Let’s write a simple application to test it.

``` c++
#include <QCoreApplication>
#include <cv.h>
#include<cxcore.h>
#include <highgui.h>
#include<iostream>

using namespace std;
using namespace cv;

int main(int argc, char *argv[])
{
    QCoreApplication a(argc, argv);
    Mat image;
    image = imread("<path to image file>");   // Read the file

       if(image.empty())                              // Check for invalid input
       {
           cout <<  "Could not open or find the image" << std::endl ;
           return -1;
       }

       namedWindow( "Display window", CV_WINDOW_AUTOSIZE );//Create a window for display.

       imshow( "Display window", image );   // Show our image inside it.
       waitKey(0);
    return a.exec();
}
```
