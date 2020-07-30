---
title: minif () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 minif () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 91764aeb8c825a272c414df7a0572d3b8310e79f
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87346748"
---
# <a name="minif-aggregation-function"></a>minif () (집계 함수)

*조건자* 가로 계산 되는 그룹의 최소값을 반환 합니다 `true` .

* [요약](summarizeoperator.md) 내의 집계 컨텍스트에서만 사용할 수 있습니다.

조건자 식 없이 group의 최소값을 반환 하는- [min ()](min-aggfunction.md) 함수를 참조 하세요.

## <a name="syntax"></a>Syntax

`summarize``minif(` *Expr* `,` *조건자*`)`

## <a name="arguments"></a>인수

* *Expr*: 집계 계산에 사용 되는 식입니다.
* *Predicate*: True 이면 *Expr* 계산 된 값이 최소값으로 확인 됩니다.

## <a name="returns"></a>반환

*조건자* 가로 계산 되는 그룹에 대 한 *Expr* 의 최 솟 값입니다 `true` .

## <a name="examples"></a>예제

```kusto
range x from 1 to 100 step 1
| summarize minif(x, x > 50)
```

|minif_x|
|---|
|51|