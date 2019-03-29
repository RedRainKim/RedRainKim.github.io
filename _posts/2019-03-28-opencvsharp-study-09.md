---
title: "[OpenCVSharp] 9. Edge Detection"
header:
  overlay_color: "#333"
categories:
  - OpenCVSharp  
tags:
  - OpenCV
  - OpenCVSharp
  - Computer Vision
  - C# programing
  - 영상처리
  - 컴퓨터비젼
  - Edge Detection
  - 가장자리 검출
last_modified_at: 2019-03-28T14:38:00-00:00
---

chapter 9. 가장자리 검출

가장자리 검출에 대하여 알아보자.

Edge Detection은 이미지 프로세싱에서 가장 기본 작업 중에 하나라고 볼 수 있다. 에지 검출이 잘되기 위해서는 이진화가 잘 되어야 한다.
통상적으로 이진화 과정 까지를 전처리 과정이라고 하는 듯 하다.

OpenCV에서 제공하는 Edge Detection 관련 함수들을 간략하게 알아보자.


## Sobel Edge Detection
Sobel() 함수는 수직방향 미분값과 수평방향 미분값을 이용하여 가장자리를 검출합니다.

자세한 내용은 [Sobel Operator](https://en.wikipedia.org/wiki/Sobel_operator){:target="_blank"} 참조.

OpenCV Sobel() 함수
```cs
void cv::Sobel(
	InputArray src,     //input Mat image
	OutputArray dst,    //output Mat image
	MatType ddepth,     //output image depth, see combinations; in the case of 8-bit input images it will result in truncated derivatives.
	int xorder,         //order of the derivative x.
	int yorder,         //order of the derivative y.
	int ksize = 3,      //size of the extended Sobel kernel. It must be 1, 3, 5 or 7.
	double scale = 1,   //optional scale factor for the computed derivative values, by default, no scaling is applied
	double delta = 0,   //optional delta value that is added to the results prior to storing them in det.
	BorderTypes borderType = BorderTypes.Reflect101 //pixel extrapolation method
)
```

예제
```cs
//variables
Mat dst = new Mat();
Mat sobelX = new Mat();
Mat sobelY = new Mat();
//sobel vertical 
using (Window srcWindow = new Window("Sobel Vertical", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    Cv2.Sobel(src, sobelX, MatType.CV_8U, 1, 0);
    srcWindow.Image = sobelX;
}
//sobel horizontal
using (Window srcWindow = new Window("Sobel Horizotal", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    Cv2.Sobel(src, sobelY, MatType.CV_8U, 0, 1);
    srcWindow.Image = sobelY;
}
//soble combined
using (Window srcWindow = new Window("Sobel", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    dst = sobelX + sobelY;
    srcWindow.Image = dst;
}

//Wait until user finishes (space or esc)
while (true)
{
    int c = Cv2.WaitKey(20);
    if ((char)c == 27 | (char)c == 32)
    {
        Cv2.DestroyAllWindows();
        break;
    }
}
```
결과화면
<figure class="third">
    <img src="/assets/images/opencvsharp-study-09_1.png">
    <img src="/assets/images/opencvsharp-study-09_2.png">
    <img src="/assets/images/opencvsharp-study-09_3.png">
    <figcaption>Virtical / Horizontal / Combined</figcaption>
</figure>




## Laplacian Edge Detection
Unlike the Sobel edge detector, the Laplacian edge detector uses only one kernel. It calculates second order derivatives in a single pass.

자세한 내용은 [Laplace Operator](https://en.wikipedia.org/wiki/Laplace_operator){:target="_blank"} 참조.


OpenCV Laplacian() 함수
```cs
void cv::Laplacian	(	
    InputArray 	src,    //source image
    OutputArray dst,    //desination image of the same size and the same number of channels as src
    int 	ddepth,     //desired depth of the destination image
    int 	ksize = 1,  //aperture size used to compute the second-derivative filters.
    double 	scale = 1,  //optional scale factor for the computed Laplacian values. By default, no scalling is applied
    double 	delta = 0,  //Optional delta value that is added to the results prior to storing them in dst.
    int 	borderType = BORDER_DEFAULT //pixel extrapolation method
)	
```

예제
```cs
using (Window srcWindow = new Window("Laplacian", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    Cv2.Laplacian(src, dst, MatType.CV_8U);
    srcWindow.Image = dst;
}
using (Window srcWindow = new Window("Src", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    srcWindow.Image = src;
}
//Wait until user finishes (space or esc)
while (true)
{
    int c = Cv2.WaitKey(20);
    if ((char)c == 27 | (char)c == 32)
    {
        Cv2.DestroyAllWindows();
        break;
    }
}
```

결과화면
<figure class="half">
    <img src="/assets/images/opencvsharp-study-09_4.png">
    <img src="/assets/images/opencvsharp-study-09_5.png">    
    <figcaption>source / Laplacian</figcaption>
</figure>



## Canny Edge Detection
Canny Edge Detector는 다단계 알고리즘을 사용하여 이미지에서 다양한 범위의 가장자리를 감지하는 가장자리 감지 연산자.

자세한 내용은 아래 링크 참조.
* [Canny edge detector](https://en.wikipedia.org/wiki/Canny_edge_detector){:target="_blank"}
* [Opencv Canny tutorial](https://docs.opencv.org/4.0.1/da/d22/tutorial_py_canny.html){:target="_blank"}
* [OpenCV Canny detector](https://docs.opencv.org/4.0.1/da/d5c/tutorial_canny_detector.html){:target="_blank"}


OpenCV Canny() 함수
```cs
void cv::Canny	(	
    InputArray 	image,      //8-bit input image
    OutputArray edges,      //output edge map; single channels 8-bit image, which has the same size as image
    double 	threshold1,     //fist threshold for the hysteresis procedure
    double 	threshold2,     //second threshold for the hysteresis procedure
    int 	apertureSize = 3,   //aperture size for the Sobel operator
    bool 	L2gradient = false  //a flag, 
)	
```

예제
```cs
// open source image
using (Window srcWindow = new Window("Src", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    srcWindow.Image = src;
}
// open canny image with trackbar
using (Window srcWindow = new Window("Canny", image: src, flags: WindowMode.AutoSize | WindowMode.FreeRatio))
{
    int ratio = 3;
    int lowThreshold = 100;

    var thresholdTrackbar = srcWindow.CreateTrackbar("threshold", value: lowThreshold, max: 200, callback: (pos) =>
    {
        Cv2.Canny(src, dst, pos, pos * ratio);
        srcWindow.Image = dst;
    });
    thresholdTrackbar.Callback.DynamicInvoke(lowThreshold);
}

//Wait until user finishes (space or esc)
while (true)
{
    int c = Cv2.WaitKey(20);
    if ((char)c == 27 | (char)c == 32)
    {
        Cv2.DestroyAllWindows();
        break;
    }
}
```

결과화면
<figure class="half">
    <img src="/assets/images/opencvsharp-study-09_6.png">
    <img src="/assets/images/opencvsharp-study-09_7.png">    
    <figcaption>source / Canny</figcaption>
</figure>