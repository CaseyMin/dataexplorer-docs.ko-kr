---
title: parse_csv ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_csv ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: b61934ec2efbfb22c17fe93a4f3969a1592cefab
ms.sourcegitcommit: 8e097319ea989661e1958efaa1586459d2b69292
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/15/2020
ms.locfileid: "84780663"
---
# <a name="parse_csv"></a>parse_csv()

쉼표로 구분 된 값의 단일 레코드를 나타내는 지정 된 문자열을 분할 하 고 이러한 값을 포함 하는 문자열 배열을 반환 합니다.

```kusto
parse_csv("aaa,bbb,ccc") == ["aaa","bbb","ccc"]
```

**구문**

`parse_csv(`*원본*`)`

**인수**

* *source*: 쉼표로 구분 된 값의 단일 레코드를 나타내는 소스 문자열입니다.

**반환**

분할 값을 포함 하는 문자열 배열입니다.

**참고 사항**

큰따옴표 (' "')를 사용 하 여 포함 된 줄 바꿈, 쉼표 및 따옴표를 이스케이프할 수 있습니다. 이 함수는 행 마다 여러 레코드를 지원 하지 않습니다. 첫 번째 레코드만 가져옵니다.

**예**

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result=parse_csv('aa,"b,b,b",cc,"Escaping quotes: ""Title""","line1\nline2"')
```

|result|
|---|
|[<br>  "aa",<br>  "b, b, b",<br>  "cc",<br>  "이스케이프 따옴표: \" Title \" ",<br>  "line1\nline2"<br>]|

여러 레코드가 포함 된 CSV 페이로드:

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print result_multi_record=parse_csv('record1,a,b,c\nrecord2,x,y,z')
```

|result_multi_record|
|---|
|[<br>  "record1",<br>  "a",<br>  "b",<br>  "c"<br>]|
