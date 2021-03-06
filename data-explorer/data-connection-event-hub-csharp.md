---
title: 'C를 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기 #'
description: '이 문서에서는 c #을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결을 만드는 방법에 대해 알아봅니다.'
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: f2f0011ea66b4951de9f4aa9eaca70b738b5e346
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714111"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>C를 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기 #

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-hub.md)
> * [한 번 클릭](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
이 문서에서는 c #을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발** 을 사용하도록 설정합니다.
* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.
* [클러스터 및 데이터베이스](create-cluster-database-csharp.md) 만들기
* [테이블 및 열 매핑](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster) 만들기
* [데이터베이스 및 테이블 정책](database-table-policies-csharp.md) 설정 (선택 사항)
* 수집할 [데이터를 사용 하 여 이벤트 허브](ingest-data-event-hub.md#create-an-event-hub)를 만듭니다. 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>이벤트 허브 데이터 연결 추가

다음 예제에서는 프로그래밍 방식으로 이벤트 허브 데이터 연결을 추가 하는 방법을 보여 줍니다. Azure Portal 사용 하 여 이벤트 허브 데이터 연결 추가에 대 한 [이벤트 허브에 연결을](ingest-data-event-hub.md#connect-to-the-event-hub) 참조 하세요.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
var compression = "None";
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat, compression: compression));
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용하는 구독 ID입니다.|
| clientId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 ID입니다.|
| clientSecret | *xxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 암호입니다.|
| resourceGroupName | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| clusterName | *mykustocluster* | 클러스터의 이름입니다.|
| databaseName | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| dataConnectionName | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| tableName | *StormEvents* | 대상 데이터베이스에 있는 대상 테이블의 이름입니다.|
| mappingRuleName | *StormEvents_CSV_Mapping* | 대상 테이블과 관련 된 열 매핑의 이름입니다.|
| dataFormat | *csv* | 메시지의 데이터 형식입니다.|
| eventHubResourceId | *리소스 ID* | 수집에 대 한 데이터를 보유 하는 이벤트 허브의 리소스 ID입니다. |
| consumerGroup | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|
| 압축 | *Gzip* 또는 *없음* | 데이터 압축의 유형입니다. |

## <a name="generate-data"></a>데이터 생성

데이터를 생성 하 고 이벤트 허브로 전송 하는 [샘플 앱](https://github.com/Azure-Samples/event-hubs-dotnet-ingest) 을 참조 하세요.

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

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](includes/data-explorer-data-connection-clean-resources-csharp.md)]