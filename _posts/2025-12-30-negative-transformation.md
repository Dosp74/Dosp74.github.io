---
title: "[컴퓨터 비전] Intensity Transformation - Negative Transformation 구현하기"
date: 2025-12-30 14:25:00 +0900
last_mod: 2025-12-30
categories: [컴퓨터 비전]
tags:
  [
    컴퓨터 비전,
    opencv,
    Visual Studio,
    Intensity Transformation,
    Negative Transformation,
  ]
---

![Image](/assets/images/2025-12-30/2025-12-30-negative-transformation.png)

영상 처리에서 **밝기(Intensity)** 를 직접 변환하는 기법을 **Intensity Transformation** 이라고 소개했는데, 이번 포스팅에서는 Intensity Transformation 중 가장 단순한 형태인 **Negative Transformation(네거티브 변환)** 에 대해 다뤄보고자 한다.

## Negative Transformation

Negative Transformation은 영상의 밝기 값을 반전시키는 변환이다.

8비트 그레이스케일 영상에서 픽셀 값의 범위는 **0 ~ 255** 이며, Negative Transformation은 다음과 같은 수식으로 정의된다.

**s = L - 1 - r**

- r: 입력 픽셀의 밝기 값
- s: 출력 픽셀의 밝기 값
- L: 밝기 레벨의 개수 (8비트 영상에서는 256)

즉, 밝은 영역은 어두워지고, 어두운 영역은 밝아지면서 영상의 **흑백이 완전히 반전** 된다.

이는 어두운 배경에 묻힌 밝은 구조를 강조할 때 유용하며, 공식이 단순하기 때문에 구현 또한 직관적이다.

---

다음은 Lena.png 파일에 Negative Transformation(s = L - 1 - r)을 적용한 결과이다.

![Image](/assets/images/2025-12-30/2025-12-30-negative-transformation.png)

[소스 코드]

사용 언어: C++

환경: Microsoft Windows 10 Home, Visual Studio 2022, C:\opencv\...

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>
using namespace cv;
using namespace std;

Mat negativeTransform(const Mat& src) {
    Mat result(src.size(), CV_8UC1); // CV_8UC1 = 0 ~ 255 값의 1채널 이미지(매크로/타입/채널)

    for (int y = 0; y < src.rows; y++) {
        for (int x = 0; x < src.cols; x++) {
            uchar r = src.at<uchar>(y, x);

            // Negative Transformation s = L - 1 - r에서 흑백 반전을 최대로 적용하려면 8비트 최댓값인 256(L)을 사용
            result.at<uchar>(y, x) = 255 - r;
        }
    }

    return result;
}

int main() {
    Mat src = imread("Lena.png", IMREAD_GRAYSCALE);

    if (src.empty()) {
        cout << "이미지를 불러올 수 없습니다." << endl;

        return -1;
    }

    Mat Lena_negative = negativeTransform(src);

    imshow("source", src);
    imshow("negative", Lena_negative);

    waitKey(0);

    return 0;
}
```
