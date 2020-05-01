---
title: 데이터 분할 정책 관리-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 데이터 분할 정책 관리에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/04/2020
ms.openlocfilehash: 1ad9b359422b51084f1be1c64d27d656313d9296
ms.sourcegitcommit: 1faf502280ebda268cdfbeec2e8ef3d582dfc23e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/01/2020
ms.locfileid: "82616323"
---
# <a name="data-partitioning-policy-management"></a>데이터 분할 정책 관리

데이터 분할 정책은 [여기](../management/partitioningpolicy.md)에 자세히 설명 되어 있습니다.

## <a name="show-policy"></a>정책 표시

```kusto
.show table [table_name] policy partitioning
```

`.show` 명령은 테이블에 적용 된 분할 정책을 표시 합니다.

### <a name="output"></a>출력

|정책 이름 | 엔터티 이름 | 정책 | 자식 엔터티 | 엔터티 유형
|---|---|---|---|---
|DataPartitioning | 테이블 이름 | 정책 개체의 JSON serialization | null | 테이블

## <a name="alter-and-alter-merge-policy"></a>alter 및 alter-merge 정책

```kusto
.alter table [table_name] policy partitioning @'policy object, serialized as JSON'

.alter-merge table [table_name] policy partitioning @'partial policy object, serialized as JSON'
```

명령을 `.alter` 사용 하 여 테이블에 적용 된 분할 정책을 변경할 수 있습니다.

이 명령에는 [Databaseadmin](access-control/role-based-authorization.md) 권한이 필요 합니다.

정책에 대 한 변경 내용을 적용 하는 데 최대 1 시간이 걸릴 수 있습니다.

### <a name="examples"></a>예

#### <a name="setting-all-properties-of-the-policy-explicitly-at-table-level"></a>테이블 수준에서 정책에 대 한 모든 속성을 명시적으로 설정

```kusto
.alter table [table_name] policy partitioning @'{'
  '"PartitionKeys": ['
    '{'
      '"ColumnName": "my_string_column",'
      '"Kind": "Hash",'
      '"Properties": {'
        '"Function": "XxHash64",'
        '"MaxPartitionCount": 256,'
      '}'
    '},'
    '{'
      '"ColumnName": "my_datetime_column",'
      '"Kind": "UniformRange",'
      '"Properties": {'
        '"Reference": "1970-01-01T00:00:00",'
        '"RangeSize": "1.00:00:00"'
      '}'
    '}'
  ']'
'}'
```

#### <a name="setting-a-specific-property-of-the-policy-explicitly-at-table-level"></a>테이블 수준에서 정책의 특정 속성을 명시적으로 설정

정책의를 `EffectiveDateTime` 다른 값으로 설정 하려면 다음 명령을 사용 합니다.

```kusto
.alter-merge table [table_name] policy partitioning @'{"EffectiveDateTime":"2020-01-01"}'
```

## <a name="delete-policy"></a>정책 삭제

```kusto
.delete table [table_name] policy partitioning
```

`.delete` 명령은 지정 된 테이블의 파티션 정책을 삭제 합니다.
