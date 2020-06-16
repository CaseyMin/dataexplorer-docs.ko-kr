---
title: Azure 데이터 탐색기의 기계 학습 기능
description: Azure 데이터 탐색기의 근본 원인 분석을 위해 machine learning 클러스터링을 사용 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: 4665064a645ad0001251a0c7246c9d799ff6e638
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780493"
---
# <a name="machine-learning-capability-in-azure-data-explorer"></a>Azure 데이터 탐색기의 기계 학습 기능

Azure 데이터 탐색기는 빅 데이터 분석 플랫폼입니다. 서비스 상태, QoS 또는 작동 하지 않는 장치를 모니터링 하는 데 사용 됩니다. 기본 제공 [변칙 검색 및 예측](anomaly-detection.md) 함수는 비정상 동작을 확인 합니다. 이러한 패턴을 감지 하면 변칙을 완화 하거나 해결 하기 위해 RCA (근본 원인 분석)가 실행 됩니다.

진단 프로세스는 복잡 하 고 길고 도메인 전문가가 수행 합니다. 프로세스에는 다음이 포함 됩니다.
* 동일한 시간 프레임에 대 한 다른 소스의 추가 데이터 가져오기 및 조인
* 여러 차원에 대 한 값 분포의 변경 내용 검색
* 추가 변수 차트
* 도메인 지식 및 intuition을 기반으로 하는 기타 기술

이러한 진단 시나리오는 Azure 데이터 탐색기에서 일반적 이므로, 진단 단계를 용이 하 게 하 고 RCA의 지속 시간을 단축 하기 위해 machine learning 플러그 인을 사용할 수 있습니다.

Azure 데이터 탐색기에는 [`autocluster`](kusto/query/autoclusterplugin.md) , 및의 세 가지 Machine Learning 플러그 인이 있습니다. [`basket`](kusto/query/basketplugin.md) [`diffpatterns`](kusto/query/diffpatternsplugin.md) 모든 플러그 인은 클러스터링 알고리즘을 구현 합니다. `autocluster`및 플러그 인은 `basket` 단일 레코드 집합을 클러스터링 하 고 `diffpatterns` 플러그 인은 두 레코드 집합 간의 차이점을 클러스터링 합니다.

## <a name="clustering-a-single-record-set"></a>단일 레코드 집합 클러스터링

일반적인 시나리오에는 다음과 같은 특정 조건으로 선택 된 데이터 집합이 포함 되어 있습니다.
* 비정상 동작을 표시 하는 시간 창
* 높은 온도 장치 판독값
* 긴 기간 명령
* 상위 지출 사용자는 데이터에서 일반적인 패턴 (세그먼트)을 찾는 빠르고 쉬운 방법을 원합니다. 패턴은 레코드가 여러 차원 (범주 열)에 대해 동일한 값을 공유 하는 데이터 집합의 하위 집합입니다. 

다음 쿼리는 10 분 내에 주 기간 동안 시간 일련의 서비스 예외를 작성 하 고 표시 합니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5XPsaoCQQyF4d6nCFa7oHCtZd9B0F6G8ajByWTJZHS5+PDOgpVgYRn485EkOAnno9NAriWGFKw7QfQYUy0O43zZ0JNKFQnG/5jrbmeIXHBgwd6DjH2/JVqk2QrTL1aYvlifa4tni29YlzaiUK4yRK3Zu54006dBZ1N5/+X6PqpRI23+pFGGfIKRtz5egzk92K+dsycMyz3szhGEKWJ01lxI760O9ABuq0bMcvV2hqFoqnOz7F9BdSHlSgEAAA==)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m
| render timechart with(title="Service exceptions over a week, 10 minutes resolution")
```

![서비스 예외 시간 차트](media/machine-learning-clustering/service-exceptions-timechart.png)

서비스 예외 수는 전체 서비스 트래픽과 관련이 있습니다. 영업일, 월요일 ~ 금요일의 일 단위를 명확 하 게 볼 수 있습니다. 하루 중에는 서비스 예외 수가 증가 하 고 있으며 밤에는 수가 감소 합니다. 플랫 낮은 수가 주말에 표시 됩니다. Azure 데이터 탐색기에서 [시계열 변칙 검색](anomaly-detection.md#time-series-anomaly-detection) 을 사용 하 여 예외 급증을 감지할 수 있습니다.

데이터의 두 번째 스파이크는 화요일 오후에 발생 합니다. 다음 쿼리를 사용 하 여 sharp 스파이크 인지 여부를 자세히 진단 하 고 확인할 수 있습니다. 이 쿼리는 1 분 내에 8 시간의 고해상도에서 스파이크 주위에 차트를 다시 그립니다. 그런 다음 해당 테두리를 연구할 수 있습니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAAyXNwQrCMBAE0Hu/YvHUooWkghSl/yDoyUsJyWpCk2xJNnjx403pbeYwbzwyBBdnnoxiZBewHYS89GLshzNIeRWiuzUGA83al8yYXPzI5gdBLdjnWjFDLGHSVCK3HVCEe0LtMj4r9mAVVngnCvsLMO3hOFqo2goyVCxhNJhgu9dWJYavY9uyY4/T4UV1XVm2CEM0kFe34AnkBhXGOs7kCzuKh+4P3/XM5M8AAAA=)**\]**

```kusto
let min_t=datetime(2016-08-23 11:00);
demo_clustering1
| make-series num=count() on PreciseTimeStamp from min_t to min_t+8h step 1m
| render timechart with(title="Zoom on the 2nd spike, 1 minute resolution")
```

![스파이크 시간 차트에 집중](media/machine-learning-clustering/focus-spike-timechart.png)

15:00 ~ 15:02에서 2 분 정도의 좁은 스파이크가 표시 됩니다. 다음 쿼리에서는 2 분 동안 발생 한 예외를 계산 합니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVHIzcyLL0hNzI4vsU1JLEktycxN1TAyMDTTNbDQNTJWMDS1MjDQtObKASlNrCCk1AioNCU1Nz8+Oae0uCS1KDMv3ZCrRqE8I7UoVSGgKDU5szg1BKgvuCQxt0AhKbWkPDU1TwPhBj09hCWaQI3J+aV5JQACnQoRpwAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| count
```

|개수 |
|---------|
|972    |

다음 쿼리에서는 20 972 개의 예외를 샘플링 합니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4XOsQrCMBSF4b1Pccd2aLmJKKL4DoLu4doeNDSJJb1SBx/eOHV0/37OCVCKPrkJMjo9DaJQH1FbNruW963dkNkemJtjFX5U3v+oLXRAfLo+vGZF9uluqg8tD2TQOaP3M66lu6jEiW7QBUj1+qHr1pGmhCojyPIX7QHvzakAAAA=)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| take 20
```

| PreciseTimeStamp            | 지역 | ScaleUnit | DeploymentId                     | 추적점 | ServiceHost                          |
|-----------------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 2016-08-23 15:00:08.7302460 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:09.9496584 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 8d257da1-7a1c-44f5-9acd-f9e02ff507fd |
| 2016-08-23 15:00:10.5911748 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 100005     | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:12.2957912 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | f855fcef-ebfe-405d-aaf8-9c5e2e43d862 |
| 2016-08-23 15:00:18.5955357 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 9d390e07-417d-42eb-bebd-793965189a28 |
| 2016-08-23 15:00:20.7444854 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 6e54c1c8-42d3-4e4e-8b79-9bb076ca71f1 |
| 2016-08-23 15:00:23.8694999 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 36109      | 19422243-19b9-4d85-9ca6-bc961861d287 |
| 2016-08-23 15:00:26.4271786 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 36109      | 3271bae4-1c5b-4f73-98ef-cc117e9be914 |
| 2016-08-23 15:00:27.8958124 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 904498     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:32.9884969 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007007   | d5c7c825-9d46-4ab7-a0c1-8e2ac1d83ddb |
| 2016-08-23 15:00:34.5061623 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:37.4490273 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | f2ee8254-173c-477d-a1de-4902150ea50d |
| 2016-08-23 15:00:41.2431223 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 103200     | 8cf38575-fca9-48ca-bd7c-21196f6d6765 |
| 2016-08-23 15:00:47.2983975 | ncus   | su1       | e24ef436e02b4823ac5d5b1465a9401e | 423690590  | 00000000-0000-0000-0000-000000000000 |
| 2016-08-23 15:00:50.5932834 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007006   | 2a41b552-aa19-4987-8cdd-410a3af016ac |
| 2016-08-23 15:00:50.8259021 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 1002110    | 0d56b8e3-470d-4213-91da-97405f8d005e |
| 2016-08-23 15:00:53.2490731 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 36109      | 55a71811-5ec4-497a-a058-140fb0d611ad |
| 2016-08-23 15:00:57.0000946 | eus2   | su2       | 89e2f62a73bb4efd8f545aeae40d7e51 | 64038      | cb55739e-4afe-46a3-970f-1b49d8ee7564 |
| 2016-08-23 15:00:58.2222707 | scus   | su5       | 9dbd1b161d5b4779a73cf19a7836ebd6 | 10007007   | 8215dcf6-2de0-42bd-9c90-181c70486c9c |
| 2016-08-23 15:00:59.9382620 | scus   | su3       | 90d3d2fc7ecc430c9621ece335651a01 | 10007006   | 451e3c4c-0808-4566-a64d-84d85cf30978 |

### <a name="use-autocluster-for-single-record-set-clustering"></a>단일 레코드 집합 클러스터링에 autocluster () 사용

1000 개 이상의 예외가 있지만 각 열에 여러 값이 있으므로 일반적인 세그먼트를 찾는 것은 여전히 어렵습니다. [`autocluster()`](kusto/query/autoclusterplugin.md)다음 쿼리에서 볼 수 있듯이 플러그 인을 사용 하 여 일반 세그먼트의 짧은 목록을 즉시 추출 하 고 스파이크의 2 분 내에 관심 있는 클러스터를 찾을 수 있습니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQrCMBRF937FG5OhJYkoovQfBN1DbC8aTNqSvlgHP94IQkf3c+65AUzRD3aCe1hue8dgHyGM0rta7WuzIb09KCWPVfii7vUPNQXtEUfbhTwzkh9uunrTckcCnRI6P+NSvDO7ONEVvACDWD80zRqRRcTThVxa5DKPv00hP81KL1+4AAAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate autocluster()
```

| SegmentId | 개수 | 백분율 | 지역 | ScaleUnit | DeploymentId | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |
| 2 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 3 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |
| 4 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |

위의 결과에서 볼 수 있습니다. 가장 기준 세그먼트는 총 예외 레코드의 65.74%를 포함 하 고 4 개의 차원을 공유 합니다. 다음 세그먼트는 훨씬 일반적입니다. 9.67%의 레코드만 포함 하 고 3 개의 차원을 공유 합니다. 다른 세그먼트는 훨씬 더 일반적이 지 않습니다.

Autocluster는 여러 차원을 마이닝 하 고 흥미로운 세그먼트를 추출 하는 전용 알고리즘을 사용 합니다. "흥미로운" 것은 각 세그먼트가 레코드 집합과 기능 집합 모두에 대해 상당한 검사가 있음을 의미 합니다. 세그먼트도 달라져서 수 있습니다. 즉, 각 세그먼트는 다른 세그먼트와 다릅니다. 이러한 세그먼트 중 하나 이상이 RCA 프로세스와 관련이 있을 수 있습니다. 세그먼트 검토 및 평가를 최소화 하기 위해 autocluster는 작은 세그먼트 목록만 추출 합니다.

### <a name="use-basket-for-single-record-set-clustering"></a>단일 레코드 집합 클러스터링에 바구니 () 사용

[`basket()`](kusto/query/basketplugin.md)다음 쿼리에서 볼 수 있는 것 처럼 플러그 인을 사용할 수도 있습니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA4WOsQ6CMBgGd57iH9sB0tZojMZ3MNG9KfBFG1og7Y84+PDWidH9LncBTNGPdoYbLF96x2AfIYzSh1oda7MjvT8pJc9V+KHu/Q81Be0RJ9uFJTOSHx+6+tD6RAJdEzqfcS/ejV2cqQWvwCi2h6bZIrKIeLmwlBa1Lg9gIb9KJv2TswAAAA==)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
demo_clustering1
| where PreciseTimeStamp between(min_peak_t..max_peak_t)
| evaluate basket()
```

| SegmentId | 개수 | 백분율 | 지역 | ScaleUnit | DeploymentId | 추적점 | ServiceHost |
|-----------|-------|------------------|--------|-----------|----------------------------------|------------|--------------------------------------|
| 0 | 639 | 65.7407407407407 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 1 | 642 | 66.0493827160494 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |  |
| 2 | 324 | 33.3333333333333 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 0 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 3 | 315 | 32.4074074074074 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 | 16108 | e7f60c5d-4944-42b3-922a-92e98a8e7dec |
| 4 | 328 | 33.7448559670782 |  |  |  | 0 |  |
| 5 | 94 | 9.67078189300411 | scus | su5 | 9dbd1b161d5b4779a73cf19a7836ebd6 |  |  |
| 6 | 82 | 8.43621399176955 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |  |
| 7 | 68 | 6.99588477366255 | scus | su3 | 90d3d2fc7ecc430c9621ece335651a01 |  |  |
| 8 | 167 | 17.1810699588477 | scus |  |  |  |  |
| 9 | 55 | 5.65843621399177 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |  |
| 10 | 92 | 9.46502057613169 |  |  |  | 10007007 |  |
| 11 | 90 | 9.25925925925926 |  |  |  | 10007006 |  |
| 12 | 57 | 5.8641975308642 |  |  |  |  | 00000000-0000-0000-0000-000000000000 |

바구니는 항목 집합 마이닝에 대 한 *"Apriori"* 알고리즘을 구현 합니다. 레코드 집합의 적용 범위가 임계값을 초과 하는 모든 세그먼트를 추출 합니다 (기본값 5%). 세그먼트 0, 1 또는 2, 3과 같이 비슷한 세그먼트를 사용 하 여 더 많은 세그먼트가 추출 된 것을 볼 수 있습니다.

두 플러그 인 모두 강력 하 고 사용 하기 쉽습니다. 이러한 제한 사항은 레이블이 없는 자율 방식으로 단일 레코드 집합을 클러스터링 하는 것입니다. 추출 된 패턴이 선택한 레코드 집합, 비정상 레코드 또는 전역 레코드 집합의 특징을 지정 하는지 여부는 명확 하지 않습니다.

## <a name="clustering-the-difference-between-two-records-sets"></a>두 레코드 집합의 차이점 클러스터링

[`diffpatterns()`](kusto/query/diffpatternsplugin.md)플러그 인은 및의 제한을 극복 합니다 `autocluster` `basket` . `Diffpatterns`두 개의 레코드 집합을 사용 하 고 다른 기본 세그먼트를 추출 합니다. 한 집합은 일반적으로 조사 중인 비정상 레코드 집합을 포함 합니다. 하나는 및에서 분석 됩니다 `autocluster` `basket` . 다른 집합에는 참조 레코드 집합인 기준이 포함 됩니다.

아래 쿼리에서는 기본에 `diffpatterns` 포함 된 클러스터와는 다른 스파이크의 2 분 내에 흥미로운 클러스터를 찾습니다. 스파이크가 시작 되 면 기준선 창은 15:00 이전의 8 분으로 정의 됩니다. 이진 열 (AB)으로 확장 하 고 특정 레코드가 기준 또는 비정상 집합에 속하는지 여부를 지정 합니다. `Diffpatterns`두 클래스 레이블이 비정상 및 기준 플래그 (AB)에서 생성 된 감독 학습 알고리즘을 구현 합니다.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA42QzU+DQBDF7/wVcwOi5UtrmhJM4OzBRO9kWqbtpssuYacfGv94t0CrxFTd02by5jfvPUkMtVBlQ7gtOauQiUVNXhLFD5NoNknuIJ7Oo8hPHXmS4vEvaXKWWuoCDUmh6Jr8fj79Tv6HfOanEIbwRLgnQFhjAwviA5EC3hCcCYCq6gamEVsC1oB7LfoRt6iMYKEVvGtFQXfeNFKc7mXe2MjNVzl+mARR6lRU63Ipd4apFWodOx9w2FBL4D23tBSGXi3mhbG+OPPGVQTB+ITvg24dGN7vlN5JTxhc+dYAHZls4LzIxGr1k/B4iXcLbq50jfLNtd9i8OB2jD3KnW0dKstokG08Zby8uLbyCfX/tG46AgAA)**\]**

```kusto
let min_peak_t=datetime(2016-08-23 15:00);
let max_peak_t=datetime(2016-08-23 15:02);
let min_baseline_t=datetime(2016-08-23 14:50);
let max_baseline_t=datetime(2016-08-23 14:58); // Leave a gap between the baseline and the spike to avoid the transition zone.
let splitime=(max_baseline_t+min_peak_t)/2.0;
demo_clustering1
| where (PreciseTimeStamp between(min_baseline_t..max_baseline_t)) or
        (PreciseTimeStamp between(min_peak_t..max_peak_t))
| extend AB=iff(PreciseTimeStamp > splitime, 'Anomaly', 'Baseline')
| evaluate diffpatterns(AB, 'Anomaly', 'Baseline')
```

| SegmentId | CountA | CountB | PercentA | PercentB | PercentDiffAB | 지역 | ScaleUnit | DeploymentId | 추적점 |
|-----------|--------|--------|----------|----------|---------------|--------|-----------|----------------------------------|------------|
| 0 | 639 | 21 | 65.74 | 1.7 | 64.04 | eau | su7 | b5d1d4df547d4a04ac15885617edba57 |  |
| 1 | 167 | 544 | 17.18 | 44.16 | 26.97 | scus |  |  |  |
| 2 | 92 | 356 | 9.47 | 28.9 | 19.43 |  |  |  | 10007007 |
| 3 | 90 | 336 | 9.26 | 27.27 | 18.01 |  |  |  | 10007006 |
| 4 | 82 | 318 | 8.44 | 25.81 | 17.38 | ncus | su1 | e24ef436e02b4823ac5d5b1465a9401e |  |
| 5 | 55 | 252 | 5.66 | 20.45 | 14.8 | weu | su4 | be1d6d7ac9574cbc9a22cb8ee20f16fc |  |
| 6 | 57 | 204 | 5.86 | 16.56 | 10.69 |  |  |  |  |

가장 기준 세그먼트는로 추출 된 세그먼트와 동일 합니다 `autocluster` . 2 분 비정상 창에서의 검사는 65.74% 이기도 합니다. 그러나 8 분 기준 기간의 적용 범위는 1.7%입니다. 차이점은 64.04%입니다. 이러한 차이는 비정상 스파이크와 관련 된 것으로 보입니다. 이러한 가정을 확인 하려면 원본 차트를이 문제가 있는 세그먼트에 속하는 레코드와 다른 세그먼트의 레코드에 분할 합니다. 아래 쿼리를 참조 하세요.

**\[**[**쿼리를 실행 하려면 클릭 하십시오.**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA5WRsWrDMBCG9zzF4cmGGuJUjh2Ktw7tUkLTzuEsnRNRnRQkuSQlD185yRTo0EWIO913/J8MRWBttxE6iC5INOhzRey20owhktd2V8EZwsiMXv/Q9Dpfe5I60Idm2kTkQ1E8AczMxMLjf1h4/IN1PzY7Ax0jWQWBdomvhyF/p512FroOMsIxA0zdTdpKn1bHSzmMzbX8TAfjTkw2vqpLp69VpYQaatEogXOBsqrbtl5WDake6yabXWjkv7WkFxeuPGqG5VzWqhQrIUqx6B/L1WKB6aBViy01imT2ANnau94QT9c35xlNVqQAjF9UhpSHAtiRO+lGG/MCUoZ7CTB4x7ePie5mNbk4QDVn6E+ThUT0SQh5iGlM7tHHX4WFgLHOAQAA)**\]**

```kusto
let min_t = toscalar(demo_clustering1 | summarize min(PreciseTimeStamp));  
let max_t = toscalar(demo_clustering1 | summarize max(PreciseTimeStamp));  
demo_clustering1
| extend seg = iff(Region == "eau" and ScaleUnit == "su7" and DeploymentId == "b5d1d4df547d4a04ac15885617edba57"
and ServiceHost == "e7f60c5d-4944-42b3-922a-92e98a8e7dec", "Problem", "Normal")
| make-series num=count() on PreciseTimeStamp from min_t to max_t step 10m by seg
| render timechart
```

![' Diffpattern ' 세그먼트의 유효성을 검사 하는 중 시간 차트](media/machine-learning-clustering/validating-diffpattern-timechart.png)

이 차트를 통해 화요일 오후의 스파이크가 플러그 인을 사용 하 여 검색 된이 특정 세그먼트의 예외로 인해 발생 했을 수 있습니다 `diffpatterns` .

## <a name="summary"></a>요약

Azure 데이터 탐색기 Machine Learning 플러그 인은 많은 시나리오에서 유용 합니다. `autocluster`및는 `basket` 자율 학습 알고리즘을 구현 하 고 사용 하기 쉽습니다. `Diffpatterns`는 감독 된 학습 알고리즘을 구현 하 고 더 복잡 한 경우에는 RCA에 대 한 차별화 세그먼트를 추출 하는 것이 더 강력 합니다.

이러한 플러그 인은 임시 시나리오 및 자동 거의 실시간 모니터링 서비스에서 대화형으로 사용 됩니다. Azure 데이터 탐색기에서 시계열 변칙 검색 후에는 진단 프로세스가 수행 됩니다. 이 프로세스는 필요한 성능 표준을 충족 하도록 최적화 되어 있습니다.
