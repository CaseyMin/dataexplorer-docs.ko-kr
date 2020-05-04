---
title: database () (범위 함수)-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터베이스 () (범위 함수)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: e3f874ecfc0bb1872f08efa3269c73b02971e4f3
ms.sourcegitcommit: d885c0204212dd83ec73f45fad6184f580af6b7e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/04/2020
ms.locfileid: "82737643"
---
# <a name="database-scope-function"></a>database () (범위 함수)

::: zone pivot="azuredataexplorer"

쿼리 참조를 클러스터 범위 내의 특정 데이터베이스로 변경 합니다. 

```kusto
database('Sample').StormEvents
cluster('help').database('Sample').StormEvents
```

**구문**

`database(`*stringConstant*`)`

**인수**

* *Stringconstant*: 참조 되는 데이터베이스의 이름입니다. 확인 된 데이터베이스는 또는 `DatabaseName` `PrettyName`중 하나일 수 있습니다. 인수는 쿼리를 실행 하기 전에 반드시 _일정_ 해야 합니다. 즉, 하위 쿼리 계산에서 가져올 수 없습니다.

**참고 사항**

* 원격 클러스터 및 원격 데이터베이스에 액세스 하려면 [cluster ()](clusterfunction.md) 범위 함수를 참조 하세요.
* 클러스터 간 및 데이터베이스 간 쿼리에 대 한 자세한 내용은 여기를 참조 [하세요](cross-cluster-or-database-queries.md) .

## <a name="examples"></a>예

### <a name="use-database-to-access-table-of-other-database"></a>데이터베이스 ()를 사용 하 여 다른 데이터베이스의 테이블에 액세스 합니다. 

```kusto
database('Samples').StormEvents | count
```

|개수|
|---|
|59066|

### <a name="use-database-inside-let-statements"></a>Let 문 내에서 database () 사용 

위와 동일한 쿼리를 데이터베이스 () 함수로 전달 되는 매개 변수 `dbName` 를 받는 인라인 함수 (let 문)를 사용 하도록 다시 작성할 수 있습니다.

```kusto
let foo = (dbName:string)
{
    database(dbName).StormEvents | count
};
foo('help')
```

|개수|
|---|
|59066|

### <a name="use-database-inside-functions"></a>함수 내에서 database () 사용 

위와 동일한 쿼리를 데이터베이스 () 함수로 전달 되는 매개 변수 `dbName` 를 받는 함수에서 사용 하도록 다시 작성할 수 있습니다.

```kusto
.create function foo(dbName:string)
{
    database(dbName).StormEvents | count
};
```

**참고:** 이러한 함수는 로컬 에서만 사용할 수 있고 클러스터 간 쿼리에서는 사용할 수 없습니다.

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end
