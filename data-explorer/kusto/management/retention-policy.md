---
title: Kusto 보존 정책 관리-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 보존 정책에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/19/2020
ms.openlocfilehash: 79cac49a553a2b906947b4c85948b67718641587
ms.sourcegitcommit: ef3d919dee27c030842abf7c45c9e82e6e8350ee
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92630078"
---
# <a name="retention-policy-command"></a>보존 정책 명령

이 문서에서는 [보존 정책을](retentionpolicy.md)만들고 변경 하는 데 사용 되는 제어 명령을 설명 합니다.

## <a name="show-retention-policy"></a>보존 정책 표시

```kusto
.show <entity_type> <database_or_table_or_materialized_view> policy retention

.show <entity_type> *  policy retention
```

* `entity_type` : 테이블, 구체화 된 뷰 또는 데이터베이스
* `database_or_table_or_materialized_view`: `database_name` 또는 `database_name.table_name` 또는 `table_name` (데이터베이스 컨텍스트) 또는 `materialized_view_name`

**예제**

데이터베이스에 대 한 보존 정책을 표시 합니다 `MyDatabase` .

```kusto
.show database MyDatabase policy retention
```

## <a name="delete-retention-policy"></a>보존 정책 삭제

데이터 보존 정책을 삭제 하는 것은 affectively에서 무제한 데이터 보존을 설정 하는 것입니다.

테이블의 데이터 보존 정책을 삭제 하면 테이블이 데이터베이스 수준에서 보존 정책을 파생 시킵니다.

```kusto
.delete <entity_type> <database_or_table_or_materialized_view> policy retention
```

* `entity_type` : 테이블, 구체화 된 뷰 또는 데이터베이스
* `database_or_table_or_materialized_view`: `database_name` 또는 `database_name.table_name` 또는 `table_name` (데이터베이스 컨텍스트) 또는 `materialized_view_name`

**예제**

테이블에 대 한 보존 정책을 삭제 합니다 `MyTable1` .

```kusto
.delete table MyTable policy retention
```


## <a name="alter-retention-policy"></a>보존 정책 변경

```kusto
.alter <entity_type> <database_or_table_or_materialized_view> policy retention <retention_policy>

.alter tables (<table_name> [, ...]) policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_or_materialized_view> policy retention <retention_policy>

.alter-merge <entity_type> <database_or_table_or_materialized_view> policy retention [softdelete = <timespan>] [recoverability = disabled|enabled]
```

* `entity_type` : 테이블 또는 데이터베이스 또는 구체화 된 뷰
* `database_or_table_or_materialized_view`: `database_name` 또는 `database_name.table_name` 또는 `table_name` (데이터베이스 컨텍스트) 또는 `materialized_view_name`
* `table_name` : 데이터베이스 컨텍스트에 있는 테이블의 이름입니다.  와일드 카드 ( `*` 여기서는 사용할 수 있음).
* `retention_policy` :

```kusto
    "{ 
        \"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Disabled\"
    }" 
```

**예**

데이터베이스에 대 한 보존 정책을 표시 합니다 `MyDatabase` .

```kusto
.show database MyDatabase policy retention
```

일시 삭제 기간이 10 일인 보존 정책을 설정 하 고 데이터 복구를 사용 하지 않도록 설정 합니다.

```kusto
.alter-merge table Table1 policy retention softdelete = 10d recoverability = disabled

.alter-merge materialized-view View1 policy retention softdelete = 10d recoverability = disabled
```

일시 삭제 기간이 10 일인 보존 정책을 설정 하 고 데이터 복구 기능을 사용 하도록 설정 합니다.

```kusto
.alter table Table1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"

.alter materialized-view View1 policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

위와 동일한 보존 정책을 설정 하지만 이번에는 여러 테이블 (Table1, Table2 및 Table3)에 대해 다음을 수행 합니다.

```kusto
.alter tables (Table1, Table2, Table3) policy retention "{\"SoftDeletePeriod\": \"10.00:00:00\", \"Recoverability\": \"Enabled\"}"
```

100 년의 기본값을 사용 하 여 보존 정책을 설정 합니다.

```kusto
.alter table Table1 policy retention "{}"
```