---
title: "[OpenCVSharp] 12. Shape Detection"
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
  - Shape Detection
last_modified_at: 2019-04-02T15:38:00-00:00
---

chapter 12. 형상 감지하기.

형태를 찾아 중심점을 그리고, 형상 타입을 구분하는 코드


## 중심점 찾기

```cs
try
{
    //image load 먼저 한 후, dst 에 복사.
    dst = src.Clone();

    //Conversion to grayscale.
    Mat gray = new Mat();
    if (src.Channels() == 3)
    {
        gray = src.CvtColor(ColorConversionCodes.BGR2GRAY);
    }
    else gray = src.Clone();

    //Blurring to reduce high frequency noise to make our contour detection process more accurate.
    Mat blurred = new Mat();
    blurred = gray.GaussianBlur(new Size(5, 5), 0);

    //Binarization of the image.             
    Mat threshold = new Mat();
    threshold = blurred.Threshold(60, 255, ThresholdTypes.Binary);

    //find contours
    Point[][] contours;
    HierarchyIndex[] hierarchyIndexes;
    Cv2.FindContours(
        image: threshold,
        contours: out contours,
        hierarchy: out hierarchyIndexes,
        mode: RetrievalModes.External,
        method: ContourApproximationModes.ApproxSimple);

    //loop over the contours
    foreach (var c in contours)
    {
        Moments m = Cv2.Moments(c);
        Point pnt = new Point(m.M10 / m.M00, m.M01 / m.M00); //center point
        Cv2.Circle(dst, pnt, 5, Scalar.Red, -1);                    
        string shape = GetShape(c); //*형상구분
        Cv2.PutText(dst, shape, pnt, HersheyFonts.HersheySimplex, 0.5, Scalar.Green, 2);
    }
    
    using (new Window("src", WindowMode.AutoSize, src))
    using (new Window("threshold", WindowMode.AutoSize, threshold))
    using (new Window("dst", WindowMode.AutoSize, dst))
    {
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
    }
}
catch (Exception ex)
{
    MessageBox.Show(ex.Message);
}     
```

> 참조 Threshold 임계값을 적용 후 이미지가 검정색 배경에 흰색 전경색으로 표시되는 것이 가장 좋다.




## 형상 분석하기

```cs
private string GetShape(Point[] c)
{
    string shape = "unidentified";
    double peri = Cv2.ArcLength(c, true);
    Point[] approx = Cv2.ApproxPolyDP(c, 0.04 * peri, true);

    
    if (approx.Length == 3) //if the shape is a triangle, it will have 3 vertices
    {
        shape = "triangle";
    }
    else if (approx.Length == 4)    //if the shape has 4 vertices, it is either a square or a rectangle
    {
        Rect rect;
        rect = Cv2.BoundingRect(approx);
        double ar = rect.Width / (double)rect.Height;

        if (ar >= 0.95 && ar <= 1.05) shape = "square";
        else shape = "rectangle";
    }
    else if (approx.Length == 5)    //if the shape has 5 vertice, it is a pantagon
    {
        shape = "pentagon";
    }
    else   //otherwise, shape is a circle
    {
        shape = "circle";
    }
    return shape;
}
```

![Shape Detection]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-12_1.png){: .align-center}

![Shape Detection]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-12_2.png){: .align-center}

![Shape Detection]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-12_3.png){: .align-center}
