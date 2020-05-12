---
title: base64_encode_fromarray ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_encode_fromarray ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/11/2020
ms.openlocfilehash: bee6471ef2cf2a2cd484af8ce84d70cce749d5e0
ms.sourcegitcommit: 39b04c97e9ff43052cdeb7be7422072d2b21725e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/12/2020
ms.locfileid: "83225329"
---
# <a name="base64_encode_fromarray"></a>base64_encode_fromarray()

바이트 배열에서 base64 문자열을 인코딩합니다.

**구문**

`base64_encode_fromarray(`*BytesArray*`)`

**인수**

* *Bytesarray*: base64 문자열로 인코딩할 입력 바이트 배열입니다.

**반환**

바이트 배열에서 인코딩된 base64 문자열을 반환 합니다.

* UTF-8 문자열로 base64 문자열을 디코딩하려면 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 를 참조 하세요.
* Base64 문자열에 문자열을 인코딩하기 위해 [base64_encode_tostring ()](base64_encode_tostringfunction.md) 를 참조 하세요.
* 이 함수는 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 의 역함수입니다.

**예제**

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let bytes_array = toscalar(print base64_decode_toarray("S3VzdG8="));
print decoded_base64_string = base64_encode_fromarray(bytes_array)
```

|decoded_base64_string|
|---|
|S3VzdG8 =|


잘못 된 u t f-8로 인코딩된 문자열에서 생성 된 잘못 된 바이트 배열의 base64 문자열을 인코딩하면 null이 반환 됩니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let empty_bytes_array = toscalar(print base64_decode_toarray("U3RyaW5n0KHR0tGA0L7Rh9C60LA"));
print empty_string = base64_encode_fromarray(empty_bytes_array)
```

|empty_string|
|---|
||
