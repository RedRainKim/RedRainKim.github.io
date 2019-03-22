---
title: "[OpenCVSharp] 8. Histogram Equalization (히스토그램 평준화)"
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
  - Histogram Equialization
last_modified_at: 2019-03-22T14:00:00-00:00
---

chapter 8. 히스토그램 평준화

이전에 히스토그램 그래프를 알아보았습니다.
이번엔 히스토그램 평준화, 균일화라는 histogram equailization을 알아보겠습니다.

히스토그램 평준화에 대해 자세한 정보가 필요하다면 아래 위키피디아 정보를 참조 하시기 바랍니다.


**Histogram Equalization :** [https://en.wikipedia.org/wiki/Histogram_equalization](https://en.wikipedia.org/wiki/Histogram_equalization){:target="_blank"}
{: .notice}


### Histogram Equalization

먼저 이전에 알아보았던 Histogram 내용 결과 화면입니다.
<figure class="half">
    <img src="/assets/images/opencvsharp-study-07_2.png">
    <img src="/assets/images/opencvsharp-study-07_3.png">
    <figcaption> Load image / Histogram </figcaption>
</figure>

이미지가 대체로 어둡다고 볼 수 있습니다. 이 이미지에 equalization을 해보겠습니다.

우선 OpenCV 에서 히스토그램 평준화를 하기 위해서는 EqualizeHist()함수를 사용할 수 있습니다.
함수 내용은 아래와 같습니다.

![Histogram Equialization]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-08_1.png){: .align-center}

예제 소스는 아래와 같습니다.
```cs
//grayscale
Mat gray = Cv2.ImRead(FilePath.Image.Lenna, ImreadModes.Grayscale);

//equalize the histogram
Mat hist_equalized_img = new Mat();
Cv2.EqualizeHist(gray, hist_equalized_img);

// Histogram view
const int Width = 260, Height = 200;
Mat render = new Mat(new Size(Width, Height), MatType.CV_8UC3, Scalar.All(255));
render = GetHistogram(hist_equalized_img);

using (new Window("src", WindowMode.AutoSize | WindowMode.FreeRatio, gray))
using (new Window("equalized", WindowMode.AutoSize | WindowMode.FreeRatio, hist_equalized_img))
using (new Window("histogram", WindowMode.AutoSize | WindowMode.FreeRatio, render))
{
    Cv2.WaitKey();
    Cv2.DestroyAllWindows();
}
```


결과화면
<figure class="half">
    <img src="/assets/images/opencvsharp-study-08_2.png">
    <img src="/assets/images/opencvsharp-study-08_3.png">
    <figcaption>Equalized / Histogram</figcaption>
</figure>



## Adaptive Histogram - CLAHE

일괄적인 평준화의 한계를 극복하는 Adaptive Histogram Equalization가 있습니다.
예전에 알아본 이미지 이진화 에서 다룬것 처럼 주변(Local) 정보를 활용하는 기법으로 평준화(Equalization) 효율을 향상 시켰습니다.
정확한 명칭은 CLAHE(Contrast Limited Adaptive Histogram Equalization) 입니다.
자세한 정보가 필요하다면 아래 위키피디아 정보를 참조 하시기 바랍니다.


**Adaptive Histogram Equalization :** [https://en.wikipedia.org/wiki/Adaptive_histogram_equalization#Contrast_Limited_AHE](https://en.wikipedia.org/wiki/Adaptive_histogram_equalization#Contrast_Limited_AHE){:target="_blank"}
{: .notice}


### CLAHE class
Adaptive Histogram Equalization(AHE)을 사용하기 위해서 OpenCV의 CLAHE Class를 사용합니다.
주로 클래스 인자인 Tile size값과 Cliplimit 값을 지정하여 보정을 하게 됩니다.

* Tile Size : 이웃 영역의 크기를 지정
* Clip limit : 히스토그램이 클리핑되는 값 (클립 한계 값)


아래 그림을 보면서 각 인자 변화에 따른 결과 차이를 이해 하시기 바랍니다.
<figure class="third">
    <img src="/assets/images/opencvsharp-study-08_4.png">
    <img src="/assets/images/opencvsharp-study-08_5.png">
    <img src="/assets/images/opencvsharp-study-08_6.png">
    <figcaption> cliplimit 2.0 / 8.0 / 14.0 </figcaption>
</figure>
<figure class="third">
    <img src="/assets/images/opencvsharp-study-08_7.png">
    <img src="/assets/images/opencvsharp-study-08_8.png">
    <img src="/assets/images/opencvsharp-study-08_9.png">
    <figcaption> TileSize 4x4 / 8x8 / 12x12 </figcaption>
</figure>


예제 소스
```cs
Mat gray = src.CvtColor(ColorConversionCodes.BGR2GRAY);
Mat dst1 = new Mat();
Mat dst2 = new Mat();
Mat dst3 = new Mat();
Mat dst4 = new Mat();
Mat dst5 = new Mat();
Mat dst6 = new Mat();


using (CLAHE clahe = Cv2.CreateCLAHE())
{
    clahe.ClipLimit = 2.0;
    clahe.Apply(gray, dst1);
    clahe.ClipLimit = 8.0;
    clahe.Apply(gray, dst2);
    clahe.ClipLimit = 14.0;
    clahe.Apply(gray, dst3);


    clahe.ClipLimit = 4.5;
    clahe.TilesGridSize = new Size(4, 4);
    clahe.Apply(gray, dst4);

    clahe.ClipLimit = 4.5;
    clahe.TilesGridSize = new Size(8, 8);
    clahe.Apply(gray, dst5);

    clahe.ClipLimit = 4.5;
    clahe.TilesGridSize = new Size(12, 12);
    clahe.Apply(gray, dst6);
}

using (new Window("gray", WindowMode.AutoSize | WindowMode.FreeRatio, gray))
using (new Window("clip2.0", WindowMode.AutoSize | WindowMode.FreeRatio, dst1))
using (new Window("clip8.0", WindowMode.AutoSize | WindowMode.FreeRatio, dst2))
using (new Window("clip14.0", WindowMode.AutoSize | WindowMode.FreeRatio, dst3))
using (new Window("tile4x4", WindowMode.AutoSize | WindowMode.FreeRatio, dst4))
using (new Window("tile8x8", WindowMode.AutoSize | WindowMode.FreeRatio, dst5))
using (new Window("tile12x12", WindowMode.AutoSize | WindowMode.FreeRatio, dst6))
{
    Cv2.WaitKey();
    Cv2.DestroyAllWindows();
}
```

이상으로 Histogram Equalization에 대해서 알아보았습니다. 