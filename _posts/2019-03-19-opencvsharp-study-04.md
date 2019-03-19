---
title: "[OpenCVSharp] 4. photo module methods"
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
last_modified_at: 2019-03-19T11:17:00-00:00
---

chapter 4. 사진 관련 함수들... 

OpenCV 예제중에 사진 보정 관련 함수들을 알아보겠습니다. 포토샵이나 라이트룸 같은 프로그램에서 사용하는 기능들과 같다고 봐야 겠네요.
복잡하지 않고 간략히 함수화 하여 제공하는 것들로 사진 보정 필터 기능들이 라고 이해 해도 될 것 같습니다.


아래 그림은 원본 이미지 입니다.
![photo methods original]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-018-1.png){: .align-center}


## EdgePreservingFilter, 가장자리 보존 필터

사진 보정에 대한 지식이 깊지가 않아 자세히는 알수 없으나, 에지 부분을 보존하면서 Blur()를 통한 노이즈 제거 기능으로 판단됩니다.


함수 내용은 아래와 같습니다.
![EdgePreservingFilter]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-017.png){: .align-center}

* 함수 사용 예제
```cs
    Mat normconv = new Mat();
    Mat recursFildered = new Mat();
    Cv2.EdgePreservingFilter(src, normconv, EdgePreservingMethods.NormconvFilter);
    Cv2.EdgePreservingFilter(src, recursFildered, EdgePreservingMethods.RecursFilter);
```

결과는 아래와 같습니다.
<figure class="half">
    <img src="/assets/images/opencvsharp-018-2.png">
    <img src="/assets/images/opencvsharp-018-3.png">
    <figcaption>좌:NormconvFilter 옵션 / 우:RecursFilter 옵션</figcaption>
</figure>



## DetailEnhance, 세부 묘사 향상 필터

이미지의 세부 내용을 향상 시켜주는 필터 함수입니다. 강한 대비 효과를 주는 기능 인 듯 합니다.


함수 내용은 아래와 같습니다.
![DetailEnhance]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-019.png){: .align-center}

* 함수 사용 예제
```cs
    Mat detailEnhance = new Mat();
    Cv2.DetailEnhance(src, detailEnhance);
```

결과는 아래와 같습니다.
<figure class="half">
    <img src="/assets/images/opencvsharp-018-1.png">
    <img src="/assets/images/opencvsharp-020.png">
    <figcaption>좌:원본이미지 / 우:DetailEnhance 적용</figcaption>
</figure>


## PencilSketch, 연필 스케치 필터

연필 스케치 효과 필터 함수입니다. 함수에서 Color와 Grayscale 두개의 값을 리턴합니다.


함수 내용은 아래와 같습니다.
![PencilSketch]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-021-1.png){: .align-center}

* 함수 사용 예제
```cs
    Mat pencil1 = new Mat();
    Mat pencil2 = new Mat();
    Cv2.PencilSketch(src, pencil1, pencil2);
```

결과는 아래와 같습니다.
<figure class="half">
    <img src="/assets/images/opencvsharp-021-2.png">
    <img src="/assets/images/opencvsharp-021-3.png">
    <figcaption>좌:Color / 우:Grayscale</figcaption>
</figure>


## Stylization, 스타일 필터

스타일 효과 필터 함수입니다. 


함수 내용은 아래와 같습니다.
![Stylization]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-022-1.png){: .align-center}

* 함수 사용 예제
```cs
    Mat stylized = new Mat();
    Cv2.Stylization(src, stylized);
```

결과는 아래와 같습니다.
<figure class="half">
    <img src="/assets/images/opencvsharp-018-1.png">
    <img src="/assets/images/opencvsharp-022-2.png">
    <figcaption>좌:원본 / 우:Stylization</figcaption>
</figure>




이상 예제에 있는 함수들을 알아 보았습니다.
마지막으로 전체 소스 내용 올려 드립니다. 참조 하십시요.
```cs
    Mat src = new Mat(FilePath.Image.Fruits, ImreadModes.Color);

    Mat normconv = new Mat();
    Mat recursFildered = new Mat();
    Cv2.EdgePreservingFilter(src, normconv, EdgePreservingMethods.NormconvFilter);
    Cv2.EdgePreservingFilter(src, recursFildered, EdgePreservingMethods.RecursFilter);

    Mat detailEnhance = new Mat();
    Cv2.DetailEnhance(src, detailEnhance);

    Mat pencil1 = new Mat(), pencil2 = new Mat();
    Cv2.PencilSketch(src, pencil1, pencil2);

    Mat stylized = new Mat();
    Cv2.Stylization(src, stylized);

    using (new Window("src", src))
    using (new Window("edgePreservingFilter - NormconvFilter", normconv))
    using (new Window("edgePreservingFilter - RecursFilter", recursFildered))
    using (new Window("detailEnhance", detailEnhance))
    using (new Window("pencilSketch grayscale", pencil1))
    using (new Window("pencilSketch color", pencil2))
    using (new Window("stylized", stylized))
    {
        Cv2.WaitKey();
    }
```


