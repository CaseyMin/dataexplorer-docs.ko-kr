---
title: hash_combine ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 hash_combine ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/19/2019
ms.openlocfilehash: 91a178ded007ff75d96ed73f864276aa7d193d8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92244774"
---
# <a name="hash_combine"></a>hash_combine()

둘 이상의 해시에 대 한 해시 값을 결합 합니다.

## <a name="syntax"></a>구문

`hash_combine(`*h1* `,` *h2* [ `,` *h3* ...]`)`

## <a name="arguments"></a>인수

* *h1*: 첫 번째 해시 값을 나타내는 Long 값입니다.
* *h2*: 두 번째 해시 값을 나타내는 Long 값입니다.
* *hN*: n 번째 해시 값을 나타내는 Long 값입니다.

## <a name="returns"></a>반환

지정 된 스칼라의 결합 된 해시 값입니다.

## <a name="examples"></a>예

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print value1 = "Hello", value2 = "World"
| extend h1 = hash(value1), h2=hash(value2)
| extend combined = hash_combine(h1, h2)
```

|value1|value2|h1|h2|전체|
|---|---|---|---|---|
|안녕하세요.|World|753694413698530628|1846988464401551951|-1440138333540407281|
