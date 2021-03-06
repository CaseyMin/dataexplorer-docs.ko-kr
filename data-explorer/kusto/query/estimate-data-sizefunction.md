---
title: estimate_data_size ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 estimate_data_size ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 09d722b07b3ff49a294d4d8ce19a89563fc8f66e
ms.sourcegitcommit: 608539af6ab511aa11d82c17b782641340fc8974
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/20/2020
ms.locfileid: "92253017"
---
# <a name="estimate_data_size"></a>estimate_data_size()

테이블 형식 식에서 선택한 열의 예상 데이터 크기 (바이트)를 반환 합니다.

```kusto
estimate_data_size(*)
estimate_data_size(Col1, Col2, Col3)
```

## <a name="syntax"></a>구문

`estimate_data_size(*)`

`estimate_data_size(`*col1* `, ` *col2* `, ` ...`)`

## <a name="arguments"></a>인수

* *col1*, *col2*: 데이터 크기 예측에 사용 되는 원본 테이블 형식 식의 열 참조를 선택 합니다. 모든 열을 포함 하려면 `*` (별표) 구문을 사용 합니다.

## <a name="returns"></a>반환

* 레코드 크기의 예상 데이터 크기 (바이트)입니다. 예측은 데이터 형식 및 값 길이를 기준으로 합니다.

## <a name="examples"></a>예

다음을 사용 하 여 총 데이터 크기 계산 `estimated_data_size()` :

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
range x from 1 to 10 step 1                    // x (long) is 8 
| extend Text = '1234567890'                   // Text length is 10  
| summarize Total=sum(estimate_data_size(*))   // (8+10)x10 = 180
```

|합계|
|---|
|180|
