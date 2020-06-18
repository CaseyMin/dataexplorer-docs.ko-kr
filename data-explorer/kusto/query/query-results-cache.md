---
title: 쿼리 결과 캐시-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 쿼리 결과 캐시 기능을 설명 합니다.
services: data-explorer
author: amitof
ms.author: amitof
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: reference
ms.date: 06/16/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 3c1e1eec2bb0ad4d856ca690039dea9aedd78e8f
ms.sourcegitcommit: a8575e80c65eab2a2118842e59f62aee0ff0e416
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/17/2020
ms.locfileid: "84943133"
---
# <a name="query-results-cache"></a>쿼리 결과 캐시

Kusto에는 쿼리 결과 캐시가 포함 됩니다. 열의를 표시 하 여 쿼리를 실행할 때 캐시 된 결과를 가져올 수 있습니다. 캐시에서 쿼리의 결과를 반환할 수 있는 경우 결과에서 "부실"이 발생 하더라도 쿼리 성능이 향상 되 고 리소스 소비가 줄어듭니다.

## <a name="indicating-willingness-to-use-the-cache"></a>캐시를 사용할 열의를 나타내는

쿼리 `query_results_cache_max_age` 결과 캐시를 사용 하도록 열의를 나타내려면 옵션을 쿼리의 일부로 설정 합니다. 이 옵션은 쿼리 텍스트에서 설정 하거나 클라이언트 요청 속성으로 설정할 수 있습니다. 다음은 그 예입니다.

```kusto
set query_results_cache_max_age = time(5m);
GithubEvent
| where CreatedAt > ago(180d)
| summarize arg_max(CreatedAt, Type) by Id
```

옵션 값은 `timespan` 쿼리 시작 시간에서 측정 된 결과 캐시의 최대 "age"를 나타내는입니다. Set timespan을 초과 하면 캐시 항목은 더 이상 사용 되지 않으며 다시 사용 되지 않습니다. 값 0을 설정 하는 것은 옵션을 설정 하지 않는 것과 같습니다.

## <a name="how-compatibility-between-queries-is-determined"></a>쿼리 간 호환성을 확인 하는 방법

Query_results_cache은 이전에 캐시 된 쿼리와 "동일한" 것으로 간주 되는 쿼리에 대해서만 결과를 반환 합니다. 다음 조건이 모두 충족 되는 경우 두 개의 쿼리가 동일한 것으로 간주 됩니다.

* 두 쿼리에는 UTF-8 문자열과 동일한 표현이 있습니다.

* 두 쿼리는 동일한 데이터베이스에 대해 수행 됩니다.

* 두 쿼리는 동일한 [클라이언트 요청 속성](../api/netfx/request-properties.md)을 공유 합니다. 캐싱 용도로는 다음 속성이 무시 됩니다.
   * [ClientRequestId](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)
   * [애플리케이션](../api/netfx/request-properties.md#the-application-x-ms-app-named-property)
   * [사용자](../api/netfx/request-properties.md#the-user-x-ms-user-named-property)

## <a name="if-the-query-results-cache-cant-find-a-valid-cache-entry"></a>쿼리 결과 캐시가 올바른 캐시 항목을 찾을 수 없는 경우

시간 제약 조건을 만족 하는 캐시 된 결과를 찾을 수 없거나 캐시에 있는 "동일" 쿼리에서 캐시 된 결과가 없는 경우 다음과 같이 쿼리를 실행 하 고 해당 결과를 캐시 합니다. 

* 쿼리 실행이 성공적으로 완료 됩니다.
* 쿼리 결과 크기가 16mb를 초과 하지 않습니다.

## <a name="how-the-service-indicates-that-the-query-results-are-being-served-from-the-cache"></a>서비스에서 쿼리 결과가 캐시에서 제공 되는 것으로 표시 되는 방법

쿼리에 응답 하는 경우 Kusto는 열 및 열을 포함 하는 추가 [Extendedproperties](../api/rest/response.md) 응답 테이블을 보냅니다 `Key` `Value` .
캐시 된 쿼리 결과에는 해당 테이블에 추가 행이 추가 됩니다.
* 행의 열에는 `Key` 문자열이 포함 됩니다.`ServerCache`
* 행의 열에는 `Value` 두 개의 필드가 있는 속성 모음이 포함 됩니다.
   * `OriginalClientRequestId`-원래 요청의 [Clientrequestid](../api/netfx/request-properties.md#the-clientrequestid-x-ms-client-request-id-named-property)를 지정 합니다.
   * `OriginalStartedOn`-원래 요청의 실행 시작 시간을 지정 합니다.

## <a name="distribution"></a>배포

캐시는 클러스터 노드에서 공유 되지 않습니다. 모든 노드에는 자체의 전용 저장소에 전용 캐시가 있습니다. 두 개의 동일한 쿼리가 서로 다른 노드에 있으면 쿼리가 실행 되 고 두 노드에서 모두 캐시 됩니다. 이 프로세스는 [약한 일관성](../concepts/queryconsistency.md) 을 사용 하는 경우에 발생할 수 있습니다.

## <a name="management"></a>관리

다음 management 및 관찰성 명령이 지원 됩니다.

* [캐시를 표시](../management/show-query-results-cache-command.md)합니다.
* [캐시를 지웁니다](../management/clear-query-results-cache-command.md).

## <a name="capacity"></a>용량

캐시 용량은 현재 클러스터 노드당 1gb로 고정 되어 있습니다.
제거 정책은 LRU입니다.
