---
title: binary_all_or () (집계 함수)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 binary_all_or () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: bff5d631493b9431d6e1a5fa88d3dd419147df96
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92247968"
---
# <a name="binary_all_or-aggregation-function"></a>binary_all_or () (집계 함수)

요약 그룹당 이항 연산을 사용 하 여 값을 누적 `OR` 합니다 (그룹화 하지 않고 요약이 수행 되는 경우 전체).

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

## <a name="syntax"></a>구문

`binary_all_or(` *식* 요약`)`

## <a name="arguments"></a>인수

* *Expr*: long 숫자입니다.

## <a name="returns"></a>반환

요약 그룹당 레코드에 대해 이항 연산을 사용 하 여 집계 된 값을 반환 `OR` 합니다. 요약을 그룹화 하지 않고 요약 하는 경우에는 합계를 사용 합니다.

## <a name="example"></a>예제

이항 연산을 사용 하 여 ' 카페 ' 생성 `OR` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
datatable(num:long)
[
  0x88888008,
  0x42000000,
  0x00767000,
  0x00000005, 
]
| summarize result = toupper(tohex(binary_all_or(num)))
```

|result|
|---|
|CAFEF00D|
