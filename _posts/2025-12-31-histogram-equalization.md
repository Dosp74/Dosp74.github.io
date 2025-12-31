---
title: "[컴퓨터 비전] Intensity Transformation - Histogram Equalization 구현하기"
date: 2025-12-31 18:33:00 +0900
last_mod: 2025-12-31
categories: [컴퓨터 비전]
tags:
  [
    컴퓨터 비전,
    opencv,
    Visual Studio,
    Intensity Transformation,
    Histogram Equalization,
  ]
---

![Image](/assets/images/2025-12-31/2025-12-31-histogram-equalization.png)

앞선 포스팅에서는 **Gamma Transformation** , **Negative Transformation** 과 같이 픽셀 단위로 밝기 값을 직접 변환하는 기법을 살펴보았다.

이번 포스팅에서는 이러한 단순 밝기 변환의 한계를 보완하는 **Histogram Equalization** 에 대해 다룬다.

## Histogram Equalization이 필요한 이유

Gamma Transformation과 같은 기법은 모든 픽셀에 동일한 변환 함수를 적용한다.

이로 인해 특정 밝기 구간에 픽셀이 몰려 있는 영상, 전체적으로 어둡거나 밝은 영상에서는 대비가 충분히 살아나지 않거나, 노이즈까지 같이 증폭되는 문제가 발생할 수 있다.

Histogram Equalization은 **영상의 밝기 분포 자체를 기준으로 변환 함수를 자동으로 생성** 하여 전체 대비를 고르게 펼치는 것을 목표로 한다.

## Histogram의 개념

히스토그램은 각 밝기 값이 영상에서 **몇 번 등장했는지** 를 나타낸다.

학습 단계이기 때문에 8비트 그레이스케일 영상과 0 ~ 255의 밝기 레벨을 기준으로 보고, 히스토그램은 다음과 같이 정의된다.

**Histogram[k] = 밝기 값 k를 가지는 픽셀의 개수**

이를 전체 픽셀 수로 나누면 **정규화된 히스토그램** 이 되며, 각 밝기 값이 등장할 확률을 의미한다.

## Histogram Equalization의 핵심 아이디어

Histogram Equalization은 정규화된 히스토그램의 **누적 분포 함수(CDF)** 를 이용한다.

CDF는 다음과 같은 의미를 가진다.

**밝기 값 r 이하의 픽셀이 전체에서 차지하는 비율**

이를 이용해 변환 함수를 정의하면,

![Image](/assets/images/2025-12-31/2025-12-31-histogram-equalization-formula.png)

L은 밝기 레벨 개수를 의미하며, 8비트 영상에서는 256이다.

그리고 p_r은 정규화된 히스토그램이다.

결과적으로 출력 영상의 히스토그램은 가능한 한 **균등한 분포** 를 갖도록 변환된다.

---

이번 예제는 두 단계로 구성된다.

### Step 1: 입력 영상 변환

- 원본 Lena 영상
- s = r / 2, 즉 전체적으로 어두운 영상 (lena1)
- s = 128 + r / 2, 즉 밝기 값이 중간 이상에 몰린 영상 (lena2)

### Step 2: Histogram Equalization 적용

- 원본 Lena 영상
- lena1
- lena2

각 영상에 Histogram Equalization을 적용하여 밝기 분포와 대비 변화를 비교한다.

![Image](/assets/images/2025-12-31/2025-12-31-histogram-equalization.png)

[소스 코드]

사용 언어: C++

환경: Microsoft Windows 10 Home, Visual Studio 2022, C:\opencv\...

```cpp
#include <opencv2/opencv.hpp>
#include <iostream>
#include <vector>
using namespace cv;
using namespace std;

// s = r/2
Mat halfTransform(const Mat& src) {
    Mat result(src.size(), CV_8UC1); // CV_8UC1 = 0 ~ 255 값의 1채널 이미지(매크로/타입/채널)

    // 모든 픽셀을 2중 for 문으로 순회
    for (int y = 0; y < src.rows; y++) {
        for (int x = 0; x < src.cols; x++) {
            uchar r = src.at<uchar>(y, x);

            // 밝기 값을 기존 밝기 값의 절반(r/2)으로 줄여 어둡게 함
            result.at<uchar>(y, x) = static_cast<uchar>(r / 2);
        }
    }

    // 변환한 이미지 반환
    return result;
}

// s = 128 + r/2
Mat halfPlus128Transform(const Mat& src) {
    Mat result(src.size(), CV_8UC1); // CV_8UC1 = 0 ~ 255 값의 1채널 이미지(매크로/타입/채널)

    // 모든 픽셀을 2중 for 문으로 순회
    for (int y = 0; y < src.rows; y++) {
        for (int x = 0; x < src.cols; x++) {
            uchar r = src.at<uchar>(y, x);
            int s = 128 + r / 2;

            // 8비트 오버플로우 방어
            if (s > 255) {
                s = 255;
            }

            // 밝기 값을 주어진 조건대로 변환
            result.at<uchar>(y, x) = static_cast<uchar>(s);
        }
    }

    // 변환한 이미지 반환
    return result;
}

// Histogram Equalization
Mat histogramEqualization(const Mat& src) {
    // 1. Histogram for Image Processing
    // Histogram[k] = 밝기값 k가 이미지에서 등장한 횟수
    // Histogram[100] = 20이라면 밝기값이 100인 픽셀이 20개 있다는 뜻
    vector<int> Histogram(256, 0);

    for (int y = 0; y < src.rows; y++) {
        for (int x = 0; x < src.cols; x++) {
            uchar r = src.at<uchar>(y, x);
            Histogram[r]++;
        }
    }

    // 2. 누적 히스토그램 구하기
    // Histogram[k] = 밝기값 k가 이미지에서 등장한 횟수로, 확률이 아님
    // 밝기값 0부터 i까지의 누적 픽셀 수 = 누적분포함수(Cumulative Distribution Function, CDF)를 구하는 과정
    vector<int> cdf(256, 0);
    cdf[0] = Histogram[0];

    // 히스토그램만 있으면 해당 밝기 값이 몇 번 나왔는지만 알 수 있음
    // 해당 픽셀보다 어두운 픽셀이 전체에서 몇 %인지 알아야 equalization을 할 수 있음

    for (int i = 1; i < 256; i++) {
        cdf[i] = cdf[i - 1] + Histogram[i];
    }

    // 0 ~ r까지의 픽셀 개수를 더해두면, cdf[r] / 전체 픽셀 수 = 0 ~ 1 사이의 누적 확률이 됨
    // 전체 픽셀 수
    int total = src.rows * src.cols;

    // 3. equalization
    // s_k = (L - 1) * cdf[k] / total (total로 나눠주어야 확률이 됨)
    // s_k는 새 밝기 값이며 cdf[k]가 크면 픽셀이 많이 모인 구간이므로 더 넓게 분배하여 밝기 대비를 증가시킴
    // cdf[k]가 작으면 픽셀이 상대적으로 덜 모인 구간으로, 덜 펼치면 됨

    // L - 1 = 255(출력 밝기의 최댓값)로 두고, Look Up Table(lut)을 만든다.
    // lut는 밝기 k를 넣었을 때 바로 새 밝기 s_k가 나오는 변환표이다.
    vector<uchar> lut(256);

    for (int k = 0; k < 256; k++) {
        // 0 ~ 1 사이의 누적 확률을 다시 0 ~ 255의 밝기값으로 되돌림
        double s = static_cast<double>(cdf[k]) / total * 255.0;

        // 8비트 언더/오버플로우 방어
        if (s < 0) {
            s = 0;
        }

        if (s > 255) {
            s = 255;
        }

        lut[k] = static_cast<uchar>(s + 0.5); // 반올림
    }

    // 4. 픽셀 매핑
    Mat result(src.size(), CV_8UC1);

    for (int y = 0; y < src.rows; y++) {
        for (int x = 0; x < src.cols; x++) {
            uchar r = src.at<uchar>(y, x);
            result.at<uchar>(y, x) = lut[r];
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

    // Step #1 레나 영상 변환
    Mat lena1 = halfTransform(src);
    Mat lena2 = halfPlus128Transform(src);

    // Step #2 원본 레나, lena1, lena2에 histogram equalization 적용
    Mat src_hev = histogramEqualization(src);
    Mat lena1_hev = histogramEqualization(lena1);
    Mat lena2_hev = histogramEqualization(lena2);

    imshow("source", src);
    imshow("source_histogram_equalization_version", src_hev);
    imshow("lena1", lena1);
    imshow("lena1_histogram_equalization_version", lena1_hev);
    imshow("lena2", lena2);
    imshow("lena2_histogram_equalization_version", lena2_hev);

    waitKey(0);

    return 0;
}
```

코드에 주석을 상세히 달아놨으니 참고하면 좋을 것 같다.

<br>

Histogram Equalization을 적용한 결과, 밝기 값이 특정 구간에 몰려 있던 영상에서도 전체 밝기 범위를 보다 고르게 사용하는 결과를 확인할 수 있었다.

특히 대비가 낮았던 lena1, lena2 영상에서도 경계와 디테일이 뚜렷하게 드러났다.

### 마무리

Histogram Equalization은 밝기 분포 자체를 기준으로 변환 함수를 설계하는 기법이다.

단순한 Intensity Transformation 기법들보다는 계산이 복잡하지만, 자동으로 대비를 개선할 수 있다는 장점이 있다.

다만 모든 영상에 항상 최적의 결과를 보장하지는 않으며, 국소적인 대비를 고려하는 기법이 추가로 사용되기도 한다.
