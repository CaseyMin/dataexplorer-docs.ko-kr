---
title: iff ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 iff ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 7eeab87f3c3ef42d1e00bf0d6b8853fe3a2f3125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87347496"
---
# <a name="iff"></a>iff()

첫 번째 인수 (조건자)를 계산 하 고, 조건자가 `true` (두 번째) 또는 (셋째)로 계산 되었는지 여부에 따라 두 번째 또는 세 번째 인수 값을 반환 합니다 `false` .

두 번째 및 세 번째 인수는 동일한 형식이어야 합니다.

## <a name="syntax"></a>구문

`iff(`*조건자* `,` *Iftrue* `,` *ifFalse*`)`

## <a name="arguments"></a>인수

* *predicate*: 값으로 계산 되는 식 `boolean` 입니다.
* *Iftrue*: *조건자* 가로 계산 되는 경우 함수에서 반환 되는 계산 된 식 및 값 `true` 입니다.
* *ifFalse*: 계산 되는 식으로, *조건자* 가로 계산 되는 경우 함수에서 반환 된 값 `false` 입니다.

## <a name="returns"></a>반환

이 함수는 *predicate*이 `true`(으)로 계산되는 경우 *ifTrue*의 값을 반환하며 그렇지 않은 경우 *ifFalse*의 값을 반환합니다.

## <a name="example"></a>예제

```kusto
T 
| extend day = iff(floor(Timestamp, 1d)==floor(now(), 1d), "today", "anotherday")
```