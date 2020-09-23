---
title: unixtime_nanoseconds_todatetime ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 unixtime_nanoseconds_todatetime ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 11/25/2019
ms.openlocfilehash: 869a89510e96c1c3fbc99feb9a01bd70d2728149
ms.sourcegitcommit: 4e95f5beb060b5d29c1d7bb8683695fe73c9f7ea
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/23/2020
ms.locfileid: "91103462"
---
# <a name="unixtime_nanoseconds_todatetime"></a>unixtime_nanoseconds_todatetime()

Unix epoch 나노초를 UTC 날짜/시간으로 변환 합니다.

## <a name="syntax"></a>구문

`unixtime_nanoseconds_todatetime(*nanoseconds*)`

## <a name="arguments"></a>인수

* *나노초*: 실수는 epoch 타임 스탬프 (나노초)를 나타냅니다. `Datetime` epoch 시간 (1970-01-01 00:00:00)에 음수 타임 스탬프 값이 발생 하기 전에 발생 합니다.

## <a name="returns"></a>반환

성공적으로 변환 되 면 결과는 [datetime](./scalar-data-types/datetime.md) 값이 됩니다. 변환이 실패 하면 결과는 null이 됩니다.

## <a name="see-also"></a>참고 항목

* [Unixtime_seconds_todatetime ()](unixtime-seconds-todatetimefunction.md)를 사용 하 여 unix epoch 초를 UTC 날짜/시간으로 변환 합니다.
* [Unixtime_milliseconds_todatetime ()](unixtime-milliseconds-todatetimefunction.md)를 사용 하 여 unix epoch 밀리초를 UTC 날짜/시간으로 변환 합니다.
* [Unixtime_microseconds_todatetime ()](unixtime-microseconds-todatetimefunction.md)를 사용 하 여 unix epoch 마이크로초를 UTC 날짜/시간으로 변환 합니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net/Samples  -->
```kusto
print date_time = unixtime_nanoseconds_todatetime(1546300800000000000)
```

|date_time|
|---|
|2019-01-01 00:00:00.0000000|
