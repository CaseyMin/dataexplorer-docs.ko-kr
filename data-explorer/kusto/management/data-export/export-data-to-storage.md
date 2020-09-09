---
title: 저장소로 데이터 내보내기-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 저장소로 데이터를 내보내는 방법을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/12/2020
ms.openlocfilehash: b470d017937ed6f2687016ab8a7cf53fed7b51ab
ms.sourcegitcommit: 993bc7b69096ab5516d3c650b9df97a1f419457b
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 09/09/2020
ms.locfileid: "89614480"
---
# <a name="export-data-to-storage"></a>저장소로 데이터 내보내기

쿼리를 실행 하 고 [저장소 연결 문자열](../../api/connection-strings/storage.md)에 의해 지정 된 첫 번째 결과 집합을 외부 저장소에 기록 합니다.

**구문**

`.export`[ `async` ] [ `compressed` ] `to` *OutputDataFormat* 
 `(` *StorageConnectionString* [ `,` ...] `)` [ `with` `(` *PropertyName* `=` *PropertyValue* [ `,` ...] `)` ] `<|` *쿼리*

**인수**

* `async`: 지정 된 경우 명령이 비동기 모드로 실행 됨을 나타냅니다.
  이 모드의 동작에 대 한 자세한 내용은 아래를 참조 하세요.

* `compressed`: 지정 된 경우 출력 저장소 아티팩트가 파일로 압축 됩니다 `.gz` . `compressionType`Parquet 파일을 snappy로 압축 하려면를 참조 하십시오. 

* *OutputDataFormat*: 명령으로 작성 된 저장소 아티팩트의 데이터 형식을 나타냅니다. 지원 되는 값은 `csv` ,, `tsv` `json` 및 `parquet` 입니다.

* *StorageConnectionString*: 데이터를 쓸 저장소를 나타내는 [저장소 연결 문자열](../../api/connection-strings/storage.md) 을 하나 이상 지정 합니다. 확장 가능한 쓰기를 위해 둘 이상의 저장소 연결 문자열을 지정할 수 있습니다. 이러한 각 연결 문자열은 저장소에 쓸 때 사용할 자격 증명을 나타내야 합니다.
  예를 들어 Azure Blob Storage에 쓸 때 자격 증명은 저장소 계정 키 이거나 blob을 읽고 쓰고 나열할 수 있는 권한이 있는 SAS (공유 액세스 키)가 될 수 있습니다.

> [!NOTE]
> Kusto cluster와 동일한 지역에 배치 된 저장소로 데이터를 내보내는 것이 좋습니다. 여기에는 다른 지역의 다른 클라우드 서비스로 전송 될 수 있도록 내보내는 데이터가 포함 됩니다. 쓰기는 로컬에서 수행 해야 하지만 읽기는 원격으로 수행 될 수 있습니다.

* *PropertyName* / *PropertyValue*: 0 개 이상의 선택적 내보내기 속성:

|속성        |Type    |Description                                                                                                                |
|----------------|--------|---------------------------------------------------------------------------------------------------------------------------|
|`sizeLimit`     |`long`  |기록 되는 단일 저장소 아티팩트의 크기 제한 (바이트)입니다 (압축 이전). 허용 되는 범위는 100MB (기본값)에서 1GB 까지입니다.|
|`includeHeaders`|`string`|`csv` / `tsv` 출력의 경우는 열 머리글의 생성을 제어 합니다. 는 `none` (기본값, 내보낼 헤더 없음) 중 하나 ( `all` 모든 저장소 아티팩트에 헤더 줄 내보내기) 또는 `firstFile` (헤더 줄을 첫 번째 저장소 아티팩트로만 내보내기) 중 하나를 사용할 수 있습니다.|
|`fileExtension` |`string`|저장소 아티팩트의 "확장" 부분을 나타냅니다 (예: `.csv` 또는 `.tsv` ). 압축을 사용 하는 경우도 `.gz` 추가 됩니다.|
|`namePrefix`    |`string`|생성 된 각 저장소 아티팩트 이름에 추가할 접두사를 나타냅니다. 지정 하지 않으면 임의의 접두사가 사용 됩니다.       |
|`encoding`      |`string`|텍스트를 인코딩하는 방법을 나타냅니다. `UTF8NoBOM` (기본값) 또는 `UTF8BOM` 입니다. |
|`compressionType`|`string`|사용할 압축 유형을 나타냅니다. 가능한 값은 `gzip` 또는 `snappy`입니다. 기본값은 `gzip`입니다. `snappy` (선택 사항)를 형식에 사용할 수 있습니다 `parquet` . |
|`distribution`   |`string`  |분포 힌트 ( `single` , `per_node` , `per_shard` )입니다. 값이 같으면 `single` 단일 스레드가 저장소에 기록 됩니다. 그렇지 않으면 내보내기는 쿼리를 병렬로 실행 하는 모든 노드에서 씁니다. [플러그 인 연산자 평가](../../query/evaluateoperator.md)를 참조 하세요. 기본값은 `per_shard`입니다.
|`distributed`   |`bool`  |분산 내보내기를 사용 하지 않거나 사용 하도록 설정 합니다. False로 설정 하는 것은 `single` 분포 힌트와 동일 합니다. 기본값은 true입니다.
|`persistDetails`|`bool`  |명령 결과를 유지 해야 함을 나타냅니다 (플래그 참조 `async` ). 는 `true` 비동기 실행에서로 기본 설정 되지만 호출자가 결과를 요구 하지 않는 경우 해제할 수 있습니다. 는 `false` 동기 실행에서로 기본 설정 되지만이를 설정할 수도 있습니다. |
|`parquetRowGroupSize`|`int`  |데이터 형식이 parquet 경우에만 해당 됩니다. 내보낸 파일의 행 그룹 크기를 제어 합니다. 기본 행 그룹 크기는 10만 레코드입니다.|

**결과**

명령은 생성 된 저장소 아티팩트를 설명 하는 테이블을 반환 합니다.
각 레코드는 단일 아티팩트를 설명 하 고 아티팩트의 저장소 경로 및이에 포함 된 데이터 레코드 수를 포함 합니다.

|경로|NumRecords|
|---|---|
|http://storage1.blob.core.windows.net/containerName/export_1_d08afcae2f044c1092b279412dcb571b.csv|10|
|http://storage1.blob.core.windows.net/containerName/export_2_454c0f1359e24795b6529da8a0101330.csv|15|

**비동기 모드**

`async`플래그가 지정 된 경우 명령은 비동기 모드로 실행 됩니다.
이 모드에서 명령은 작업 ID를 사용 하 여 즉시 반환 하 고, 데이터 내보내기는 완료 될 때까지 백그라운드에서 계속 됩니다. 명령에서 반환 된 작업 ID를 사용 하 여 다음 명령을 통해 진행 상황과 궁극적으로 결과를 추적할 수 있습니다.

* [. 작업 표시](../operations.md#show-operations): 진행률을 추적 합니다.
* [. 작업 세부 정보 표시](../operations.md#show-operation-details): 완료 결과를 가져옵니다.

예를 들어 성공적으로 완료 되 면 다음을 사용 하 여 결과를 검색할 수 있습니다.

```kusto
.show operation f008dc1e-2710-47d8-8d34-0d562f5f8615 details
```

**예** 

이 예에서 Kusto는 쿼리를 실행 한 다음 쿼리에서 생성 된 첫 번째 레코드 집합을 하나 이상의 압축 된 CSV blob으로 내보냅니다.
열 이름 레이블은 각 blob에 대 한 첫 번째 행으로 추가 됩니다.

```kusto 
.export
  async compressed
  to csv (
    h@"https://storage1.blob.core.windows.net/containerName;secretKey",
    h@"https://storage1.blob.core.windows.net/containerName2;secretKey"
  ) with (
    sizeLimit=100000,
    namePrefix=export,
    includeHeaders=all,
    encoding =UTF8NoBOM
  )
  <| myLogs | where id == "moshe" | limit 10000
```

#### <a name="known-issues"></a>알려진 문제

**내보내기 명령 중에 발생 한 오류**

* Export 명령은 실행 중에 transiently 실패할 수 있습니다. 내보내기 명령이 실패 하면 이미 저장소에 기록 된 아티팩트가 삭제 되지 않습니다. 이러한 아티팩트는 저장소에 유지 됩니다. 명령이 실패 하면 일부 아티팩트가 작성 된 경우에도 내보내기가 불완전 한 것으로 가정 합니다. 성공적으로 완료 될 때 내보낸 아티팩트와 명령의 완료를 모두 추적 하는 가장 좋은 방법은 [. show operations](../operations.md#show-operations) 및 [. show operation details](../operations.md#show-operation-details) 명령을 사용 하는 것입니다.

* 기본적으로 내보내기 명령은 데이터를 포함 하는 모든 [익스텐트가](../extents-overview.md) 저장소에 동시에 쓰기를 내보내는 것으로 배포 됩니다. 대규모 내보내기에서 이러한 익스텐트 수가 높으면 저장소 제한 또는 일시적인 저장소 오류를 초래 하는 저장소의 부하가 높을 수 있습니다. 이러한 경우 내보내기 명령에 제공 된 저장소 계정의 수를 늘리거나 (부하를 계정 간에 분산 함) 배포 힌트를로 설정 하 여 동시성을 줄이려는 것이 좋습니다 `per_node` (명령 속성 참조). 배포를 완전히 사용 하지 않도록 설정 하는 것도 가능 하지만이는 명령 성능에 크게 영향을 줄 수 있습니다.
