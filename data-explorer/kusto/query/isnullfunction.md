---
title: isnull ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 isnull ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 4c57c7aba2bff2dfaecfa72b20ab76cc84ed17d6
ms.sourcegitcommit: 974d5f2bccabe504583e387904851275567832e7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/18/2020
ms.locfileid: "83550591"
---
# <a name="isnull"></a>isnull()

유일한 인수를 계산 하 고 `bool` 인수가 null 값으로 계산 되는지를 나타내는 값을 반환 합니다.

```kusto
isnull(parse_json("")) == true
```

**구문**

`isnull(`*Expr*`)`

**반환**

값이 null 인지 여부에 따라 True 또는 false입니다.

**참고 사항**

* `string`값은 null 일 수 없습니다. [Isempty](./isemptyfunction.md) 를 사용 하 여 형식의 값이 비어 있는지 여부를 확인 `string` 합니다.

|x                |`isnull(x)`|
|-----------------|-----------|
|`""`             |`false`    |
|`"x"`            |`false`    |
|`parse_json("")`  |`true`     |
|`parse_json("[]")`|`false`    |
|`parse_json("{}")`|`false`    |

**예제**

```kusto
T | where isnull(PossiblyNull) | count
```