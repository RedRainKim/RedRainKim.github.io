---
title: "[OpenCVSharp] 5. Drawing methods"
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
last_modified_at: 2019-03-21T11:40:00-00:00
---

chapter 5. 그리기 관련 함수들... 

이번에는 간단한 그리기 함수들을 알아보도록 하겠습니다.

## Drwing line, 선 그리기 

이미지에 선을 그리기 위해서는 Line()함수를 사용합니다. Line() 함수는 아래와 같이 2개의 오버로드가 있습니다.
좌표데이터(x위치, y위치)를 int로 지정하거나, point로 하거나의 차이 입니다.

{% capture notice-2 %}
* Cv2.Line(InputOutputArray, Point, Point, Scalar, Int32, LineTypes, Int32)
* Cv2.Line(InputOutputArray, Int32, Int32, Int32, Int32, Scalar, Int32, LineTypes, Int32)
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>


함수 내용은 아래와 같습니다.
![Drawing Line]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_1.png){: .align-center}

예제 코드
```cs
//drawing line        
{
    Mat img = new Mat(500, 500, MatType.CV_8UC3);

    //red, thick3, antialias
    Cv2.Line(img, (Point)new Point2d(100, 100), (Point)new Point2d(400, 105), Scalar.Red, 3, LineTypes.AntiAlias);
    //green, thick5, link4
    Cv2.Line(img, (Point)new Point2d(100, 200), (Point)new Point2d(400, 205), Scalar.Green, 5, LineTypes.Link4);
    //blue, thick10, link8
    Cv2.Line(img, (Point)new Point2d(100, 300), (Point)new Point2d(400, 305), Scalar.Blue, 10, LineTypes.Link8);

    using (new Window("Draw Line", img))
    {
        Cv2.WaitKey();
        Cv2.DestroyAllWindows();
    }
}
```

결과
![Drawing Line]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_2.png){: .align-center}


## drawing ractangle, 사각형 그리기

이미지에 사각형을 그리기 위해서 Rectangle( )함수를 사용합니다. Rectangle() 함수는 4개의 오버로드가 있습니다.

{% capture notice-2 %}
* Rectangle(InputOutputArray, Rect, Scalar, Int32, LineTypes, Int32)
* Rectangle(Mat, Rect, Scalar, Int32, LineTypes, Int32)
* Rectangle(InputOutputArray, Point, Point, Scalar, Int32, LineTypes, Int32)
* Rectangle(Mat, Point, Point, Scalar, Int32, LineTypes, Int32)
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

그중에 마지막 함수형을 알아보겠습니다.
![Drawing rectangle]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_3.png){: .align-center}


예제 코드
```cs
//drawing ractangle
{
    Mat img = new Mat(500, 500, MatType.CV_8UC3);

    //red, thick3, link4
    Cv2.Rectangle(img, (Point)new Point2d(200, 50), (Point)new Point2d(300, 150), Scalar.Red, 3, LineTypes.Link4);
    //green, thick5, link8
    Cv2.Rectangle(img, (Point)new Point2d(200, 200), (Point)new Point2d(300, 300), Scalar.Green, 5, LineTypes.Link8);
    //blue, thick-1(fill), Antialias
    Cv2.Rectangle(img, (Point)new Point2d(200, 350), (Point)new Point2d(300, 450), Scalar.Blue, -1, LineTypes.AntiAlias);

    using (new Window("Draw Ractangle", img))
    {
        Cv2.WaitKey();
        Cv2.DestroyAllWindows();
    }
}
```

결과
![Drawing rectangle]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_4.png){: .align-center}


## Drawing Circle, 원 그리기

이미지에 원을 그리기 위해서 Circle()함수를 사용합니다. Circle() 함수는 2개의 오버로드가 있습니다.

{% capture notice-2 %}
* Circle(InputOutputArray, Point, Int32, Scalar, Int32, LineTypes, Int32)
* Circle(InputOutputArray, Int32, Int32, Int32, Scalar, Int32, LineTypes, Int32)
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

그중에 Point를 사용하는 함수형을 알아보겠습니다. 함수 인자로 원의 중심좌료, 반지름, 색상, 선두께, 선종류를 받습니다.
![Drawing circle]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_5.png){: .align-center}

예제 코드
```cs
//drawing circle
{
    Mat img = new Mat(500, 500, MatType.CV_8UC3);

    //red - circle(이미지, 원의 중심 좌표, 반지름, 색상, 선두께, 선종류)
    Cv2.Circle(img, (Point)new Point2d(300, 100), 100, Scalar.Red, 3, LineTypes.Link4);
    //green
    Cv2.Circle(img, (Point)new Point2d(200, 250), 120, Scalar.Green, 8, LineTypes.Link8);
    //blue
    Cv2.Circle(img, (Point)new Point2d(300, 400), 80, Scalar.Blue, -1, LineTypes.AntiAlias);

    using (new Window("Draw Circle", img))
    {
        Cv2.WaitKey();
        Cv2.DestroyAllWindows();
    }
}
```

결과
![Drawing circle]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_6.png){: .align-center}


## Drawing ellipse, 타원형 그리기

이미지에 타원형을 그리기 위해서 ellipse()함수를 사용합니다. ellipse() 함수는 2개의 오버로드가 있습니다.

{% capture notice-2 %}
* Ellipse(InputOutputArray, RotatedRect, Scalar, Int32, LineTypes)
* Ellipse(InputOutputArray, Point, Size, Double, Double, Double, Scalar, Int32, LineTypes, Int32)
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

그중에 Point를 사용하는 함수형을 알아보겠습니다. 함수인자로 원의 중심좌료, 회전각도, 호시작각도, 호종료각도, 색상, 선두께, 선종류를 받습니다.
![Drawing ellipse]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_7.png){: .align-center}

예제 코드
```cs
//drawing ellipse
{
    Mat img = new Mat(500, 500, MatType.CV_8UC3);
    double angle;

    //ellipse (이미지, 중심좌표 (장경·단경), 회전각도, 호 시작각도, 호 종료각도, 색상, 선두께, 선종류)
    //red, thick3, link4
    angle = 30;
    Cv2.Ellipse(img, (Point)new Point2d(400, 150), (Size)new Size(200, 100), angle, angle - 100, angle + 200, Scalar.Red, 3, LineTypes.Link4);

    //green, thick5, link8
    angle = 0;
    Cv2.Ellipse(img, (Point)new Point2d(200, 200), (Size)new Size(100, 100), angle, angle, angle + 360, Scalar.Green, 5, LineTypes.Link8);

    //blue, thick-1, antialias
    angle = 100;
    Cv2.Ellipse(img, (Point)new Point2d(200, 400), (Size)new Size(100, 200), angle, angle - 200, angle + 100, Scalar.Blue, -1, LineTypes.AntiAlias);

    using (new Window("Draw Ellipse", img))
    {
        Cv2.WaitKey();
        Cv2.DestroyAllWindows();
    }
}
```

결과
![Drawing ellipse]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-05_8.png){: .align-center}


이상으로 간략히 그리기 함수를 알아보았습니다.


