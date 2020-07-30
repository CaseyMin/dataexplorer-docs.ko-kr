---
title: base64_encode_tostring ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 base64_encode_tostring ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 06/22/2019
ms.openlocfilehash: 218f87a3c11695c4aa8135f98b0c445580de9ad0
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349264"
---
# <a name="base64_encode_tostring"></a>base64_encode_tostring()

문자열을 base64 문자열로 인코딩합니다.

## <a name="syntax"></a>Syntax

`base64_encode_tostring(`*문자열*`)`

## <a name="arguments"></a>인수

* *문자열*: base64 문자열로 인코딩할 입력 문자열입니다.

## <a name="returns"></a>반환

Base64 문자열로 인코드된 문자열을 반환 합니다.

* Base64 문자열을 UTF-8 문자열로 디코딩하려면 [base64_decode_tostring ()](base64_decode_tostringfunction.md) 를 참조 하세요.
* Base64 문자열을 long 값 배열로 디코딩하려면 [base64_decode_toarray ()](base64_decode_toarrayfunction.md) 를 참조 하세요.


## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print Quine=base64_encode_tostring("Kusto")
```

|방법   |
|--------|
|S3VzdG8 =|

