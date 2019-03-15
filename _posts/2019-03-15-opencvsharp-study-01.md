---
title: "[OpenCVSharp] OpenCV programing with C#"
header:
  overlay_color: "#333"
categories:
  - OpenCVSharp  
tags:
  - OpenCV
  - OpenCVSharp
  - Computer Vision
  - C# programing
last_modified_at: 2019-03-15T10:21:00-00:00
---

OpenCV 공부 이야기...




최근 컴퓨터 비젼 관련하여 필요하게 되어 완전 초보가 공부를 시작 해볼려고 합니다.
처음에는 Python으로 시작해볼까 싶었지만... 현재 주로 C#을 하고 있고, 기타 다른 프로그램들과의 연계성을 감안하여 C#으로 개발하고 싶어 자료를 좀 찾아보니... 
OpenCV 래퍼인 EmguCV 와 OpenCVSharp이 있네요. 

EmguCV는 라이센스 정책도 좀 그렇고... 기타 다른 경험자분들 의견등....을 감안 하여 OpenCVSharp으로 하기로 결정하였습니다.

OpenCV가 작년 말(2018-12-22) Version 4.0이 릴리즈 되었네요. 

인터넷에 있는 기존 자료들이 거의 대부분 3.x 버전으로 되어 있어서, 4.0버전으로 컨버젼 하면서 공부를 해볼가 합니다.

OepnCV 관련 사이트는 아래와 같습니다.

{% capture notice-2 %}
* **OpenCV 공식 :** [https://opencv.org](https://opencv.org){:target="_blank"}
* **OpenCV4.0 Document :** [https://docs.opencv.org/4.0.0/](https://docs.opencv.org/4.0.0/){:target="_blank"}
* **OpenCVSharp :** [https://github.com/shimat/opencvsharp/releases](https://github.com/shimat/opencvsharp/releases){:target="_blank"}
{% endcapture %}

<div class="notice">
  {{ notice-2 | markdownify }}
</div>

OpenCvSharp sample 자료를 먼저 참조 할 듯 합니다.

개발 내용 부분 포스트들은 개인적으로 기록을 남기기위한 초기의 취지도 그렇고, 사실 말주변도 별로 없고 해서 설명 부분은 거의 없을 거라 보여 집니다. ^^;

