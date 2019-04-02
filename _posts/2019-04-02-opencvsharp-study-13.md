---
title: "[OpenCVSharp] 13. Color Detection"
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
  - Color Detection
last_modified_at: 2019-04-02T17:38:00-00:00
---

chapter 13. 색상으로 객체 감지



![HSV]({{ site.url }}{{ site.baseurl }}/assets/images/hsv_color_solid_cylinder_alpha_lowgamma1.png){: .align-right}
색상으로 객체를 감지 하기 위해서 먼저 HSV로 변환을 진행 한다. HSV 형식은 픽셀의 색상 값을 유지하는 단일 채널(H)과 채도 및 밝기 수준을 유지하는 
두개의 다른 채널(S 및 V)를 갖기 때문에 RGB를 사용하는 것 보다 색상 처리에 훨씬 유용하다.


HSV는 "Hue", "Saturation", "Value" 속성을 사용하여 색상을 구분한다.
첫번째 Hue(색조)는 0 ~ 360까지의 각도로 0이 순수한 빨간색이고 120은 순수 녹색, 240은 순수 파란색입니다. 
두번째 Saturation(채도)는 색상이 얼마나 강한지 말한다.
세번째 Value(값)은 색상이 얼마나 밝고 어두운지 구분한다.

OpenCV에서 HSV 포맷은 부호없는 8비트 값으로 저장되며, saturation 및 value는 0 ~ 255까지이며, Hue 경우는 0~360d에서 절반 값, 즉 0~180까지의 범위로 저장됨.

![HSV]({{ site.url }}{{ site.baseurl }}/assets/images/color-colors-wheel-names-degrees-rgb-hsb-hsv-hue-78027630.jpg){: .align-center}



## 빨강색 객체 찾기

### HSV 변환
```cs
//converting color space to HSV
Mat hsv = new Mat();
Cv2.CvtColor(src, hsv, ColorConversionCodes.BGR2HSV); 
```

### 색상 지정하여 찾기 - 복수 구간 찾기
Hue 색상을 지정하여 inrange()함수로 특정 색상 구간을 찾는다.

```cs
//color thresholding - red
Mat lowerRedHueRange = new Mat();
Mat upperRedHueRange = new Mat();
Cv2.InRange(hsv, new Scalar(0, 100, 100), new Scalar(30 / 2, 255, 255), lowerRedHueRange);
Cv2.InRange(hsv, new Scalar(330 / 2, 100, 100), new Scalar(360 / 2, 255, 255), upperRedHueRange);

//combine the above two images
Mat redHueImage = new Mat();
Cv2.AddWeighted(lowerRedHueRange, 1.0, upperRedHueRange, 1.0, 0.0, redHueImage);
```


### 노이즈 제거

```cs
//noise reduce
Cv2.GaussianBlur(redHueImage, redHueImage, new Size(5, 5), 2, 2);

Mat str_el = Cv2.GetStructuringElement(MorphShapes.Rect, new Size(3, 3));
Cv2.MorphologyEx(redHueImage, redHueImage, MorphTypes.Open, str_el);
Cv2.MorphologyEx(redHueImage, redHueImage, MorphTypes.Close, str_el);
```

### Find contours and drawing...

```cs
//find contours
Point[][] contours;
HierarchyIndex[] hierarchyIndexes;
Cv2.FindContours(
    redHueImage,
    out contours,
    out hierarchyIndexes,
    mode: RetrievalModes.External,
    method: ContourApproximationModes.ApproxSimple);

//drawing contours
if (contours.Length == 0)
{
    MessageBox.Show("Couldn't find any object in the image.");
}
else
{
    //draw contours - default all
    Cv2.DrawContours(dst, contours, -1, Scalar.Green, 2);

    //display option
    string msg = "Found contours : [" + contours.Length.ToString() + "]";
    Cv2.PutText(dst, msg, new Point(5, 35), HersheyFonts.HersheyComplex, 1.0, Scalar.Red, 1, LineTypes.AntiAlias);
}
```

결과
<figure class="half">
    <img src="/assets/images/opencvsharp-study-13_1.png">
    <img src="/assets/images/opencvsharp-study-13_2.png">    
    <figcaption>source / HSV</figcaption>
</figure>

<figure class="half">
    <img src="/assets/images/opencvsharp-study-13_3.png">
    <img src="/assets/images/opencvsharp-study-13_4.png">    
    <figcaption>lower / upper</figcaption>
</figure>

<figure class="half">
    <img src="/assets/images/opencvsharp-study-13_5.png">
    <img src="/assets/images/opencvsharp-study-13_6.png">    
    <figcaption>Combine / final</figcaption>
</figure>

