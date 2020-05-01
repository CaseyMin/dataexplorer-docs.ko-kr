---
title: series_decompose_anomalies ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 series_decompose_anomalies ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 08/28/2019
ms.openlocfilehash: 51ac499690323b1d2bafb4dc20ab7773f5c99c63
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82618913"
---
# <a name="series_decompose_anomalies"></a>series_decompose_anomalies()

시리즈 분해 기반 변칙 검색 ( [series_decompose ()](series-decomposefunction.md)참조) 

계열 (동적 숫자 배열)을 포함 하는 식을 입력으로 사용 하 고 점수를 사용 하 여 비정상 요소를 추출 합니다.

**구문**

`series_decompose_anomalies (`*계열* `[, ` *임계값* `,` *Trend* *AD_method* *Seasonality* `,` *Seasonality_threshold* *Test_points* 계절성 추세`, ` Test_points`, ` AD_method Seasonality_threshold`,``])`

**인수**

* *Series*: 숫자 값의 배열인 동적 배열 셀입니다. 일반적으로 [Series](make-seriesoperator.md) 또는 [make_list](makelist-aggfunction.md) 연산자의 결과 출력입니다.
* *임계값*: 매우 강력한 이상 값을 검색 하기 위한 비정상 임계값, 기본값 1.5 (k 값)
* *계절성*: 다음 중 하나를 포함 하는 계절 분석을 제어 하는 정수입니다.
    * -1: 자동 검색 계절성 ( [series_periods_detect](series-periods-detectfunction.md)사용) [기본값] 
    * 0: 계절성 없음 (즉,이 구성 요소 추출 건너뛰기)
    * period: 예상 기간을 bin 단위 수로 지정 하는 양의 정수입니다. 예를 들어 계열이 1 시간 bin에 있으면 주간 기간은 168입니다.
* *추세*: 추세 분석을 제어 하는 문자열로, 다음 중 하나를 포함 합니다.    
    * "avg": 추세 구성 요소를 계열의 평균으로 정의 합니다 [기본값]
    * "none": 추세 없음,이 구성 요소 추출 건너뛰기 
    * "linefit": 선형 회귀를 사용 하 여 추세 구성 요소 추출
* *Test_points*: 0 [기본값] 또는 양의 정수 이며, 학습 (재발) 프로세스에서 제외할 계열의 끝에 있는 점의 수를 지정 합니다. 이 매개 변수는 예측 목적으로 설정 해야 합니다.
* *AD_method*: 남아 있는 시계열에서 변칙 검색 방법 ( [series_outliers](series-outliersfunction.md)참조)을 제어 하는 문자열입니다.    
    * " [ctukey": 사용자](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) 지정 10-90 백분위 범위를 사용 하는.
    * 표준 25-75 번째 백분위 수 범위를 사용 하는 백분위 수 범위를 사용 하는 " [고 키"](https://en.wikipedia.org/wiki/Outlier#Tukey's_fences) :.
* *Seasonality_threshold*: *계절성* 가 자동 검색으로 설정 된 경우 계절성 점수에 대 한 임계값입니다. 기본 `0.6` 점수 임계값은입니다 (자세한 내용은 [series_periods_detect](series-periods-detectfunction.md)참조).


**돌려**

 함수는 다음의 각 계열을 반환 합니다.

* `ad_flag`: (+ 1,-1, 0)을 포함 하는 삼항 계열입니다.
* `ad_score`: 변칙 점수
* `baseline`: 분해에 따른 계열의 예측 값입니다.

**알고리즘에 대 한 자세한 정보**

이 함수는 다음 단계를 따릅니다.
1. 각 매개 변수를 사용 하 여 [series_decompose ()](series-decomposefunction.md) 를 호출 하 여 기준선 및 잔차 시리즈를 만듭니다.
2. 잔차 시리즈에서 선택한 변칙 검색 방법과 [series_outliers ()](series-outliersfunction.md) 를 적용 하 여 ad_score 계열을 계산 합니다.
3. Ad_score에 임계값을 적용 하 여 ad_flag 계열을 계산 합니다.
 
**예제**

**1. 주간 계절성의 변칙 검색**

다음 예에서는 주간 계절성를 사용 하 여 계열을 생성 한 다음, 일부 이상 값을 추가 합니다. `series_decompose_anomalies`계절성를 자동으로 검색 하 고 반복 패턴을 캡처하는 기준선을 생성 합니다. 추가한 이상 값은 ad_score 구성 요소에서 명확 하 게 파악할 수 있습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 10.0, 15.0) - (((t%24)/10)*((t%24)/10)) // generate a series with weekly seasonality
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers.png" alt-text="기준선 및 이상 값을 보여 주는 주간 계절성" border="false":::

**2. 추세를 사용 하 여 주간 계절성의 변칙 검색**

이 예제에서는 이전 예제의 계열에 추세를 추가 합니다. 먼저 추세 `avg` 기본값 값 `series_decompose_anomalies` 이 평균을 취하고 추세를 계산 하지 않는 기본 매개 변수를 사용 하 여를 실행 합니다. 생성 된 기준선에 추세가 포함 되지 않고 추세를 계산 하지 않는 것을 볼 수 있습니다. 따라서 데이터에 삽입 된 이상 값 중 일부는 더 높은 분산으로 인해 검색 되지 않습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y)
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart   
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-outliers-with-trend.png" alt-text="추세와 함께 주간 계절성 이상 값" border="false":::

다음으로 동일한 예제를 실행 하지만 계열에서 추세를 예상 하므로 trend 매개 변수에서를 지정 `linefit` 합니다. 기준이 입력 계열에 훨씬 더 가까운 것을 볼 수 있습니다. 삽입 한 모든 이상 값이 검색 되 고 일부 거짓 긍정도 검색 됩니다 (임계값을 조정 하는 다음 예제 참조).

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and ongoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 1.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-linefit-trend.png" alt-text="Linefit 추세를 사용 하는 주간 계절성 이상" border="false":::

**3. 변칙 검색 임계값 조정**

이전 예제에서는 잘못 된 수 만큼의 소음이 발생 한 것으로 감지 되었습니다 .이 예제에서는 변칙 검색 임계값을 기본값인 1.5에서 2.5로 늘려 더 강력한 변칙만 검색 되도록 합니다. 이제 데이터에 삽입 된 이상 값만 검색 됨을 확인할 수 있습니다.

```kusto
let ts=range t from 1 to 24*7*5 step 1 
| extend Timestamp = datetime(2018-03-01 05:00) + 1h * t 
| extend y = 2*rand() + iff((t/24)%7>=5, 5.0, 15.0) - (((t%24)/10)*((t%24)/10)) + t/72.0 // generate a series with weekly seasonality and onlgoing trend
| extend y=iff(t==150 or t==200 or t==780, y-8.0, y) // add some dip outliers
| extend y=iff(t==300 or t==400 or t==600, y+8.0, y) // add some spike outliers
| summarize Timestamp=make_list(Timestamp, 10000),y=make_list(y, 10000);
ts 
| extend series_decompose_anomalies(y, 2.5, -1, 'linefit')
| extend series_decompose_anomalies_y_ad_flag = 
series_multiply(10, series_decompose_anomalies_y_ad_flag) // multiply by 10 for visualization purposes
| render timechart  
```

:::image type="content" source="images/series-decompose-anomaliesfunction/weekly-seasonality-higher-threshold.png" alt-text="비정상 임계값이 높은 주간 시리즈 이상" border="false":::

