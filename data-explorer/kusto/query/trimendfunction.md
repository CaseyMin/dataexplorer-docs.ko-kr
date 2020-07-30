---
title: trim_end ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 trim_end ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: cab78680a3b996234724bc052d75959928520289
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87339853"
---
# <a name="trim_end"></a>trim_end()

지정 된 정규식의 후행 일치 항목을 제거 합니다.

## <a name="syntax"></a>Syntax

`trim_end(`*regex* `,` *텍스트*`)`

## <a name="arguments"></a>인수

* *regex*: *텍스트*의 끝에서 잘라내는 문자열 또는 [정규식](re2.md) 입니다.  
* *text*: 문자열입니다.

## <a name="returns"></a>반환

*텍스트*끝에 있는 *regex* 의 일치 항목을 지운 후의 *텍스트* 입니다.

## <a name="example"></a>예제

문 아래는 *string_to_trim*끝에서 *부분 문자열* 을 자릅니다.

```kusto
let string_to_trim = @"bing.com";
let substring = ".com";
print string_to_trim = string_to_trim,trimmed_string = trim_end(substring,string_to_trim)
```

|string_to_trim|trimmed_string|
|--------------|--------------|
|bing.com      |bing          |

다음 문은 문자열의 끝에서 모든 비단어 문자를 자릅니다.

```kusto
print str = strcat("-  ","Te st",x,@"// $")
| extend trimmed_str = trim_end(@"[^\w]+",str)
```

|str          |trimmed_str|
|-------------|-----------|
|-Te st1//$|-Te st1  |
|-Te st2//$|-Te st2  |
|-Te st3//$|-Te st3  |
|-Te st4//$|-Te st4  |
|-Te st5//$|-Te st5  |