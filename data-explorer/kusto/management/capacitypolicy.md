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
ms.openlocfilehash: bb3ee687e995af7d4161ca111f9efbe91c1b9ca0
ms.sourcegitcommit: a60ad8da32f16c5d9ce35b62e7331d7439081e3d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/06/2020
ms.locfileid: "84466309"
---
# <a name="capacity-policy"></a>용량 정책

용량 정책은 클러스터의 데이터 관리 작업에 사용 되는 계산 리소스를 제어 하는 데 사용 됩니다.

## <a name="the-capacity-policy-object"></a>용량 정책 개체

용량 정책은 다음으로 구성 됩니다.

* [IngestionCapacity](#ingestion-capacity)
* [ExtentsMergeCapacity](#extents-merge-capacity)
* [ExtentsPurgeRebuildCapacity](#extents-purge-rebuild-capacity)
* [ExportCapacity](#export-capacity)
* [ExtentsPartitionCapacity](#extents-partition-capacity)

## <a name="ingestion-capacity"></a>수집 용량

|속성                           |Type    |Description                                                                                                                                                                               |
|-----------------------------------|--------|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 수집 작업 수에 대 한 최대 값                                                                                                            |
|CoreUtilizationCoefficient         |double  |수집 용량을 계산할 때 사용할 코어 비율의 계수 (계산 결과는 항상에 의해 정규화 됨 `ClusterMaximumConcurrentOperations` ) |                                                                                                                             |

클러스터의 총 수집 용량 ( [. show capacity](../management/diagnostics.md#show-capacity))은 다음과 같이 계산 됩니다.

최소 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 최대 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 수집 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-merge-capacity"></a>익스텐트 병합 용량

|속성                           |Type    |Description                                                                                                |
|-----------------------------------|--------|-----------------------------------------------------------------------------------------------------------|
|MinimumConcurrentOperationsPerNode |long    |단일 노드에서 동시 익스텐트 병합/다시 작성 작업 수에 대 한 최소값입니다. 기본값: 1 |
|MaximumConcurrentOperationsPerNode |long    |단일 노드에서 동시 익스텐트 병합/다시 작성 작업 수에 대 한 최대값입니다. 기본값: 5 |

클러스터의 총 익스텐트 병합 용량 ( [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이)은 다음을 통해 계산 됩니다.

`Number of nodes in cluster`.x`Concurrent operations per node`

의 유효 값은 `Concurrent operations per node` [,] 범위에서 시스템에 의해 자동으로 `MinimumConcurrentOperationsPerNode` 조정 `MaximumConcurrentOperationsPerNode` 됩니다.


> [!Note]
> * 노드가 세 개 이상인 클러스터에서는 관리 노드가 병합 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-purge-rebuild-capacity"></a>익스텐트 재작성 용량 제거

|속성                           |Type    |Description                                                                                                                           |
|-----------------------------------|--------|--------------------------------------------------------------------------------------------------------------------------------------|
|MaximumConcurrentOperationsPerNode |long    |단일 노드에서의 제거 작업에 대 한 동시 다시 빌드 익스텐트의 최대 값 |

클러스터의 총 익스텐트 재작성 용량 제거 ( [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이)는 다음을 통해 계산 됩니다.

`Number of nodes in cluster`.x`MaximumConcurrentOperationsPerNode`

> [!Note]
> 노드가 세 개 이상인 클러스터에서는 관리 노드가 병합 작업 수행에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="export-capacity"></a>용량 내보내기

|속성                           |Type    |Description                                                                                                                                                                            |
|-----------------------------------|--------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 내보내기 작업 수에 대 한 최대값입니다.                                                                                                           |
|CoreUtilizationCoefficient         |double  |내보내기 용량을 계산할 때 사용할 코어 비율의 계수입니다. 계산 결과는 항상으로 정규화 됩니다 `ClusterMaximumConcurrentOperations` . |

클러스터의 총 내보내기 용량 ( [. 용량 표시](../management/diagnostics.md#show-capacity)와 같이)은 다음을 통해 계산 됩니다.

최소 ( `ClusterMaximumConcurrentOperations` , `Number of nodes in cluster` * 최대 (1, `Core count per node`  *  `CoreUtilizationCoefficient` ))

> [!Note]
> 노드가 세 개 이상인 클러스터에서 관리 노드는 내보내기 작업을 수행 하는 작업에 참여 하지 않습니다. 는 `Number of nodes in cluster` 1로 줄어듭니다.

## <a name="extents-partition-capacity"></a>익스텐트 파티션 용량

|속성                           |Type    |Description                                                                                         |
|-----------------------------------|--------|----------------------------------------------------------------------------------------------------|
|ClusterMinimumConcurrentOperations |long    |클러스터의 동시 익스텐트 파티션 작업 수에 대 한 최소값입니다. 기본값: 1  |
|ClusterMaximumConcurrentOperations |long    |클러스터의 동시 익스텐트 파티션 작업 수에 대 한 최대값입니다. 기본값: 16 |

클러스터의 총 익스텐트 파티션 용량 (에 표시 된 [용량 표시](../management/diagnostics.md#show-capacity))입니다.

의 유효 값은 `Concurrent operations` [,] 범위에서 시스템에 의해 자동으로 `ClusterMinimumConcurrentOperations` 조정 `ClusterMaximumConcurrentOperations` 됩니다.

## <a name="defaults"></a>기본값

기본 용량 정책의 JSON 표현은 다음과 같습니다.

```kusto 
{
  "IngestionCapacity": {
    "ClusterMaximumConcurrentOperations": 512,
    "CoreUtilizationCoefficient": 0.75
  },
  "ExtentsMergeCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExtentsPurgeRebuildCapacity": {
    "MaximumConcurrentOperationsPerNode": 1
  },
  "ExportCapacity": {
    "ClusterMaximumConcurrentOperations": 100,
    "CoreUtilizationCoefficient": 0.25
  }
}
```

## <a name="control-commands"></a>제어 명령

> [!WARNING]
> 용량 정책을 변경 하기 전에 Azure 데이터 탐색기 팀에 문의 하세요.

* 클러스터 [정책 용량 표시](capacity-policy.md#show-cluster-policy-capacity) 를 사용 하 여 클러스터의 현재 용량 정책을 표시 합니다.

* 클러스터 [정책 용량 변경](capacity-policy.md#alter-cluster-policy-capacity) 을 사용 하 여 클러스터의 용량 정책을 변경 합니다.

## <a name="throttling"></a>제한

Kusto는 다음과 같은 사용자 시작 명령에 대 한 동시 요청 수를 제한 합니다.

* Ingestions ( [여기](../../ingest-data-overview.md)에 나열 된 모든 명령이 포함 되어 있습니다.)
   * 제한은 [용량 정책](#capacity-policy)에 정의 된 대로입니다.
* 제거
   * 전역은 현재 클러스터당 하나의에 고정 되어 있습니다.
   * 제거 작업을 수행 하는 동안 동시 다시 작성 작업의 수를 확인 하기 위해 제거 다시 작성 용량을 내부적으로 사용 합니다. 제거 명령은이 프로세스로 인해 차단/제한 되지 않지만 다시 작성 용량 제거에 따라 더 빠르거나 더 느리게 작동 합니다.
* 내보내기
   * 제한은 [용량 정책](#capacity-policy)에 정의 된 대로입니다.

클러스터가 허용 된 동시 작업을 초과 하는 작업을 감지 하면 429 ("제한 된") HTTP 코드로 응답 합니다.
백오프 후 작업을 다시 시도 하세요.
