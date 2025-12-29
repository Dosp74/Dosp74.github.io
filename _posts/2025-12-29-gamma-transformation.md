---
title: "[컴퓨터 비전] Intensity Transformation - Gamma Transformation 구현하기"
date: 2025-12-29 21:50:00 +0900
last_mod: 2025-12-29
categories: [컴퓨터 비전]
tags:
  [
    컴퓨터 비전,
    opencv,
    Visual Studio,
    Intensity Transformation,
    Gamma Transformation,
  ]
---

![Image](/assets/images/2025-12-29/2025-12-29-gamma-transformation.png)

영상 처리에서 **밝기(Intensity)** 를 직접 변환하는 기법을 **Intensity Transformation** 이라고 한다.

이 기법은 공간적인 위치 관계를 고려하지 않고, **각 픽셀의 밝기 값 자체를 함수로 변환** 하는 방식이다.

이번 포스팅에서는 여러 Intensity Transformation 중 가장 자주 사용되는 **Gamma Transformation(감마 변환)** 에 대해 다루려고 한다.

## Intensity Transformation

Intensity Transformation은 다음과 같은 형태로 표현할 수 있다.

**g(x, y) = T[f(x, y)]**

- f(x, y): 입력 영상(input image)
- g(x, y): 출력 영상(output image)
- T: 밝기 변환 함수(intensity transformation function)

즉, **각 픽셀의 밝기 값 r을 변환 함수 T에 넣어 새로운 밝기 값 s를 만드는 과정** 이다.

공간 좌표 `(x, y)` 주변 픽셀을 고려하지 않기 때문에 **픽셀 단위** 로 분류된다.

## Power-Law(Gamma) Transformation

Gamma Transformation은 **Power-Law Transformation** 이라고도 불리며, 다음과 같은 수식으로 정의된다.

![Image](/assets/images/2025-12-29/2025-12-29-gamma-transformation-formula.png)

**s = cr^γ**

- r: 입력 밝기 값 (0 ~ 1로 정규화)
- s: 출력 밝기 값
- γ: 감마 값
- c: 상수 (보통 1로 둠)

예를 들어, 어두운 톤의 영상에 감마 값 0.2를 채택한다면 어떻게 될까?

어두운 곳의 변화를 증폭시켜 정상화할 수 있을 것이다.

영상 밝기 값이 8비트라면 0 ~ 255 범위지만, 수식을 다루기 편하게 하기 위해 정규화하여 계산한다. 어떤 픽셀 값이 128이라면 계산할 때는 128 / 255 = 0.5로 바꾸는 것이다. 변환 s = cr^γ는 이러한 정규화된 값 r(0 ~ 1)에 대해 적용한다. 연산이 끝난 뒤에는 다시 0 ~ 255 범위로 되돌려 저장하거나 화면에 표시한다.

따라서 γ > 1이면 r^γ < r이므로 밝은 쪽이 눌리면서 전체가 더 어두워지고, γ < 1이면 r^γ > r이므로 어두운 입력도 커져 전체가 밝아진다. 지수 함수의 개념을 떠올리면 이해가 쉬울 것이다.

### 정리

**γ 값에 따른 특징**

γ < 1이면 어두운 영역이 강조된다. 즉 전체적으로 밝아진다.

γ = 1이면 입력과 출력이 동일하다.

γ > 1이면 밝은 영역이 억제된다. 즉 전체적으로 어두워진다.

이러한 특성 때문에 감마 변환은 디스플레이 보정이나 의료, 위성, 야간 영상 처리 등에서 매우 자주 사용된다.

하지만 감마 변환은 모든 픽셀의 밝기 값을 동일한 함수로 변환하기 때문에, 픽셀 값과 함께 **노이즈 역시 같이 증폭되거나 감소하는 문제** 가 있다.

특히 γ < 1을 적용하여 어두운 영역을 강조할 경우, 어두운 영역에 존재하던 노이즈까지 함께 커지면서 영상의 품질이 오히려 저하될 수도 있다.

이러한 문제를 해결하기 위해서는 선명하게 하고자 하는 영역만 선택적으로 강조하고, 노이즈가 많이 포함된 영역은 변환의 영향을 최소화하는 보다 정교한 영상 처리 알고리즘이 필요하다.

---

다음은 γ = 0.5, γ = 1.5를 각각 적용한 결과이다.

![Image](/assets/images/2025-12-29/2025-12-29-gamma-transformation.png)

[소스 코드]

사용 언어: C++

환경: Microsoft Windows 10 Home, Visual Studio 2022, C:\opencv\...

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>
#include <cmath>
using namespace cv;
using namespace std;

Mat gammaTransform(const Mat& src, double gamma) {
    Mat result(src.size(), CV_8UC1); // CV_8UC1 = 0 ~ 255 값의 1채널 이미지(매크로/타입/채널)

    for (int y = 0; y < src.rows; y++) {
        for (int x = 0; x < src.cols; x++) {
            // 0 ~ 255 -> 0 ~ 1로 정규화
            double r = static_cast<double>(src.at<uchar>(y, x)) / 255.0;

            // s = cr^gamma (c = 1)
            double s = pow(r, gamma);

            // 다시 0 ~ 255 범위로 변환
            result.at<uchar>(y, x) = static_cast<uchar>(s * 255.0);
        }
    }

    return result;
}

int main() {
    Mat src = imread("Lena.png", IMREAD_GRAYSCALE);

    if (src.empty()) {
        cout << "이미지를 열 수 없습니다." << endl;

        return -1;
    }

    Mat gamma05 = gammaTransform(src, 0.5);
    Mat gamma15 = gammaTransform(src, 1.5);

    imshow("source", src);
    imshow("Gamma: 0.5", gamma05);
    imshow("Gamma: 1.5", gamma15);

    waitKey(0);

    return 0;
}
```

8비트 그레이스케일 영상에서 픽셀 값의 범위는 0 ~ 255이다. 하지만 감마 변환 공식은 0 ~ 1 범위의 실수 값을 기준으로 정의되므로 다음과 같은 과정을 거친다.

1. 입력 픽셀 값을 0 ~ 1로 정규화
2. 감마 변환 공식 적용
3. 다시 0 ~ 255 범위로 복원

γ = 0.5일 때는 어두운 영역의 픽셀 값이 크게 증가하는 것을 볼 수 있다.
전체적으로 밝아졌으며 디테일이 잘 드러난다.

반면 γ = 1.5일 때는 밝은 영역이 억제되는 것을 볼 수 있다.
전체적으로 어두워졌으며 밝기가 감소되었다.

같은 영상이라도 γ 값에 따라 완전히 다른 인상을 주는 결과가 나온다는 점이 인상적이다.

### 마무리

Gamma Transformation은 픽셀 단위 밝기 변환 기법이다.

γ 값에 따라 밝기 분포를 비선형적으로 조절할 수 있다.

영상의 디테일을 강조하거나, 디스플레이를 보정하는 등에 쓰이는 기법이다.

이미지 출처

광운대학교 이윤구 교수님 강의자료  
Rafael C. Gonzalez & Richard E. Woods, Digital Image Processing (2nd Edition, Prentice Hall)
