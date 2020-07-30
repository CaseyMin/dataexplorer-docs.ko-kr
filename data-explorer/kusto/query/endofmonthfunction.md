---
title: endofmonth ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 endofmonth ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 772cf42bfb4bd96a9cff94b7723b234139da462a
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348295"
---
# <a name="endofmonth"></a>endofmonth()

지정 된 경우 오프셋으로 이동한 날짜를 포함 하는 월의 끝을 반환 합니다.

## <a name="syntax"></a>구문

`endofmonth(`*날짜* [ `,` *오프셋*]`)`

## <a name="arguments"></a>인수

* `date`: 입력 날짜입니다.
* `offset`: 입력 날짜 (정수, 기본값-0)에서 오프셋 월의 선택적 개수입니다.

## <a name="returns"></a>반환

지정 된 경우 오프셋을 사용 하 여 지정 된 *날짜* 값에 대 한 월의 끝을 나타내는 날짜/시간입니다.

## <a name="example"></a>예제

```kusto
  range offset from -1 to 1 step 1
 | project monthEnd = endofmonth(datetime(2017-01-01 10:10:17), offset) 
```

|monthEnd|
|---|
|2016-12-31 23:59:59.9999999까지|
|2017-01-31 23:59:59.9999999까지|
|2017-02-28 23:59:59.9999999까지|