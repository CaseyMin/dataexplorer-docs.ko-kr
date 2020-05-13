---
title: unixtime_seconds_todatetime ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 unixtime_seconds_todatetime ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 87a2db1109fecfd7e27f29d4305449b596f7cd68
ms.sourcegitcommit: bb8c61dea193fbbf9ffe37dd200fa36e428aff8c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83370416"
---
# <a name="unixtime_seconds_todatetime"></a>unixtime_seconds_todatetime()

Unix epoch 초를 UTC 날짜/시간으로 변환 합니다.

**구문**

`unixtime_seconds_todatetime(*seconds*)`

**인수**

* *초*: 실수는 epoch 타임 스탬프 (초)를 나타냅니다. `Datetime`epoch 시간 (1970-01-01 00:00:00)에 음수 타임 스탬프 값이 발생 하기 전에 발생 합니다.

**반환**

성공적으로 변환 되 면 결과는 [datetime](./scalar-data-types/datetime.md) 값이 됩니다. 변환이 실패 하면 결과는 null이 됩니다.

**참고 항목**

* [Unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md)를 사용 하 여 unix epoch 밀리초를 UTC 날짜/시간으로 변환 합니다.
* [Unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md)를 사용 하 여 unix epoch 마이크로초를 UTC 날짜/시간으로 변환 합니다.
* [Unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)를 사용 하 여 unix epoch 나노초를 UTC 날짜/시간으로 변환 합니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_seconds_todatetime(1546300800)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
