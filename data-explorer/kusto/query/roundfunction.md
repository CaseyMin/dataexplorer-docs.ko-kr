---
title: round ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 라운드 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 90d424929fe0b2034e4778ca2167e1e14dfbf79e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92242903"
---
# <a name="round"></a>round()

지정 된 전체 자릿수로 반올림 된 원본을 반환 합니다.

## <a name="syntax"></a>구문

`round(`*원본* [ `,` *전체 자릿수*]`)`

## <a name="arguments"></a>인수

* *원본*: 라운드를 계산 하는 원본 스칼라입니다.
* *전체 자릿수*: 소스가 반올림 되는 자릿수입니다. (기본값은 0)

## <a name="returns"></a>반환

지정 된 전체 자릿수로 반올림 된 원본입니다.

Round는 [`bin()`](binfunction.md) / [`floor()`](floorfunction.md) 첫 번째 값이 숫자를 특정 자릿수로 반올림 하는 반면, 마지막 반올림 값은 지정 된 bin 크기의 정수 배수 (round (2.15, 1)는 2.2을 반환 하는 반면 bin (2.15, 1)는 2를 반환 함)와는 다릅니다.
 

## <a name="examples"></a>예

```kusto
round(2.15, 1)                   // 2.2
round(2.15) (which is the same as round(2.15, 0))                   // 2
round(-50.55, -2)                   // -100
round(21.5, -1)                   // 20
```