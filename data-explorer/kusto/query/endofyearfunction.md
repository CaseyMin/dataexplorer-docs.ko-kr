---
title: endofyear() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기에서 endofyear()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: d4a14d1cc42d5b9116e8a144e2b67fb74c1932ee
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81515779"
---
# <a name="endofyear"></a>endofyear()

제공된 경우 오프셋으로 이동된 날짜를 포함하는 연도의 끝을 반환합니다.

**구문**

`endofyear(`*날짜* `,`[*오프셋*]`)`

**인수**

* `date`: 입력 날짜입니다.
* `offset`: 입력 날짜(정수, 기본값 - 0)에서 오프셋 연도의 선택적 수입니다.

**반환**

*지정된* 날짜 값에 대한 연도의 끝을 나타내는 날짜 시간(지정된 경우 오프셋)입니다.

**예제**

```kusto
  range offset from -1 to 1 step 1
 | project yearEnd = endofyear(datetime(2017-01-01 10:10:17), offset) 
```

|연말|
|---|
|2016-12-31 23:59:59.9999999|
|2017-12-31 23:59:59.9999999|
|2018-12-31 23:59:59.9999999|