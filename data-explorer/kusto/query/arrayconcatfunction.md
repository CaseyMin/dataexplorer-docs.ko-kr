---
title: array_concat ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 array_concat ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 10/23/2018
ms.openlocfilehash: 4c8e2da4d2ba4ed205987b5a1d063ac2e75ed289
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92246914"
---
# <a name="array_concat"></a>array_concat()

여러 동적 배열을 단일 배열에 연결 합니다.

## <a name="syntax"></a>구문

`array_concat(`*arr1* `[` , ` *arr2*, ...]` ) '

## <a name="arguments"></a>인수

* *arr1 ... arrN*: 동적 배열에 연결 되는 입력 배열입니다. 모든 인수는 동적 배열 이어야 합니다 ( [pack_array](packarrayfunction.md)참조). 

## <a name="returns"></a>반환

Arr1, arr2, ..., arrN를 사용 하는 배열의 동적 배열입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
range x from 1 to 3 step 1
| extend y = x * 2
| extend z = y * 2
| extend a1 = pack_array(x,y,z), a2 = pack_array(x, y)
| project array_concat(a1, a2)
```

|열1|
|---|
|[1, 2, 4, 1, 2]|
|[2, 4, 8, 2, 4]|
|[3, 6, 12, 3, 6]|
