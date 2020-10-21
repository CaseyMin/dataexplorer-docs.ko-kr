---
title: take operator-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 take operator에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 5192bb2d752a5754ae36840611b9f7b0e84da256
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92250728"
---
# <a name="take-operator"></a>take 연산자

지정한 수 만큼의 행을 반환 합니다.

```kusto
T | take 5
```

원본 데이터가 정렬 되지 않은 경우 레코드가 반환 되는 것을 보장 하지 않습니다.

> [!NOTE]
> `take` 는 데이터 집합이 변경 되지 않은 경우에도 데이터를 대화형으로 검색할 때 작은 레코드 샘플을 볼 수 있는 간단 하 고 빠르고 효율적인 방법 이지만 여러 번 실행 하는 경우 결과의 일관성을 보장 하지는 않습니다.
> 쿼리에서 반환 되는 행 수가 쿼리에 의해 명시적으로 제한 되지 않더라도 (연산자 사용 안 함 `take` ) Kusto 기본적으로 해당 수를 제한 합니다. 자세한 내용은 [Kusto 쿼리 제한](../concepts/querylimits.md)을 참조 하세요.

## <a name="syntax"></a>구문

`take`*Numberofrows* 
 `limit` *Numberofrows*

`take`및 `limit` 는 동의어입니다.

## <a name="does-kusto-support-paging-of-query-results"></a>Kusto 쿼리 결과의 페이징을 지원 하나요?

Kusto는 기본 제공 페이징 메커니즘을 제공 하지 않습니다.

Kusto는 대용량 데이터 집합 보다 뛰어난 쿼리 성능을 제공 하기 위해 저장 하는 데이터를 지속적으로 최적화 하는 복잡 한 서비스입니다. 페이징은 리소스가 제한 된 상태 비저장 클라이언트에 유용한 메커니즘 이지만 클라이언트 상태 정보를 추적 해야 하는 백 엔드 서비스로 부담을 이동 합니다. 이후에는 백 엔드 서비스의 성능 및 확장성이 심각 하 게 제한 됩니다.

페이징을 지원 하기 위해 다음 기능 중 하나를 구현 합니다.

* 쿼리 결과를 외부 저장소로 내보내고 생성 된 데이터를 페이징 합니다.

* Kusto 쿼리 결과를 캐시 하 여 상태 저장 페이징 API를 제공 하는 중간 계층 응용 프로그램을 작성 합니다.

## <a name="see-also"></a>참조

* [sort 연산자](sortoperator.md)
* [top 연산자](topoperator.md)
* [top-nested 연산자](topnestedoperator.md)