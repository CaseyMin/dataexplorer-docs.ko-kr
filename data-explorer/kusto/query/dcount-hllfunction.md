---
title: dcount_hll ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 dcount_hll ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/15/2019
ms.openlocfilehash: 2e3847f0ad6c120f076461c5b4774f60349d6125
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348431"
---
# <a name="dcount_hll"></a>dcount_hll()

Hll 또는 [hll_merge](hll-merge-aggfunction.md) [에서 생성](hll-aggfunction.md) 된 결과에서 dcount를 계산 합니다.

[기본 알고리즘 (*H*yper*L*og*l*og) 및 예측 정확도](dcount-aggfunction.md#estimation-accuracy)에 대해 읽어 보십시오.

## <a name="syntax"></a>Syntax

`dcount_hll(`*Expr*`)`

## <a name="arguments"></a>인수

* *Expr*: [hll](hll-aggfunction.md) 또는 [hll-merge](hll-merge-aggfunction.md) 에 의해 생성 된 식입니다.

## <a name="returns"></a>반환

*Expr* 의 각 값에 대 한 고유 개수입니다.

## <a name="examples"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

|dcount_hll_hllMerged|
|---|
|315|
