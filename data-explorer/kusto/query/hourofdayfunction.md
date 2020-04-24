---
title: 시간() - Azure 데이터 탐색기 | 마이크로 소프트 문서
description: 이 문서에서는 Azure 데이터 탐색기의 시간()에 대해 설명합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 08fdb45af5eec7f71d491725ea58a7d72c06371b
ms.sourcegitcommit: 47a002b7032a05ef67c4e5e12de7720062645e9e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 04/15/2020
ms.locfileid: "81514079"
---
# <a name="hourofday"></a>hourofday()

지정된 날짜의 시간 번호를 나타내는 정수 번호를 반환합니다.

```kusto
hourofday(datetime(2015-12-14 18:54)) == 18
```

**구문**

`hourofday(`*a_date*`)`

**인수**

* `a_date`: `datetime`입니다.

**반환**

`hour number`(0-23)