---
title: 이벤트 허브에서 수집-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 이벤트 허브 수집을 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: how-to
ms.date: 08/13/2020
ms.openlocfilehash: 8b24d6f771eaa4004b36a1b3171eb593e2fc0f6c
ms.sourcegitcommit: f354accde64317b731f21e558c52427ba1dd4830
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/26/2020
ms.locfileid: "88874888"
---
# <a name="connect-to-event-hub"></a>Event Hub에 연결


[Azure Event Hubs](https://docs.microsoft.com/azure/event-hubs/event-hubs-about) 는 빅 데이터 스트리밍 플랫폼 및 이벤트 수집 서비스입니다. Azure 데이터 탐색기는 고객이 관리 하는 Event Hubs에서 지속적인 수집을 제공 합니다.

이벤트 허브 수집 파이프라인은 여러 단계를 사용 하 여 Azure 데이터 탐색기에 이벤트를 전송 합니다. 먼저 Azure Portal에서 이벤트 허브를 만듭니다. 그런 다음 지정 된 수집 [속성](#set-ingestion-properties)을 사용 하 여 [특정 형식의 데이터가](#data-format)수집 되는 대상 테이블 Azure 데이터 탐색기를 만듭니다. 이벤트 허브 연결에서 [이벤트 라우팅을](#set-events-routing)알아야 합니다. [이벤트 시스템 속성 매핑에](#set-event-system-properties-mapping)따라 선택한 속성에 데이터를 포함 합니다. 이 프로세스는 [Azure Portal](ingest-data-event-hub.md)를 통해 관리 하거나 [c #](data-connection-event-hub-csharp.md) 또는 [Python](data-connection-event-hub-python.md)을 사용 하 여 프로그래밍 방식으로 관리 하거나 [Azure Resource Manager 템플릿과](data-connection-event-hub-resource-manager.md)함께 관리할 수 있습니다.

## <a name="data-format"></a>데이터 형식

* [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata?view=azure-dotnet) 개체의 형태로 이벤트 허브에서 데이터를 읽습니다.
* 이벤트 페이로드는 [Azure 데이터 탐색기에서 지 원하는 형식](ingestion-supported-formats.md)중 하나로 수집 수 있습니다.
* 압축 알고리즘을 사용 하 여 데이터를 압축할 수 있습니다 `GZip` . 수집 속성으로 지정 해야 합니다 `Compression` [ingestion property](#set-ingestion-properties).

    > [!Note]
    > * 압축 형식 (Avro, Parquet, ORC)에는 데이터 압축이 지원 되지 않습니다.
    > * 사용자 지정 인코딩 및 포함 된 [시스템 속성](#set-event-system-properties-mapping) 은 압축 된 데이터에서 지원 되지 않습니다.
    
## <a name="set-ingestion-properties"></a>수집 속성 설정

수집 속성은 수집 프로세스, 데이터를 라우팅하는 위치 및이를 처리 하는 방법을 지시 합니다. [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)를 사용 하 여 이벤트 수집의 수집 [속성](ingestion-properties.md) 을 지정할 수 있습니다. 다음 속성을 설정할 수 있습니다.

|속성 |설명|
|---|---|
| 테이블 | 기존 대상 테이블의 이름 (대/소문자 구분)입니다. `Table`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| 서식 | 데이터 형식입니다. `Data format`블레이드의 집합을 재정의 `Data Connection` 합니다. |
| IngestionMappingReference | 사용할 기존 수집 [매핑의](kusto/management/create-ingestion-mapping-command.md) 이름입니다. `Column mapping`블레이드의 집합을 재정의 `Data Connection` 합니다.|
| 압축 | 데이터 압축 `None` (기본값) 또는 `GZip` 압축|
| Encoding | 데이터 인코딩입니다. 기본값은 UTF8입니다. 은 [.net에서 지원 되는 인코딩을](https://docs.microsoft.com/dotnet/api/system.text.encoding?view=netframework-4.8#remarks)사용할 수 있습니다. |
| 태그 (미리 보기) | JSON 배열 문자열로 형식이 지정 된 수집 데이터와 연결할 [태그](kusto/management/extents-overview.md#extent-tagging) 의 목록입니다. 태그를 사용 하는 경우 [성능에 영향을 미칩니다](kusto/management/extents-overview.md#performance-notes-1) . |

<!--| Database | Name of the existing target database.|-->
<!--| Tags | String representing [tags](https://docs.microsoft.com/azure/kusto/management/extents-overview#extent-tagging) that will be attached to resulting extent. |-->

## <a name="set-events-routing"></a>이벤트 라우팅 설정

Azure 데이터 탐색기 클러스터에 대 한 이벤트 허브 연결을 설정 하는 경우 대상 테이블 속성 (테이블 이름, 데이터 형식, 압축 및 매핑)을 지정 합니다. 데이터의 기본 라우팅은 라고도 `static routing` 합니다.
이벤트 속성을 사용 하 여 각 이벤트에 대 한 대상 테이블 속성을 지정할 수도 있습니다. 연결은 [EventData](https://docs.microsoft.com/dotnet/api/microsoft.servicebus.messaging.eventdata.properties?view=azure-dotnet#Microsoft_ServiceBus_Messaging_EventData_Properties)에 지정 된 대로 데이터를 동적으로 라우팅하고이 이벤트에 대 한 정적 속성을 재정의 합니다.

다음 예제에서는 이벤트 허브 세부 정보를 설정 하 고 날씨 메트릭 데이터를 테이블에 보냅니다 `WeatherMetrics` .
데이터의 `json` 형식입니다. `mapping1` 는 테이블에 미리 정의 되어 `WeatherMetrics` 있습니다.

```csharp
var eventHubNamespaceConnectionString=<connection_string>;
var eventHubName=<event_hub>;

// Create the data
var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = 32 }; 
var data = JsonConvert.SerializeObject(metric);

// Create the event and add optional "dynamic routing" properties
var eventData = new EventData(Encoding.UTF8.GetBytes(data));
eventData.Properties.Add("Table", "WeatherMetrics");
eventData.Properties.Add("Format", "json");
eventData.Properties.Add("IngestionMappingReference", "mapping1");
eventData.Properties.Add("Tags", "['mydatatag']");

// Send events
var eventHubClient = EventHubClient.CreateFromConnectionString(eventHubNamespaceConnectionString, eventHubName);
eventHubClient.Send(eventData);
eventHubClient.Close();
```

## <a name="set-event-system-properties-mapping"></a>이벤트 시스템 속성 매핑 설정

시스템 속성은 이벤트를 큐에 넣을 때 Event Hubs 서비스에서 설정 하는 속성을 저장 합니다. Azure 데이터 탐색기 이벤트 허브 연결에서는 선택한 속성을 테이블의 데이터에 포함 합니다.

> [!Note]
> * 시스템 속성은 단일 레코드 이벤트에 대해 지원 됩니다.
> * 압축 된 데이터에서는 시스템 속성이 지원 되지 않습니다.
> * `csv`매핑의 경우 속성은 레코드의 시작 부분에서 아래 표에 나열 된 순서 대로 추가 됩니다. 매핑의 경우 속성 `json` 은 다음 표의 속성 이름에 따라 추가 됩니다.

### <a name="event-hub-exposes-the-following-system-properties"></a>이벤트 허브는 다음 시스템 속성을 노출 합니다.

|속성 |데이터 형식 |설명|
|---|---|---|
| x-opt-enqueued-time |Datetime | 이벤트를 큐에 넣은 UTC 시간 |
| x-opt-sequence-number |long | 이벤트 허브의 파티션 스트림 내에 있는 이벤트의 논리적 시퀀스 번호입니다.
| x-opt-offset |문자열 | 이벤트 허브 파티션 스트림의 이벤트 오프셋입니다. 오프셋 식별자는 이벤트 허브 스트림의 파티션 내에서 고유 합니다. |
| x opt-게시자 |문자열 | 게시자 끝점에 메시지를 보낸 경우 게시자 이름 |
| x-opt-partition-key |문자열 |이벤트를 저장 한 해당 파티션의 파티션 키입니다. |

테이블의 **데이터 원본** 섹션에서 **이벤트 시스템 속성** 을 선택한 경우 테이블 스키마 및 매핑에 속성을 포함 해야 합니다.

### <a name="examples"></a>예

#### <a name="table-schema-example"></a>테이블 스키마 예제

테이블 스키마 명령 (데이터에 다음이 포함 된 경우)을 사용 하 여 테이블 스키마를 만들거나 변경 합니다.
* 열 `Timespan` , `Metric` 및 `Value`  
* 속성 `x-opt-enqueued-time` 및 `x-opt-offset`

```kusto
    .create-merge table TestTable (TimeStamp: datetime, Metric: string, Value: int, EventHubEnqueuedTime:datetime, EventHubOffset:long)
```

#### <a name="csv-mapping-example"></a>CSV 매핑 예

다음 명령을 실행 하 여 레코드의 시작 부분에 데이터를 추가 합니다.
속성은 위의 표에 나열 된 순서 대로 레코드의 시작 부분에 추가 됩니다.
서 수 값은 매핑된 시스템 속성에 따라 열 서 수가 변경 되는 CSV 매핑에 중요 합니다.

```kusto
    .create table TestTable ingestion csv mapping "CsvMapping1"
    '['
    '   { "column" : "Timespan", "Properties":{"Ordinal":"2"}},'
    '   { "column" : "Metric", "Properties":{"Ordinal":"3"}},'
    '   { "column" : "Value", "Properties":{"Ordinal":"4"}},'
    '   { "column" : "EventHubEnqueuedTime", "Properties":{"Ordinal":"0"}},'
    '   { "column" : "EventHubOffset", "Properties":{"Ordinal":"1"}}'
    ']'
```
 
#### <a name="json-mapping-example"></a>JSON 매핑 예제

*데이터 연결* 블레이드 *이벤트 시스템 속성* 목록에 표시 된 대로 시스템 속성 이름을 사용 하 여 데이터를 추가 합니다. 다음을 실행합니다.

```kusto
    .create table TestTable ingestion json mapping "JsonMapping1"
    '['
    '    { "column" : "Timespan", "Properties":{"Path":"$.timestamp"}},'
    '    { "column" : "Metric", "Properties":{"Path":"$.metric"}},'
    '    { "column" : "Value", "Properties":{"Path":"$.metric_value"}},'
    '    { "column" : "EventHubEnqueuedTime", "Properties":{"Path":"$.x-opt-enqueued-time"}},'
    '    { "column" : "EventHubOffset", "Properties":{"Path":"$.x-opt-offset"}}'
    ']'
```

## <a name="create-event-hub-connection"></a>Event Hub 연결 만들기

> [!Note]
> 최상의 성능을 위해 Azure 데이터 탐색기 클러스터와 동일한 지역에 모든 리소스를 만듭니다.

### <a name="create-an-event-hub"></a>Event Hub 만들기

아직 없는 경우 [이벤트 허브를 만듭니다](https://docs.microsoft.com/azure/event-hubs/event-hubs-create). 템플릿은 [이벤트 허브를 만드는](ingest-data-event-hub.md#create-an-event-hub) 방법 가이드에서 찾을 수 있습니다.

> [!Note]
> * 파티션 수는 변경할 수 없으므로 파티션 수를 설정할 때 장기적인 규모를 고려해야 합니다.
> * 소비자 그룹은 소비자 당 고유 *해야* 합니다. Azure 데이터 탐색기 연결 전용 소비자 그룹을 만듭니다.

#### <a name="generate-data"></a>데이터 생성

* 데이터를 생성 하 고 이벤트 허브로 전송 하는 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 을 참조 하세요.

이벤트는 최대 크기 제한까지 하나 이상의 레코드를 포함할 수 있습니다. 다음 샘플에서는 두 개의 이벤트를 보냅니다. 각각에는 5 개의 레코드가 추가 됩니다.

```csharp
var events = new List<EventData>();
var data = string.Empty;
var recordsPerEvent = 5;
var rand = new Random();
var counter = 0;

for (var i = 0; i < 10; i++)
{
    // Create the data
    var metric = new Metric { Timestamp = DateTime.UtcNow, MetricName = "Temperature", Value = rand.Next(-30, 50) }; 
    var data += JsonConvert.SerializeObject(metric) + Environment.NewLine;
    counter++;

    // Create the event
    if (counter == recordsPerEvent)
    {
        var eventData = new EventData(Encoding.UTF8.GetBytes(data));
        events.Add(eventData);

        counter = 0;
        data = string.Empty;
    }
}

// Send events
eventHubClient.SendAsync(events).Wait();
```

## <a name="next-steps"></a>다음 단계

* [Event Hub에서 Azure Data Explorer로 데이터 수집](ingest-data-event-hub.md)
* [C를 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기 #](data-connection-event-hub-csharp.md)
* [Python을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기](data-connection-event-hub-python.md)
* [Azure Resource Manager 템플릿을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기](data-connection-event-hub-resource-manager.md)
