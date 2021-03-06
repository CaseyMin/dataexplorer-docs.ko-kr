---
title: hll_merge () (집계 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기에서 hll_merge () (집계 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: alexans
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 59813656fce6afea3ecba62b13c971e74b095fe1
ms.sourcegitcommit: 42cc7d11f41a5bfa9e021764b044dcd68d99a258
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93403717"
---
# <a name="hll_merge-aggregation-function"></a>hll_merge () (집계 함수)

`HLL`그룹 전체에 대 한 결과를 단일 `HLL` 값으로 병합 합니다.

* [요약](summarizeoperator.md)내의 집계 컨텍스트에서만 사용할 수 있습니다.

자세한 내용은 [기본 알고리즘 ( *H* yper *L* og *l* og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)를 참조 하세요.

## <a name="syntax"></a>구문

`summarize``hll_merge(` *Expr*`)`

## <a name="arguments"></a>인수

* `*Expr*`: 집계 계산에 사용 되는 식입니다.

## <a name="returns"></a>반환

함수는 `hll` 그룹 전체에서의 병합 된 값을 반환 합니다 `*Expr*` .
 
**팁**

1) 함수 [dcount_hll](dcount-hllfunction.md) 를 사용 하 여 `dcount` 집계 함수에서를 계산 `hll`  /  `hll-merge` 합니다.
