---
title: base64_decode_toarray ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_decode_toarray ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 34a4601c35acb9c95e09e49d201b2e1cddaace8c
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92252722"
---
# <a name="base64_decode_toarray"></a>base64_decode_toarray()

Base64 문자열을 long 값 배열로 디코딩합니다.

## <a name="syntax"></a>구문

`base64_decode_toarray(`*문자열*`)`

## <a name="arguments"></a>인수

* *문자열*: BASE64에서 UTF8 문자열로 디코딩할 입력 문자열입니다.

## <a name="returns"></a>반환

Base64 문자열에서 디코딩된 long 값의 배열을 반환 합니다.

* Base64 문자열을 UTF-8 문자열로 디코딩하려면 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 를 참조 하세요.
* 문자열을 base64 문자열로 인코딩하려면 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 를 참조 하세요.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_decode_toarray("S3VzdG8=")  
// 'K', 'u', 's', 't', 'o'
```

|방법|
|-----|
|[75117115116111]|

잘못 된 UTF-8 인코딩에서 생성 된 base64 문자열을 디코딩하 려 면 "null"이 반환 됩니다.

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Empty=base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA=")
```

|Empty|
|-----|
||
