---
title: "[OpenCVSharp] 14. Template Matching"
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
  - Template Matching
last_modified_at: 2019-04-02T17:38:00-00:00
---

chapter 14. 탬플릿 객체 감지

![Template]({{ site.url }}{{ site.baseurl }}/assets/images/puzzle_small.jpg){: .align-center}
이번에는 지정된 탬플릿을 이용한 객체 감지에 대해 알아보자.

OpenCV MatchTemplate()함수와 MinMaxLoc()함수를 사용한다.

## 탬플릿 매칭

먼저 레퍼런스 이미지와 탬플릿 이미지를 로드 하고, result Mat를 구성한다.

```cs
//read image
using (Mat refMat = src.Clone())
using (Mat tplMat = ReadImage())
using (Mat res = new Mat(refMat.Rows - tplMat.Rows + 1, refMat.Cols - tplMat.Cols + 1, MatType.CV_32FC1))
{
    .....
}
```

Gray 이미지로 변환을 하고  MatchTemplate를 실행
```cs
//Convert input images to gray
Mat gref = refMat.CvtColor(ColorConversionCodes.BGR2GRAY);
Mat gtpl = tplMat.CvtColor(ColorConversionCodes.BGR2GRAY);

Cv2.MatchTemplate(gref, gtpl, res, TemplateMatchModes.CCoeffNormed);
Cv2.Threshold(res, res, 0.8, 1.0, ThresholdTypes.Tozero);
```

이후 MinMaxLoc() 함수를 통하여 매치하는 객체의 위치 값을 찾는다. 
하나의 객체를 찾는경우 max value가 가장 높은 것을 찾으면 되고, 여러개의 객체를 찾는경우 max value 임계치를 지정하여 반복문을 활용하면 된다.

```cs
while (true)
{
    double minval, maxval, threshold = 0.8;
    Point minloc, maxloc;
    Cv2.MinMaxLoc(res, out minval, out maxval, out minloc, out maxloc);

    if (maxval >= threshold)
    {
        //draw a rectangle of the matching area
        Rect r = new Rect(new Point(maxloc.X, maxloc.Y), new Size(tplMat.Width, tplMat.Height));
        Cv2.Rectangle(refMat, r, Scalar.Red, 2);

        //fill in the res mat so you don't find the same area again in the minmaxloc
        Cv2.FloodFill(res, maxloc, new Scalar(0));                    
    }
    else
    {
        break;
    }
}
```

전체 소스
```cs
private void btnTempDetection_Click(object sender, EventArgs e)
{
    //read image
    using (Mat refMat = src.Clone())
    using (Mat tplMat = ReadImage())
    using (Mat res = new Mat(refMat.Rows - tplMat.Rows + 1, refMat.Cols - tplMat.Cols + 1, MatType.CV_32FC1))
    {
        //Convert input images to gray
        Mat gref = refMat.CvtColor(ColorConversionCodes.BGR2GRAY);
        Mat gtpl = tplMat.CvtColor(ColorConversionCodes.BGR2GRAY);

        Cv2.MatchTemplate(gref, gtpl, res, TemplateMatchModes.CCoeffNormed);
        Cv2.Threshold(res, res, 0.8, 1.0, ThresholdTypes.Tozero);

        while (true)
        {
            double minval, maxval, threshold = 0.8;
            Point minloc, maxloc;
            Cv2.MinMaxLoc(res, out minval, out maxval, out minloc, out maxloc);

            if (maxval >= threshold)
            {
                //draw a rectangle of the matching area
                Rect r = new Rect(new Point(maxloc.X, maxloc.Y), new Size(tplMat.Width, tplMat.Height));
                Cv2.Rectangle(refMat, r, Scalar.Red, 2);

                //debug
                String msg = $"MinVal={minval.ToString()} MaxVal={maxval.ToString()} MinLoc={minloc.ToString()} MaxLoc={maxloc.ToString()} Rect={r.ToString()}";
                MessageBox.Show(msg);

                //fill in the res mat so you don't find the same area again in the minmaxloc
                //Rect outRect;
                //Cv2.FloodFill(res, maxloc, new Scalar(0), out outRect, new Scalar(0.1), new Scalar(1.0), FloodFillFlags.Link4);
                Cv2.FloodFill(res, maxloc, new Scalar(0));                    
            }
            else
            {
                break;
            }
        }

        using (new Window("refMat", WindowMode.AutoSize, refMat))
        using (new Window("tplMat", WindowMode.AutoSize, tplMat))
        using (new Window("gref", WindowMode.AutoSize, gref))
        using (new Window("gtpl", WindowMode.AutoSize, gtpl))
        {
            //Wait until user finishes (space or esc)
            while (true)
            {
                int c = Cv2.WaitKey(20);
                if ((char)c == 27 | (char)c == 32)
                {
                    //SetDstImage();
                    Cv2.DestroyAllWindows();
                    break;
                }
            }
        }
    }
}
```


탬플릿 이미지
![Template]({{ site.url }}{{ site.baseurl }}/assets/images/template.png){: .align-center}

결과
![Template]({{ site.url }}{{ site.baseurl }}/assets/images/opencvsharp-study-14_1.png){: .align-center}
