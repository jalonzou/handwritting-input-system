# handwritting-input-system

## How to run

To run the program, make sure you have configured the following environment on your computer:

1. Install Xcode (v8.0 or above) on your Macbook. (Install Visual Studio is theoretically feasible, but may encounter other problems.)
2. Configure OpenCV environment on your laptop (our version is v3.1.0).
3. Plug in two usb cameras.
4. Open "Writing_Recog_System.xcodeproj" in Xcode.
5. You may configure "VideoCapture" parameter in main.cpp file (line 38-39)to enable our program to identify your cameras.
6. Place two cameras at the right position. One on top of your hand with appropriate distance and other on the left edge of your desk.
7. Run the project in Xcode and enjoy the innovated writing experience.

## Program Structure
The overall design of our program is illustrated below:
![structure pic](Demo/Sfigure1.png)

## Initiative and Design
The design idea of this system can be generally divided into two parts. In the first part we mainly consider those methods that can be used to trace the fingertip of a hand for the top camera. The second part focuses on figuring out a set of algorithms that serve to detect the finger touching result for the side camera. The functions to be implementing for these two parts are illustrated in details below:

### The first part
- Detect and trace the position of fingertip of the “longest” finger, that is, the fingertip with the highest position along y axis.
- Record the fingertip position for each frame and translate this set of points as drawing path.
- Be able to detect and drop “unrealistic” drawing points – the kind of points with an unrealistic position in a frame given the position of previous drawing points (usually caused by fingertip detection failure).

### The second part
- Detect and trace the position of fingertip of the “lowest” finger, that is, the fingertip with the lowest position along y axis.
- Automatically detect the height of plane so as to determine the touching threshold as well as guide user to place side camera on the correct level.
- Detect the touching state of finger. If a touching event is detected, informing drawing function to draw path on the canvas. If the touching event terminates, informing drawing function to stop drawing path.

## Method and Implementation
In our project, we need to implement (or call from OpenCV library) a series of image processing method so as to extract useful and meaning information from the original scene images captured by the camera system. In order to segment the target object (In our case, hand and fingertip) from the background, We made reference to some relevant papers and finally came up with a set of methods that, when combined into a specific order, can yield acceptable results.

For a quick look at the methods we adopted, we list them below (sorted by their actual order of applications):

Methods applying to fingertip detection:
- Skin color detection
- Median blur (Median filter)
- Dilation
- Contour finding
- Convex hull
- Target point reducing
- Target point filtering

Methods applying to plane height detection:
- Detect Region of Interest
- HSV Color Conversion
- Histogram Calculation
- Back projection
- Filter2D
- Contours Finding
- Extract the Largest Contour
- Draw contour and bounding box

For each aforementioned method, we will elaborate in the following part the function it serves and the purpose we introduce it into our program.

### Skin color detection
The general purpose this method is serving as is to identify objects in the scene image with skin color and extract them into a processed image. This is no doubt one of the most widely used methods (before the pervasion of CNN) when the target object is the part of human body.

There already existed a bulk of research conducted by scientists from various field with regards to figuring out a most accurate way to identify skin color from background and reduce the rate of misclassification. Based on our own experiment, the skin color can by more accurately extracted if we can take advantage of the color information captured by our camera system to a larger extent. After repetitive experiments, we finally decided to adopt the algorithm proposed by Nusirwan Anwar[1] which takes RGB, HCbCr and HSV into consideration.

In addition to the most popular mentioned RGB color model, some color spaces have their luminance component separated from the chromatic component, and they are known to possess higher discriminability between skin pixels and non-skin pixels over various illumination conditions. HCbCr and HSV are models of such kind. Figure2 and Figure3 shown below are images in HSV and HCbCr color mode converting from the primitive RGB image Figure1.

![RGB](Demo/Mfigure1)
![HSV](Demo/Mfigure2)
![HCbCr](Demo/Mfigure3)