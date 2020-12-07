---
title: series_moving_avg_fl ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 series_moving_avg_fl () 사용자 정의 함수에 대해 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: adieldar
ms.service: data-explorer
ms.topic: reference
ms.date: 09/08/2020
ms.openlocfilehash: 4d6ce55389c13acc0192d7dc0ef21b4940c6d240
ms.sourcegitcommit: 80f0c8b410fa4ba5ccecd96ae3803ce25db4a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96321780"
---
# <a name="series_moving_avg_fl"></a>series_moving_avg_fl()

계열에 이동 평균 필터를 적용 합니다.

함수는 `series_moving_avg_fl()` 동적 숫자 배열을 입력으로 포함 하는 식을 사용 하 여 [단순 이동 평균](https://en.wikipedia.org/wiki/Moving_average#Simple_moving_average) 필터를 적용 합니다.

> [!NOTE]
> 이 함수는 [UDF (사용자 정의 함수)](../query/functions/user-defined-functions.md)입니다. 자세한 내용은 [사용](#usage)을 참조 하세요.

## <a name="syntax"></a>Syntax

`series_moving_avg_fl(`*y_series* `,` *n* `, [` *가운데*`])`
  
## <a name="arguments"></a>인수

* *y_series*: 숫자 값의 동적 배열 셀입니다.
* *n*: 이동 평균 필터의 너비입니다.
* *center*: 이동 평균이 다음 옵션 중 하나 인지 여부를 나타내는 선택적 부울 값입니다.
    * 현재 지점 앞과 뒤에 있는 창에 대칭으로 적용 되거나 
    * 현재 점에서 뒤로의 창에 적용 됩니다. <br>
    기본적으로 *center* 는 False입니다.

## <a name="usage"></a>사용량

`series_moving_avg_fl()` 는 사용자 정의 함수입니다. 쿼리에 해당 코드를 포함 하거나 데이터베이스에 설치할 수 있습니다. Ad hoc 및 영구 사용의 두 가지 사용 옵션이 있습니다. 예제는 아래 탭을 참조 하세요.

# <a name="ad-hoc"></a>[임시](#tab/adhoc)

임시 사용의 경우 [let 문을](../query/letstatement.md)사용 하 여 해당 코드를 포함 합니다. 권한이 필요 하지 않습니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
let series_moving_avg_fl = (y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
;
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_fl(num, 5, True)
| render timechart 
```

# <a name="persistent"></a>[Persistent](#tab/persistent)

영구적으로 사용 하려면을 사용 [`.create function`](../management/create-function.md) 합니다. 함수를 만들려면 [데이터베이스 사용자 권한이](../management/access-control/role-based-authorization.md)필요 합니다.

### <a name="one-time-installation"></a>일회성 설치

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
.create-or-alter function with (folder = "Packages\\Series", docstring = "Calculate moving average of specified width")
series_moving_avg_fl(y_series:dynamic, n:int, center:bool=false)
{
    series_fir(y_series, repeat(1, n), true, center)
}
```

### <a name="usage"></a>사용량

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
//
//  Moving average of 5 bins
//
demo_make_series1
| make-series num=count() on TimeStamp step 1h by OsVer
| extend num_ma=series_moving_avg_fl(num, 5, True)
| render timechart 
```

---

:::image type="content" source="images/series-moving-avg-fl/moving-average-five-bins.png" alt-text="5 개 계급의 이동 평균을 보여 주는 그래프" border="false":::
