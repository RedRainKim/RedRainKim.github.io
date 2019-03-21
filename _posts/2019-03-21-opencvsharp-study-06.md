---
title: "[OpenCVSharp] 6. Binarization, 영상 이진화"
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
  - Binarization
  - Threshold
last_modified_at: 2019-03-21T11:40:00-00:00
---

chapter 6. 영상 이진화 (Binarization, Thresholding)


영상 혹은 이미지 처리에서 이진화에 대해... 정리를 좀 해볼가 합니다.
이진화는 주어진 임계값을 기준으로 밝으면 흰색으로, 어두우면 검은색으로 바꾸어 대비 하는 기능을 칭합니다. 
주로 어떤 객체를 분리하기 위해 사용되는 기법입니다.

이진화에 대한 자세한 내용은 다크프로그래머 님의 블로그를 참조 
* **다크프로그래머 님의 블로그 :** [https://darkpgmr.tistory.com/115](https://darkpgmr.tistory.com/115){:target="_blank"}

이미지 이진화는 OCR(Optical Character Recognition)에서 중요한 단계 입니다. 이미지 이진화에 사용되는 여러가지 방법이 있지만 모든 이미지에 
사용되는 단일 또는 최상의 방법이라는 것은 없습니다.
다양한 이진화 알고리즘에 대한 이해가 있어야 좋은 방법을 찾을 수 있다고 볼 수 있습니다.

## Binarization, 이진화

OpenCV에서 이진화를 위해서는 Threshold() 함수를 사용합니다. 함수 내용은 아래와 같습니다.
![Binary]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-06_1.png){: .align-center}

### * ThresholdTypes
Threshold 타입을 비교 해보겠습니다. 아래 예제 소스를 참조 하시기 바랍니다.


```cs
Mat src = frame.Clone();
Mat gray = src.CvtColor(ColorConversionCodes.BGR2GRAY);

//고정 임계값 처리
Mat bin_img = new Mat();
Mat bininv_img = new Mat();
Mat trunc_img = new Mat();
Mat tozero_img = new Mat();
Mat tozeroinv_img = new Mat();

Cv2.Threshold(gray, bin_img, 0, 255, ThresholdTypes.Binary | ThresholdTypes.Otsu);
Cv2.Threshold(gray, bininv_img, 0, 255, ThresholdTypes.BinaryInv | ThresholdTypes.Otsu);
Cv2.Threshold(gray, trunc_img, 0, 255, ThresholdTypes.Trunc | ThresholdTypes.Otsu);
Cv2.Threshold(gray, tozero_img, 0, 255, ThresholdTypes.Tozero | ThresholdTypes.Otsu);
Cv2.Threshold(gray, tozeroinv_img, 0, 255, ThresholdTypes.TozeroInv | ThresholdTypes.Otsu);            

//가변 임계값 처리
Mat adaptive_img = new Mat();
Cv2.AdaptiveThreshold(gray, adaptive_img, 255, AdaptiveThresholdTypes.GaussianC, ThresholdTypes.Binary, 7, 8);

using (new Window("src", gray))
using (new Window("binary", bin_img))
using (new Window("BinaryInv", bininv_img))
using (new Window("Trunc", trunc_img))
using (new Window("Tozero", tozero_img))
using (new Window("TozeroInv", tozeroinv_img))
using (new Window("adaptive", adaptive_img))            
{
    Cv2.WaitKey();
    Cv2.DestroyAllWindows();
}
```

결과 화면
<figure class="third">
    <img src="/assets/images/opencvsharp-study-06_2.png">
    <img src="/assets/images/opencvsharp-study-06_3.png">
    <img src="/assets/images/opencvsharp-study-06_4.png">
    <figcaption>좌측부터 Binary, BinaryInv, Trunc</figcaption>
</figure>
<figure class="third">
    <img src="/assets/images/opencvsharp-study-06_5.png">
    <img src="/assets/images/opencvsharp-study-06_6.png">
    <img src="/assets/images/opencvsharp-study-06_7.png">
    <figcaption>좌측부터 Tozero, TozeroInv, adaptive</figcaption>
</figure>




## Binarization - Extensions, 확장 이진화 기법들

Adaptive threashold 기법 중에 확장 된 추가 기법들을 알아보겠습니다. 적응형 이진화 기법은 기본적으로 주변 블록의 값을 활용하여 임계치를 설정하는 방법입니다.
여러가지 알고리즘 연구가 진행 중입니다. 
* Other Thresholding Algorithms
* Niblack Thresholding
* Bernsen Thresholding
* Abutaleb Thresholding
* Sauvola Thresholding
* PirahanSiah Adaptive Single thresholding based on PSNR ( www.pirahansiah.com )


### Binarizer Class

이번에는 OpenCV Extension에서 제공하고 있는 알고리즘을 확인 해 보겠습니다.

{% capture notice-2 %}
* Bernsen : Binarizes by Bernsen's method
* Niblack : Binarizes by Niblack's method (This is faster but memory-hogging)
* Nick    : Binarizes by Nick's method
* Sauvola : Binarizes by Sauvola's method (This is faster but memory-hogging)
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

1. bernsen
> Bernsen(Mat src, Mat dst, int kernelSize, byte constrastMin, byte bgThreshold);

2. Niblack
> Niblack(Mat src, Mat dst, int kernelSize, double k);

3. Nick
> Nick(Mat src, Mat dst, int kernelSize, double k);

4. Sauvola
> Sauvola(Mat src, Mat dst, int kernelSize, double k, double r);


예제 소스
```cs
using (var src = Cv2.ImRead(FilePath.Image.Binarization, ImreadModes.Grayscale))
using (var niblack = new Mat())
using (var sauvola = new Mat())
using (var bernsen = new Mat())
using (var nick = new Mat())
{
    int kernelSize = 51;

    Binarizer.Bernsen(src, bernsen, kernelSize, 50, 200);
    Binarizer.Niblack(src, niblack, kernelSize, -0.2);
    Binarizer.Nick(src, nick, kernelSize, -0.14);
    Binarizer.Sauvola(src, sauvola, kernelSize, 0.1, 64);

    using (new Window("src", WindowMode.AutoSize, src))
    using (new Window("Bernsen", WindowMode.AutoSize, bernsen))
    using (new Window("Niblack", WindowMode.AutoSize, niblack))
    using (new Window("Nick", WindowMode.AutoSize, nick))
    using (new Window("Sauvola", WindowMode.AutoSize, sauvola))
    {
        Cv2.WaitKey();
    }
}
```

결과는 아래와 같습니다.
<figure class="half">
    <img src="/assets/images/opencvsharp-study-06_8.png">
    <img src="/assets/images/opencvsharp-study-06_9.png">
    <figcaption>Bernsen / Niblack</figcaption>
</figure>
<figure class="half">
    <img src="/assets/images/opencvsharp-study-06_10.png">
    <img src="/assets/images/opencvsharp-study-06_11.png">
    <figcaption>Nick / Sauvola</figcaption>
</figure>


