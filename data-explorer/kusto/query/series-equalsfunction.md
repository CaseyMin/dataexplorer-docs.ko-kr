---
title: series_equals ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_equals ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
ms.openlocfilehash: ffc474ab4415fd8258a4167098450d72f3a9567b
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253198"
---
# <a name="series_equals"></a>series_equals()

`==`두 개의 숫자 계열 입력의 요소 단위 같음 () 논리 연산을 계산 합니다.

## <a name="syntax"></a>구문

`series_equals (`*Series1* `,` *Series2*`)`

## <a name="arguments"></a>인수

* *Series1, Series2*: 요소를 비교 하는 데 사용할 숫자 배열을 입력 합니다. 모든 인수는 동적 배열 이어야 합니다. 

## <a name="returns"></a>반환

두 입력 사이에 계산 된 요소와 동등한 논리 연산을 포함 하는 부울의 동적 배열입니다. 숫자가 아닌 요소나 비 기존 요소 (크기가 다른 배열)는 `null` 요소 값을 생성 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print s1 = dynamic([1,2,4]), s2 = dynamic([4,2,1])
| extend s1_equals_s2 = series_equals(s1, s2)
```

|s1|s2|s1_equals_s2|
|---|---|---|
|[1, 2, 4]|[4, 2, 1]|[false, true, false]|

## <a name="see-also"></a>참조

전체 계열 통계 비교는 다음을 참조 하세요.
* [series_stats()](series-statsfunction.md)
* [series_stats_dynamic()](series-stats-dynamicfunction.md)
