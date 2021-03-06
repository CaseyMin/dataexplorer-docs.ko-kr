---
title: 용량 정책-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 용량 정책을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: 6d85b5cbf6752924bbaa03d2b81732cd69be6239
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321627"
---
# <a name="capacity-policy"></a>용량 정책 

용량 정책은 클러스터에서 데이터 관리 작업의 계산 리소스를 제어 하는 데 사용 됩니다.

## <a name="the-capacity-policy-object"></a>용량 정책 개체

용량 정책은 다음으로 구성 됩니다.

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>수집 용량

|속성       |형식    |설명    |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 수집 작업 수에 대 한 최대값입니다.               |
|CoreUtilizationCoefficient         |double  |수집 용량을 계산할 때 사용할 코어 비율의 계수입니다. 계산 결과는 항상 다음으로 정규화 됩니다. `ClusterMaximumConcurrentOperations` <br> [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이 클러스터의 총 수집 용량은 다음과 같이 계산 됩니다. <br> 최소 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 최대 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 수집 작업에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-merge-capacity"></a>익스텐트 병합 용량

|속성                           |형식    |설명                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|MinimumConcurrentOperationsPerNode |long    |단일 노드에서 동시 익스텐트 병합/다시 작성 작업 수에 대 한 최소값입니다. 기본값은 1입니다. |
|MaximumConcurrentOperationsPerNode |long    |단일 노드에서 동시 익스텐트 병합/다시 작성 작업 수에 대 한 최대값입니다. 기본값은 3입니다. |

에 표시 된 대로 클러스터의 총 익스텐트 병합 용량은 [`.show capacity`](../management/diagnostics.md#show-capacity) 다음과 같이 계산 됩니다.

`Number of nodes in cluster` .x `Concurrent operations per node`

`Concurrent operations per node` `MinimumConcurrentOperationsPerNode` 병합 작업의 성공률은 90%를 초과 하는 경우의 유효 값은 [,] 범위에서 시스템에 의해 자동으로 조정 `MaximumConcurrentOperationsPerNode` 됩니다.

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 병합 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-purge-rebuild-capacity"></a>익스텐트 재작성 용량 제거

|속성                           |형식    |설명                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |단일 노드에서의 제거 작업에 대 한 동시 다시 빌드 익스텐트의 최대 값 |

클러스터의 총 익스텐트 재작성 용량 제거 (에 표시 된 대로 [`.show capacity`](../management/diagnostics.md#show-capacity) )는 다음을 통해 계산 됩니다.

`Number of nodes in cluster` .x `MaximumConcurrentOperationsPerNode`

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 병합 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="export-capacity"></a>용량 내보내기

|속성                           |형식    |설명                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 내보내기 작업 수에 대 한 최대값입니다.                                           |
|CoreUtilizationCoefficient         |double  |내보내기 용량을 계산할 때 사용할 코어 비율의 계수입니다. 계산 결과는 항상으로 정규화 됩니다 `ClusterMaximumConcurrentOperations` . |

에 표시 된 대로 클러스터의 총 내보내기 용량은 [`.show capacity`](../management/diagnostics.md#show-capacity) 다음과 같이 계산 됩니다.

최소 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 최대 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 노드가 세 개 이상인 클러스터에서 관리 노드는 내보내기 작업에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-partition-capacity"></a>익스텐트 파티션 용량

|속성                           |형식    |설명                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|ClusterMinimumConcurrentOperations |long    |클러스터의 동시 익스텐트 파티션 작업 수에 대 한 최소값입니다. 기본값: 1  |
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 익스텐트 파티션 작업 수에 대 한 최대값입니다. 기본값: 16 |

클러스터의 총 익스텐트 파티션 용량 (에서 표시 [`.show capacity`](../management/diagnostics.md#show-capacity) )입니다.

`Concurrent operations` `ClusterMinimumConcurrentOperations` `ClusterMaximumConcurrentOperations` 분할 작업의 성공률은 90%를 초과 하는 경우의 유효 값은 [,] 범위의 시스템에 의해 자동으로 조정 됩니다.

## <a name="materialized-views-capacity-policy"></a>구체화 된 뷰 용량 정책

변경 [클러스터 정책 용량](capacity-policy.md#alter-cluster-policy-capacity)을 사용 하 여 용량 정책을 변경 합니다. 이렇게 변경 하려면 `AllDatabasesAdmin` 권한이 필요 합니다.
이 정책은 구체화 된 뷰의 동시성 설정을 변경 하는 데 사용할 수 있습니다. 이 변경은 클러스터에 정의 된 구체화 된 뷰가 두 개 이상이 고 클러스터가 모든 뷰의 구체화를 유지할 수 없는 경우에 필요할 수 있습니다. 기본적으로, 동시성 설정은 구체화가 클러스터의 성능에 영향을 주지 않도록 비교적 낮습니다.

> [!WARNING]
> 구체화 된 보기 용량 정책은 클러스터의 리소스가 부족 한 경우에만 증가 해야 합니다 (낮은 CPU, 사용 가능한 메모리). 리소스가 제한 될 때 이러한 값을 늘려도 리소스가 고갈 되 고 클러스터의 성능에 잘못 영향을 줄 수 있습니다.

구체화 된 뷰 용량 정책은 클러스터의 [용량 정책의](#capacity-policy)일부 이며 다음 JSON 표현을 포함 합니다.

<!-- csl -->
``` 
{
   "MaterializedViewsCapacity": {
    "ClusterMaximumConcurrentOperations": 1,
    "ExtentsRebuildCapacity": {
      "ClusterMaximumConcurrentOperations": 50,
      "MaximumConcurrentOperationsPerNode": 5
    }
  }
}
```

### <a name="properties"></a>속성

속성 | 설명
|---|---|
|`ClusterMaximumConcurrentOperations` | 클러스터가 동시에 구체화할 수 있는 구체화 된 뷰의 최대 수입니다. 이 값은 기본적으로 1 이지만 구체화 자체 (단일 개별 뷰의 경우)는 여러 동시 작업을 실행할 수 있습니다. 클러스터에 구체화 된 뷰가 하나 이상 정의 되어 있고 클러스터의 리소스가 양호한 상태 이면이 값을 늘리는 것이 좋습니다. |
| `ExtentsRebuildCapacity`|  구체화 프로세스 중에 구체화 된 모든 뷰에 대해 실행 되는 동시 익스텐트 다시 작성 작업의 수를 결정 합니다. 가 1 보다 크므로 여러 뷰가 동시에 실행 되는 경우 `ClusterMaximumConcurrentOperation` 이 속성에 정의 된 할당량을 공유 합니다. 동시 익스텐트 다시 작성 작업의 최대 수는이 값을 초과 하지 않습니다. |

### <a name="extents-rebuild"></a>익스텐트 다시 빌드

익스텐트 다시 빌드 작업에 대 한 자세한 내용은 [구체화 된 뷰 작동 방식](materialized-views/materialized-view-overview.md#how-materialized-views-work)을 참조 하세요. 익스텐트 다시 작성의 최대 수는 다음을 통해 계산 됩니다.
    
```kusto
Maximum(`ClusterMaximumConcurrentOperations`, `Number of nodes in cluster` * `MaximumConcurrentOperationsPerNode`)
```

* 기본값은 50 총 동시성 재작성 수와 노드당 최대 5입니다.
* `ExtentsRebuildCapacity`정책은 상한 으로만 사용 됩니다. 사용 되는 실제 값은 현재 클러스터의 조건 (메모리, CPU)에 따라 시스템에 의해 동적으로 결정 되며 다시 작성 작업에 필요한 리소스의 양을 예측 합니다. 실제로 동시성은 용량 정책에 지정 된 값 보다 훨씬 더 낮을 수 있습니다.
    * `MaterializedViewExtentsRebuild`메트릭은 각 구체화 주기에서 다시 빌드된 익스텐트의 수에 대 한 정보를 제공 합니다. 자세한 내용은 [구체화 된 뷰 모니터링](materialized-views/materialized-view-overview.md#materialized-views-monitoring)을 참조 하세요.

## <a name="defaults"></a>기본값

기본 용량 정책의 JSON 표현은 다음과 같습니다.

```json
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MinimumConcurrentOperationsPerNode": 1,
    "MaximumConcurrentOperationsPerNode": 3
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  },
  "ExtentsPartitionCapacity": {
    "ClusterMinimumConcurrentOperations": 1,
    "ClusterMaximumConcurrentOperations": 16
  }
}
```

## <a name="control-commands"></a>제어 명령

> [!WARNING]
> 용량 정책을 변경 하기 전에 Azure 데이터 탐색기 팀에 문의 하세요.

* [`.show cluster policy capacity`](capacity-policy.md#show-cluster-policy-capacity)를 사용 하 여 클러스터의 현재 용량 정책을 표시 합니다.

* [`.alter cluster policy capacity`](capacity-policy.md#alter-cluster-policy-capacity)를 사용 하 여 클러스터의 용량 정책을 변경 합니다.

## <a name="throttling"></a>제한

Kusto는 다음과 같은 사용자 시작 명령에 대 한 동시 요청 수를 제한 합니다.

* Ingestions ( [여기](../../ingest-data-overview.md)에 나열 된 모든 명령이 포함 되어 있습니다.)
   * 제한은 [용량 정책](#capacity-policy)에 정의 된 대로입니다.
* 제거
   * 전역은 현재 클러스터당 하나의에 고정 되어 있습니다.
   * 제거 작업을 수행 하는 동안 동시 다시 작성 작업의 수를 확인 하기 위해 제거 다시 작성 용량을 내부적으로 사용 합니다. 제거 명령은이 프로세스 때문에 차단/제한 되지 않지만 다시 작성 용량 제거에 따라 더 빠르거나 더 느리게 작동 합니다.
* 내보내기
   * 제한은 [용량 정책](#capacity-policy)에 정의 된 대로입니다.

클러스터가 허용 된 동시 작업을 초과 하는 작업을 감지 하면 429, "제한 됨", HTTP 코드로 응답 합니다.
백오프 후 작업을 다시 시도 하세요.
