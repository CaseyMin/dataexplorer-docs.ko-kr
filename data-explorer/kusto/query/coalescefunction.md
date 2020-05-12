---
title: 병합 ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 병합 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: ea57efe36fb86189d798e5f18fa3fe9470bfd634
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83227539"
---
# <a name="coalesce"></a>coalesce()

식 목록을 계산 하 고 null이 아닌 첫 번째 (또는 문자열의 경우 비어 있지 않은) 식을 반환 합니다.

```kusto
coalesce(tolong("not a number"), tolong("42"), 33) == 42
```

**구문**

`coalesce(`*expr_1* `, ` *expr_2* `,` ...)

**인수**

* *expr_i*: 평가할 스칼라 식입니다.
- 모든 인수는 동일한 형식 이어야 합니다.
- 최대 64 개의 인수가 지원 됩니다.


**반환**

값이 null이 아닌 첫 번째 *expr_i* 의 값 (또는 문자열 식의 경우 비어 있지 않음)입니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print result=coalesce(tolong("not a number"), tolong("42"), 33)
```

|result|
|---|
|42|
