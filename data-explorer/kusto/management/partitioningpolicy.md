---
title: 데이터 분할 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 분할 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/10/2020
ms.openlocfilehash: 433d8786ad3664d02387efacd7dcd3865b4deb13
ms.sourcegitcommit: ddafa58deb79417bd6f36e8bb3ad106d375b63e1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/26/2020
ms.locfileid: "85448505"
---
# <a name="data-partitioning-policy"></a>데이터 분할 정책

분할 정책은 특정 테이블에 대해 [익스텐트 (데이터 분할)](../management/extents-overview.md) 가 분할 되어야 하는지 여부 및 방법을 정의 합니다.

정책의 주요 목적은 분할 된 열에 있는 값의 데이터 집합 범위를 좁히는 것으로 알려진 쿼리 성능을 향상 시키는 것이 고, 높은 카디널리티 문자열 열에는 집계/조인 하는 것입니다. 이로 인해 데이터 압축이 향상 될 수도 있습니다.

> [!CAUTION]
> 정책을 정의할 수 있는 테이블의 수에는 하드 코드 된 제한이 설정 되어 있지 않습니다. 그러나 모든 추가 테이블은 클러스터의 노드에서 실행 되는 백그라운드 데이터 분할 프로세스에 오버 헤드를 추가 합니다. 이로 인해 더 많은 클러스터 리소스가 사용 될 수 있습니다. 자세한 내용은 [모니터링](#monitoring) 및 [용량](#capacity)을 참조 하세요.

## <a name="partition-keys"></a>파티션 키

지원 되는 파티션 키 종류는 다음과 같습니다.

|종류                                                   |열 유형 |파티션 속성                    |파티션 값                                        |
|-------------------------------------------------------|------------|----------------------------------------|----------------------|
|[Hash](#hash-partition-key)                            |`string`    |`Function`, `MaxPartitionCount`, `Seed` | `Function`(`ColumnName`, `MaxPartitionCount`, `Seed`) |
|[균일 범위](#uniform-range-datetime-partition-key) |`datetime`  |`RangeSize`, `Reference`                | `bin_at`(`ColumnName`, `RangeSize`, `Reference`)      |

### <a name="hash-partition-key"></a>해시 파티션 키

테이블의 형식 열에 해시 파티션 키를 적용 하는 `string` 것은 대부분의 쿼리에서 같음 필터 (,)를 사용 `==` `in()` 하거나 `string` ,, 또는 등의 *큰 차원* (10M 이상의 카디널리티)의 특정 열에 집계/조인할 때 적합 합니다 `application_ID` `tenant_ID` `user_ID` .

* 해시 모듈로 함수는 데이터를 분할 하는 데 사용 됩니다.
* 동일한 파티션에 속하는 모든 동일 (분할 된) 익스텐트는 동일한 데이터 노드에 할당 됩니다.
* 유형이 같은 (분할 된) 익스텐트의 데이터는 해시 파티션 키를 기준으로 정렬 됩니다.
  * 테이블에 해시 파티션 키가 정의 되어 있는 경우에는 해당 키를 [행 순서 정책](roworderpolicy.md)에 포함할 필요가 없습니다.
* [순서 섞기 전략](../query/shufflequery.md)을 사용 하는 쿼리 및 `shuffle key` 에서 사용 되는 쿼리는 테이블의 해시 파티션 키에 사용 되는 것으로 `join` `summarize` 예상 됩니다 `make-series` . 클러스터 노드 간에 이동 하는 데 필요한 데이터 양이 크게 감소 하기 때문입니다.

#### <a name="partition-properties"></a>파티션 속성

* `Function`사용할 hash 모듈로 함수의 이름입니다.
  * 지원 되는 값은 `XxHash64` 입니다.
* `MaxPartitionCount`만들 파티션의 최대 수 (해시 모듈로 함수에 대 한 모듈로 인수)입니다.
  * 지원 되는 값은 범위 내에 `(1,1024]` 있습니다.
    * 값은 다음과 같아야 합니다.
      * 클러스터의 노드 수보다 큼
      * 열 카디널리티 보다 작습니다.
    * 값이 높을수록 클러스터의 노드에서 데이터 분할 프로세스의 오버 헤드가 증가 하 고 각 기간의 익스텐트 수가 커집니다.
    * 값으로 시작 하는 것이 좋습니다 `256` .
      * 위의 고려 사항에 따라 필요에 따라 또는 쿼리 성능 및 데이터 사후 수집의 분할 오버 헤드에 따라 값을 조정 합니다.
* `Seed`해시 값을 임의로 선택할 하는 데 사용할 값입니다.
  * 값은 양의 정수 여야 합니다.
  * 권장 값은 이며 `1` , 지정 되지 않은 경우 기본값입니다.

#### <a name="example"></a>예제

이라는 형식화 된 열에 대 한 해시 파티션 키 `string` `tenant_id` 입니다.
이 함수는 `XxHash64` 해시 함수를 사용 하며,의는와 기본값을 사용 `MaxPartitionCount` `256` `Seed` `1` 합니다.

```json
{
  "ColumnName": "tenant_id",
  "Kind": "Hash",
  "Properties": {
    "Function": "XxHash64",
    "MaxPartitionCount": 256,
    "Seed": 1
  }
}
```

### <a name="uniform-range-datetime-partition-key"></a>균일 범위 datetime 파티션 키

테이블에서 형식화 된 열에 균일 한 범위 datetime 파티션 키를 적용 하는 것 `datetime` 은 테이블의 데이터 수집이 열에 따라 정렬 되지 않는 경우에 적합 합니다. 범위 간에 데이터를 재조정 하 여 각 익스텐트가 제한 된 시간 범위에서 레코드를 포함 하 여 종료 되도록 하는 것이 유용할 수 있습니다. 단지 섞는 쿼리 시 열에 대 한 필터가 `datetime` 더 효율적입니다.

* 사용 된 파티션 함수는 [bin_at ()](../query/binatfunction.md) 되며 사용자 지정할 수 없습니다.

#### <a name="partition-properties"></a>파티션 속성

* `RangeSize``timespan`각 datetime 파티션의 크기를 나타내는 스칼라 상수입니다.
  다음을 수행 하는 것이 좋습니다.
  * 값으로 시작 `1.00:00:00` 합니다 (1 일).
  * 테이블에 병합할 수 없는 작은 익스텐트가 많이 있을 수 있으므로 보다 짧은 값을 설정 하지 마세요.
* `Reference`정렬 된 `datetime` datetime 파티션에 따라 고정 된 지정 시간을 나타내는 스칼라 상수입니다.
  * 로 시작 하는 것이 좋습니다 `1970-01-01 00:00:00` .
  * Datetime 파티션 키에 값이 있는 레코드가 있는 경우 `null` 해당 파티션 값은의 값으로 설정 됩니다 `Reference` .

#### <a name="example"></a>예제

코드 조각은 이라는 형식화 된 열에 대해 균일 한 datetime 범위 파티션 키를 보여 줍니다 `datetime` `timestamp` .
`datetime(1970-01-01)`각 파티션에 대해 크기를 사용 하 여를 참조 지점으로 사용 `1d` 합니다.

```json
{
  "ColumnName": "timestamp",
  "Kind": "UniformRange",
  "Properties": {
    "Reference": "1970-01-01T00:00:00",
    "RangeSize": "1.00:00:00"
  }
}
```

## <a name="the-policy-object"></a>정책 개체

기본적으로 테이블의 데이터 분할 정책은입니다 .이 `null` 경우 테이블의 데이터는 분할 되지 않습니다.

데이터 분할 정책에는 다음과 같은 주요 속성이 있습니다.

* **파티션 키**:
  * 테이블의 데이터를 분할 하는 방법을 정의 하는 [파티션 키](#partition-keys) 의 컬렉션입니다.
  * 테이블에는 다음 옵션 중 하나를 사용 하 여 최대 파티션 키가 있을 수 있습니다 `2` .
    * [해시 파티션 키](#hash-partition-key)1 개
    * 단일 [범위 datetime 파티션 키](#uniform-range-datetime-partition-key)입니다.
    * 하나의 [해시 파티션 키](#hash-partition-key) 와 하나의 [단일 범위 datetime 파티션 키](#uniform-range-datetime-partition-key)입니다.
  * 각 파티션 키에는 다음과 같은 속성이 있습니다.
    * `ColumnName`: `string ` -분할 되는 데이터에 따라 열의 이름입니다.
    * `Kind`: `string` 적용할 데이터 분할 종류 ( `Hash` 또는 `UniformRange` )입니다.
    * `Properties`: `property bag` -분할에 따라 수행 되는 매개 변수를 정의 합니다.

* **EffectiveDateTime**:
  * 정책이 적용 되는 UTC 날짜/시간입니다.
  * 이 속성은 선택 사항입니다. 지정 하지 않으면 정책이 적용 된 후에 정책이 데이터 수집 적용 됩니다.
  * 보존으로 인해 삭제 될 수 있는 유형이 아닌 (분할 되지 않은) 익스텐트는 테이블의 효과적인 일시 삭제 기간 중 90% 이상으로 생성 되므로 분할 프로세스에서 무시 됩니다.

### <a name="example"></a>예제

두 개의 파티션 키가 있는 데이터 분할 정책 개체입니다.
1. 이라는 형식화 된 열에 대 한 해시 파티션 키 `string` `tenant_id` 입니다.
    - 이 함수는 `XxHash64` 해시 함수를 사용 하며 `MaxPartitionCount` ,의 기본값은 256입니다 `Seed` `1` .
1. 라는 유형 열에 대 한 균일 한 datetime 범위 파티션 키 `datetime` `timestamp` 입니다.
    - `datetime(1970-01-01)`각 파티션에 대해 크기를 사용 하 여를 참조 지점으로 사용 `1d` 합니다.

```json
{
  "PartitionKeys": [
    {
      "ColumnName": "tenant_id",
      "Kind": "Hash",
      "Properties": {
        "Function": "XxHash64",
        "MaxPartitionCount": 256,
        "Seed": 1
      }
    },
    {
      "ColumnName": "timestamp",
      "Kind": "UniformRange",
      "Properties": {
        "Reference": "1970-01-01T00:00:00",
        "RangeSize": "1.00:00:00"
      }
    }
  ]
}
```

### <a name="additional-properties"></a>추가 속성

다음 속성은 정책의 일부로 정의 될 수 있지만 선택적 이며 변경 하지 않는 것이 좋습니다.

* **Minrowcountperoperation**:
  * 단일 데이터 분할 작업의 원본 익스텐트의 행 수 합계에 대 한 최소 대상입니다.
  * 이 속성은 선택 사항입니다. 기본값은 `0`합니다.

* **Maxrowcountperoperation**:
  * 단일 데이터 분할 작업의 원본 익스텐트의 행 수 합계에 대 한 최대 대상입니다.
  * 이 속성은 선택 사항입니다. 기본값은 이며 `0` 기본 대상은 500만 레코드입니다.
    * 5 m 보다 낮은 값을 설정 하는 것을 고려할 수 있습니다. 분할 작업은 작업당 매우 많은 양의 메모리/CPU를 사용 하는 것을 볼 수 있습니다 (#monitoring 참조).

## <a name="notes"></a>참고

### <a name="the-data-partitioning-process"></a>데이터 분할 프로세스

* 데이터 분할은 클러스터에서 사후 수집 백그라운드 프로세스로 실행 됩니다.
  * 지속적으로 수집 되는 테이블은 항상 분할 (유형이 다른 익스텐트) 할 수 있는 데이터의 "비상"이 있어야 합니다.
* 데이터 분할은 정책의 속성 값에 관계 없이 핫 익스텐트 에서만 실행 됩니다 `EffectiveDateTime` .
  * 콜드 익스텐트가 분할 되어야 하는 경우에는 [캐싱 정책을](cachepolicy.md)일시적으로 조정 해야 합니다.

#### <a name="monitoring"></a>모니터링

[. 진단 표시](../management/diagnostics.md#show-diagnostics) 명령을 사용 하 여 클러스터에서의 분할 상태를 모니터링할 수 있습니다.

```kusto
.show diagnostics
| project MinPartitioningPercentageInSingleTable, TableWithMinPartitioningPercentage
```

출력에는 다음이 포함 됩니다.

  * `MinPartitioningPercentageInSingleTable`: 클러스터에 데이터 분할 정책이 있는 모든 테이블에서 분할 된 데이터의 최소 비율입니다.
    * 이 백분율이 지속적으로 90% 미만으로 유지 되 면 클러스터의 파티션 용량 ( [용량](partitioningpolicy.md#capacity)참조)을 평가 합니다.
  * `TableWithMinPartitioningPercentage`: 분할 백분율이 위에 표시 된 테이블의 정규화 된 이름입니다.

[. Show 명령을](commands.md) 사용 하 여 분할 명령 및 해당 리소스 사용률을 모니터링 합니다. 예를 들면 다음과 같습니다.

```kusto
.show commands 
| where StartedOn > ago(1d)
| where CommandType == "ExtentsPartition"
| parse Text with ".partition async table " TableName " extents" *
| summarize count(), sum(TotalCpu), avg(tolong(ResourcesUtilization.MemoryPeak)) by TableName, bin(StartedOn, 15m)
| render timechart with(ysplit = panels)
```

#### <a name="capacity"></a>용량

* 데이터 분할 프로세스로 인해 더 많은 익스텐트가 생성 됩니다. 클러스터는 익스텐트 [병합 용량](../management/capacitypolicy.md#extents-merge-capacity)을 점진적으로 늘릴 수 있으므로 [익스텐트](../management/extents-overview.md) 병합 프로세스가 계속 될 수 있습니다.
* 수집 처리량이 많고 분할 정책이 정의 된 테이블이 충분히 많은 경우 클러스터는 범위 [파티션 용량](../management/capacitypolicy.md#extents-partition-capacity)을 점차적으로 늘릴 수 있으므로 [분할 익스텐트의 프로세스가](#the-data-partitioning-process) 계속 될 수 있습니다.
* 리소스를 너무 많이 사용 하지 않도록 이러한 동적 증가가 더 이상 사용 되지 않습니다. 전체적으로 사용 되는 경우에는 캡을 넘어 점차 증가 하는 것이 필요할 수 있습니다.
  * 용량을 [늘리면 클러스터의](../../manage-cluster-vertical-scaling.md)리소스 사용이 크게 증가 하는 경우 / [out](../../manage-cluster-horizontal-scaling.md)수동으로 또는 자동 크기 조정을 사용 하 여 클러스터를 확장할 수 있습니다.

### <a name="outliers-in-partitioned-columns"></a>분할 된 열의 이상 값

* 해시 파티션 키에 해당 하는 값 (예: 또는)이 다른 항목 보다 훨씬 더 많은 값을 포함 `null` `N/A` 하거나 데이터 집합에서 더 널리 사용할 수 있는 엔터티 (예:)를 나타내는 경우 `tenant_id` 클러스터의 노드 간에 분산 된 데이터 분산에 영향을 주어 쿼리 성능이 저하 될 수 있습니다.
* 균일 범위의 datetime 파티션 키에 열에 있는 대부분의 값 (예: 과거 또는 미래의 날짜/시간 값)에서 "far"로 충분 한 비율의 값이 있는 경우 데이터 분할 프로세스의 오버 헤드가 증가 하 고 클러스터에서 추적을 유지 해야 하는 작은 익스텐트가 많이 소요 될 수 있습니다.

이러한 두 경우 모두 데이터를 "수정" 하거나 수집 시간 전후에 데이터에 있는 관련 없는 레코드를 필터링 하 여 클러스터에서 데이터 분할의 오버 헤드를 줄여야 합니다. 예를 들어 [업데이트 정책을](updatepolicy.md)사용 합니다.

## <a name="next-steps"></a>다음 단계

[분할 정책 제어 명령을](../management/partitioning-policy.md) 사용 하 여 테이블에 대 한 데이터 분할 정책을 관리할 수 있습니다.
