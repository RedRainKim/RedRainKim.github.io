---
title: "[OpenCVSharp] 3. Image split and merge..."
header:
  overlay_color: "#333"
categories:
  - OpenCVSharp  
tags:
  - OpenCV
  - OpenCVSharp
  - Computer Vision
  - C# programing
last_modified_at: 2019-03-19T09:58:00-00:00
---

chapter 3. image 분리와 병합 기능

이번에 공부할 Sample은 이미지 분리와 병합입니다. 컬러이미지의 RGB 채널별 분리와, 병합을 할 수 있는 기능입니다.
OpenCV Split() 함수와 Merge() 함수가 있습니다.

<figure class="half">
    <img src="/assets/images/opencvsharp-013.png">
    <img src="/assets/images/opencvsharp-014.png">
    <figcaption>함수 설명</figcaption>
</figure>


## 채널 분리

먼저 Split()은 어떻게 사용하는지 아래 예제를 보겠습니다. 우선 Mat[] 배열을 선언 한뒤, Split() 함수로 값을 받아옵니다.
3개의 채널 데이터를 리턴 받게 되고, 각 채널은 순서대로 R, G, B 채널을 분리하여 planes에 리턴 받습니다.

```cs
    Mat src = new Mat(FilePath.Image.Lenna, ImreadModes.Color);

    // Split each plane
    Mat[] planes;
    Cv2.Split(src, out planes);

    Cv2.ImShow("planes 0", planes[0]);
    Cv2.ImShow("planes 1", planes[1]);
    Cv2.ImShow("planes 2", planes[2]);
    Cv2.WaitKey();
    Cv2.DestroyAllWindows();
```
실행 결과는 아래 그림과 같습니다.

<figure class="half">
    <img src="/assets/images/opencvsharp-015-1.png">
    <img src="/assets/images/opencvsharp-015-2.png">    
</figure>
<figure class="half">
    <img src="/assets/images/opencvsharp-015-3.png">
    <img src="/assets/images/opencvsharp-015-4.png">    
    <figcaption>채널 분리</figcaption>
</figure>

## 채널 병합

다음은 병합을 알아보겠습니다. 채널별 분리된 배열 데이터를 하나로 병합 하는 방법은 아래와 같습니다.
```cs
    // Merge
    Mat merged = new Mat();
    Cv2.Merge(planes, merged);
```

아래 전체 예제 소스를 살펴 보겠습니다.
먼저 이미지를 분리 한 후 G(Green) 채널만 Bit반전을 준뒤 다시 병합 하는 예제입니다.

```cs
    Mat src = new Mat(FilePath.Image.Lenna, ImreadModes.Color);

    // Split each plane
    Mat[] planes;
    Cv2.Split(src, out planes);

    Cv2.ImShow("planes 0", planes[0]);
    Cv2.ImShow("planes 1", planes[1]);
    Cv2.ImShow("planes 2", planes[2]);
    Cv2.WaitKey();
    Cv2.DestroyAllWindows();

    // Invert G plane
    Cv2.BitwiseNot(planes[1], planes[1]);

    // Merge
    Mat merged = new Mat();
    Cv2.Merge(planes, merged);

    Cv2.ImShow("src", src);
    Cv2.ImShow("merged", merged);
    Cv2.WaitKey();
    Cv2.DestroyAllWindows();
```

결과 화면은 아래와 같습니다.

<figure class="half">
    <img src="/assets/images/opencvsharp-016-1.png">
    <img src="/assets/images/opencvsharp-016-2.png">        
</figure>

채널을 분리하여 특정 작업을 해야 할때 유용할 듯 합니다. 특정색을 인식한다던지..뭐 그럴때 써먹을려나요? 