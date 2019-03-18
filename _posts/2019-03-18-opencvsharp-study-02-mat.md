---
title: "[OpenCVSharp] 2. Mat class..."
header:
  overlay_color: "#333"
categories:
  - OpenCVSharp  
tags:
  - OpenCV
  - OpenCVSharp
  - Computer Vision
  - C# programing
last_modified_at: 2019-03-18T16:54:00-00:00
---

chapter 2. Mat 클래스에 대하여...

OpenCV를 공부함에 있어 가장 기본 자료형인 Mat 클래스에 대한 이해가 필요합니다.
Mat 클래스는 이미지 파일 자료형으로 이미지 파일의 정보를 Matrix로 가지고 있는 구조체로 이해하면 좀 더 이해가 쉽습니다.
거기에 덧 붙여 여러 생성자, 함수, 속성들이 더해진 클래스로 상당히 많은 내용들이 있으며, 그 중 몇가지 기능을 살펴보도록 하겠습니다.

상세한 내용은 아래 링크 참조


**OpenCvSharp Docs - Mat Class :** [OpenCvSharp Docs - Mat Class](https://shimat.github.io/opencvsharp_docs/html/7b686c13-aaa7-5f3a-e2be-7da57895443f.htm){:target="_blank"}
{: .notice}



## Mat class 활용 - 1. Resize

처음 image를 load 한 후에 아래와 같이 Resize() 함수를 통행 간단히 크기를 조정할 수 있습니다.
```cs    
    Mat small = new Mat();
    Cv2.Resize(src, small, new Size(100, 100)); //src = inputArray, small = outputArray    
```

## Mat Class 활용 - 2. 위치 접근 속성(property[rowstart, rowend, colstart, colend])

Mat 속성 중에 이미지의 특정 위치에 접근 할 수 있는 기능을 살펴 보겠습니다.
![Mat Properties]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-006.png){: .align-center}

아래 예제는 최초 로드한 이미지의 축소본을 만들고, 열위치 10에서 110까지와 행위치 10에서 110까지에 그전에 만든 축소본 이미지를 대입시킵니다.
두번째는 열위치370에서 470까지와 행위치 400에서 500까지에 마찬가지로 축소본 이미지를 Transposes하여 대입시키는 예제 입니다.

```cs
    // Assign small image to mat
    Mat small = new Mat();
    Cv2.Resize(src, small, new Size(100, 100));
    src[10, 110, 10, 110] = small;
    src[370, 470, 400, 500] = small.T();
    // ↑ This is same as the following:
    //small.T().CopyTo(src[370, 470, 400, 500]);
```

<figure class="half">
    <img src="/assets/images/opencvsharp-007.png">
    <img src="/assets/images/opencvsharp-008.png">
    <figcaption>결과 화면</figcaption>
</figure>


또한 특정 위치 값을 변형 시킬 수 있습니다.
아래 예제는 특정위치의 값을 bit 변환하거나, 특정 color로 값을 대입시키는 예제입니다.
```cs
    // Get partial mat (similar to cvSetImageROI)
    Mat part = src[200, 400, 200, 360];
    // Invert partial pixel values
    Cv2.BitwiseNot(part, part);
    
    // Fill the region (50..100, 100..150) with color (128, 0, 0)
    part = src.SubMat(50, 100, 400, 450);
    part.SetTo(128);
```
<figure class="half">
    <img src="/assets/images/opencvsharp-007.png">
    <img src="/assets/images/opencvsharp-009.png">
    <figcaption>결과 화면</figcaption>
</figure>


## Mat class 활용 - 3. 위치 접근 속성(property[rowRange, colRange])
다음으로 형, 열 범위로 접근 하는 속성을 살펴보겠습니다.
![Mat Properties]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-010.png){: .align-center}


아래 예제는 특정 행, 열 부분에 blur 효과를 주고 그 이미지를 다른 행, 열 범위에 대입 시키는 예제입니다.
```cs
    /// <summary>
    /// Submatrix operations
    /// </summary>
    private void RowColRangeOperation()
    {
        Mat src = Cv2.ImRead(FilePath.Image.Lenna);

        Cv2.GaussianBlur(
            src.RowRange(100, 200),
            src.RowRange(200, 300),
            new Size(7, 7), 20);

        Cv2.GaussianBlur(
            src.ColRange(200, 300),
            src.ColRange(100, 200),
            new Size(7, 7), 20);

        using (new Window("RowColRangeOperation", src))
        {
            Cv2.WaitKey();
        }
    }
```
<figure class="half">
    <img src="/assets/images/opencvsharp-007.png">
    <img src="/assets/images/opencvsharp-011.png">
    <figcaption>결과 화면</figcaption>
</figure>

추가로 아래 예제는, 랜덤한 위치를 이동 시키는 예제와 특정영역에 constant 값을 임의로 변경하는 예제입니다.
```cs
    /// <summary>
    /// Submatrix expression operations
    /// </summary>
    private void RowColOperation()
    {
        Mat src = Cv2.ImRead(FilePath.Image.Lenna);

        Random rand = new Random();
        for (int i = 0; i < 200; i++)
        {
            int c1 = rand.Next(100, 400);
            int c2 = rand.Next(100, 400);
            Mat temp = src.Row[c1];
            src.Row[c1] = src.Row[c2];
            src.Row[c2] = temp;
        }

        src.Col[0, 50] = ~src.Col[450, 500];
        
        // set constant value (not recommended)
        //src.Row[450,460] = src.Row[450,460] * 0 + new Scalar(0,0,255);
        // recommended way
        src.RowRange(450, 460).SetTo(new Scalar(0, 0, 255));

        using (new Window("RowColOperation", src))
        {
            Cv2.WaitKey();
        }
    }
```
<figure class="half">
    <img src="/assets/images/opencvsharp-007.png">
    <img src="/assets/images/opencvsharp-012.png">
    <figcaption>결과 화면</figcaption>
</figure>


## Mat class 활용 - 4. Convert (Mat, Bitmap)

마지막으로 형변환에 대하여 알아보겠습니다.

* Mat to Bitmap는 아래와 같이 변환 할 수 있습니다.
```cs
    Bitmap bitmap = BitmapConverter.ToBitmap(img);
    Bitmap bitmap = img.ToBitmap();
```

* Bitmap to Mat는 아래와 같이 변환 할 수 있습니다.
```cs
    Mat converted = BitmapConverter.ToMat(bitmap);
    Mat converted = Mat.FromBitmap(bitmap);
```

이상 Mat 클래스에 대하여 간략히 알아보았습니다. 




