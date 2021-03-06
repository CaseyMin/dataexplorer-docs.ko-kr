---
title: unixtime_milliseconds_todatetime ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 unixtime_milliseconds_todatetime ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 9f029b18a147907a9e77e5ade7a1b708d3bed877
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92245650"
---
# <a name="unixtime_milliseconds_todatetime"></a>unixtime_milliseconds_todatetime()

Unix epoch 밀리초를 UTC 날짜/시간으로 변환 합니다.

## <a name="syntax"></a>구문

`unixtime_milliseconds_todatetime(*milliseconds*)`

## <a name="arguments"></a>인수

* *밀리초*: 실수는 epoch 타임 스탬프 (밀리초)를 나타냅니다. `Datetime` epoch 시간 (1970-01-01 00:00:00)에 음수 타임 스탬프 값이 발생 하기 전에 발생 합니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 [datetime](./scalar-data-types/datetime.md) 값이 됩니다. 변환이 실패 하면 결과는 null이 됩니다.

## <a name="see-also"></a>참조

* [Unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)를 사용 하 여 unix epoch 초를 UTC 날짜/시간으로 변환 합니다.
* [Unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md)를 사용 하 여 unix epoch 마이크로초를 UTC 날짜/시간으로 변환 합니다.
* [Unixtime_nanoseconds_todatetime ()](unixtime-nanoseconds-todatetimefunction.md)를 사용 하 여 unix epoch 나노초를 UTC 날짜/시간으로 변환 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_milliseconds_todatetime(1546300800000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
