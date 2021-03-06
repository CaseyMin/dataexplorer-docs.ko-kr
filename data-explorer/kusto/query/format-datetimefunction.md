---
title: format_datetime() - Azure Data Explorer
description: 이 문서에서는 Azure Data Explorer의 format_datetime()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.localizationpriority: high
ms.openlocfilehash: ec9fc6edf0e62c694e1090ea5d5adade333a80ac
ms.sourcegitcommit: f49e581d9156e57459bc69c94838d886c166449e
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "95512930"
---
# <a name="format_datetime"></a>format_datetime()

제공된 형식에 따라 datetime 서식을 지정합니다.

```kusto
format_datetime(datetime(2015-12-14 02:03:04.12345), 'y-M-d h:m:s.fffffff') == "15-12-14 2:3:4.1234500"
```

## <a name="syntax"></a>Syntax

`format_datetime(`*datetime* `,` *형식*`)`

## <a name="arguments"></a>인수

* `datetime`: `datetime` 형식의 값입니다.
* `format`: 하나 이상의 [형식 요소](#supported-formats)로 구성된 서식 지정자 문자열입니다.

## <a name="returns"></a>반환

형식 결과를 포함하는 문자열입니다.

## <a name="supported-formats"></a>지원되는 형식

|형식 지정자   |설명    |예제
|---|---|---
|`d`    |1부터 31까지의 일(월 기준)입니다. | 2009-06-01T13:45:30 -> 1, 2009-06-15T13:45:30 -> 15
|`dd`   |01부터 31까지의 일(월 기준)입니다.| 2009-06-01T13:45:30 -> 01, 2009-06-15T13:45:30 -> 15
|`f`    |날짜 및 시간 값에서 1/10초입니다. |2009-06-15T13:45:30.6170000 -> 6, 2009-06-15T13:45:30.05 -> 0
|`ff`   |날짜 및 시간 값의 1/100초입니다. |2009-06-15T13:45:30.6170000 -> 61, 2009-06-15T13:45:30.0050000 -> 00
|`fff`  |날짜 및 시간 값의 1/1000초입니다. |6/15/2009 13:45:30.617 -> 617, 6/15/2009 13:45:30.0005 -> 000
|`ffff` |날짜 및 시간 값의 1/10000초입니다. |2009-06-15T13:45:30.6175000 -> 6175, 2009-06-15T13:45:30.0000500 -> 0000
|`fffff`    |날짜 및 시간 값의 1/100000초입니다. |2009-06-15T13:45:30.6175400 -> 61754, 2009-06-15T13:45:30.000005 -> 00000
|`ffffff`   |날짜 및 시간 값의 1/1000000초입니다. |2009-06-15T13:45:30.6175420 -> 617542, 2009-06-15T13:45:30.0000005 -> 000000
|`fffffff`  |날짜 및 시간 값의 1/10000000초입니다. |2009-06-15T13:45:30.6175425 -> 6175425, 2009-06-15T13:45:30.0001150 -> 0001150
|`F`    |0이 아닌 경우 날짜 및 시간 값의 1/10초입니다. |2009-06-15T13:45:30.6170000 -> 6, 2009-06-15T13:45:30.0500000 -> (출력 없음)
|`FF`   |0이 아닌 경우 날짜 및 시간 값의 1/100초입니다. |2009-06-15T13:45:30.6170000 -> 61, 2009-06-15T13:45:30.0050000 -> (출력 없음)
|`FFF`  |0이 아닌 경우 날짜 및 시간 값의 1/1000초입니다. |2009-06-15T13:45:30.6170000 -> 617, 2009-06-15T13:45:30.0005000 -> (출력 없음)
|`FFFF` |0이 아닌 경우 날짜 및 시간 값의 1/10000초입니다. |2009-06-15T13:45:30.5275000 -> 5275, 2009-06-15T13:45:30.0000500 -> (출력 없음)
|`FFFFF`    |0이 아닌 경우 날짜 및 시간 값의 1/100000초입니다. |2009-06-15T13:45:30.6175400 -> 61754, 2009-06-15T13:45:30.0000050 -> (출력 없음)
|`FFFFFF`   |0이 아닌 경우 날짜 및 시간 값의 1/1000000초입니다. |2009-06-15T13:45:30.6175420 -> 617542, 2009-06-15T13:45:30.0000005 -> (출력 없음)
|`FFFFFFF`  |0이 아닌 경우 날짜 및 시간 값의 1/10000000초입니다. |2009-06-15T13:45:30.6175425 -> 6175425, 2009-06-15T13:45:30.0001150 -> 000115
|`h`    |12시간 형식을 사용하는 1부터 12까지의 시간입니다. |2009-06-15T01:45:30 -> 1, 2009-06-15T13:45:30 -> 1
|`hh`   |12시간 형식을 사용하는 01부터 12까지의 시간입니다. |2009-06-15T01:45:30 -> 01, 2009-06-15T13:45:30 -> 01
|`H`    |24시간 형식을 사용하는 0부터 23까지의 시간입니다. |2009-06-15T01:45:30 -> 1, 2009-06-15T13:45:30 -> 13
|`HH`   |24시간 형식을 사용하는 00부터 23까지의 시간입니다. |2009-06-15T01:45:30 -> 01, 2009-06-15T13:45:30 -> 13
|`m`    |0부터 59까지의 분입니다. |2009-06-15T01:09:30 -> 9, 2009-06-15T13:29:30 -> 29
|`mm`   |00부터 59까지의 분입니다. |2009-06-15T01:09:30 -> 09, 2009-06-15T01:45:30 -> 45
|`M`    |1부터 12까지의 월입니다. |2009-06-15T13:45:30 -> 6
|`MM`   |01부터 12까지의 월입니다.|2009-06-15T13:45:30 -> 06
|`s`    |0부터 59까지의 초입니다. |2009-06-15T13:45:09 -> 9
|`ss`   |00부터 59까지의 초입니다. |2009-06-15T13:45:09 -> 09
|`y`    |0부터 99까지의 연도입니다. |0001-01-01T00:00:00 -> 1, 0900-01-01T00:00:00 -> 0, 1900-01-01T00:00:00 -> 0, 2009-06-15T13:45:30 -> 9, 2019-06-15T13:45:30 -> 19
|`yy`   |00부터 99까지의 연도입니다. | 0001-01-01T00:00:00 -> 01, 0900-01-01T00:00:00 -> 00, 1900-01-01T00:00:00 -> 00, 2019-06-15T13:45:30 -> 19
|`yyyy` |네 자리 숫자로 된 연도입니다. | 0001-01-01T00:00:00 -> 0001, 0900-01-01T00:00:00 -> 0900, 1900-01-01T00:00:00 -> 1900, 2009-06-15T13:45:30 -> 2009
|`tt`   |AM / PM 시간 |2009-06-15T13:45:09 -> PM

**지원되는 구분 기호**

서식 지정자는 다음 구분 기호 문자를 포함할 수 있습니다.

|구분 기호|의견|
|---------|-------|
|`' '`| Space|
|`'/'`||
|`'-'`|대시|
|`':'`||
|`','`||
|`'.'`||
|`'_'`||
|`'['`||
|`']'`||

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let dt = datetime(2017-01-29 09:00:05);
print 
v1=format_datetime(dt,'yy-MM-dd [HH:mm:ss]'), 
v2=format_datetime(dt, 'yyyy-M-dd [H:mm:ss]'),
v3=format_datetime(dt, 'yy-MM-dd [hh:mm:ss tt]')
```

|v1|v2|v3|
|---|---|---|
|17-01-29 [09:00:05]|2017-1-29 [9:00:05]|17-01-29 [09:00:05 AM]|
