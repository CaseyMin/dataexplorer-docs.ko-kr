---
title: 전 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 전 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 3f3d58a7b29731125407eec7429967d1f90f5b7e
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87349774"
---
# <a name="ago"></a>ago()

현재 UTC 시계 시간에서 지정된 시간 범위를 뺍니다.

```kusto
ago(1h)
ago(1d)
```

`now()`와(과) 마찬가지로, 이 함수를 문에 여러 번 사용할 수 있으며 참조하는 UTC 시계 시간은 모든 인스턴스에 대해 동일합니다.

## <a name="syntax"></a>구문

`ago(`*a_timespan*`)`

## <a name="arguments"></a>인수

* *a_timespan*: 현재 UTC 시계 시간(`now()`)에서 뺄 간격입니다.

## <a name="returns"></a>반환

`now() - a_timespan`

## <a name="example"></a>예제

과거 시간에 타임스탬프를 가진 모든 행:

```kusto
T | where Timestamp > ago(1h)
```