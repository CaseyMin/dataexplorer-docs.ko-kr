---
title: Python을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기
description: 이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결을 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: how-to
ms.date: 10/07/2019
ms.openlocfilehash: b2877cb3de37610a9e526f659ccdb4a93d2d6568
ms.sourcegitcommit: 574296b9a84084de031684a65f32b6c1bd1a4858
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/17/2020
ms.locfileid: "94714145"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Python을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결 만들기

> [!div class="op_single_selector"]
> * [포털](ingest-data-event-hub.md)
> * [한 번 클릭](one-click-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-event-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기에 대 한 이벤트 허브 데이터 연결을 만듭니다. 

## <a name="prerequisites"></a>사전 요구 사항

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Python 3.4 이상](https://www.python.org/downloads/).
* [클러스터 및 데이터베이스](create-cluster-database-python.md)
* [테이블 및 열 매핑](./net-sdk-ingest-data.md#create-a-table-on-your-test-cluster).
* [데이터베이스 및 테이블 정책](database-table-policies-python.md) (옵션).
* 수집 [에 대 한 데이터를 포함 하는 이벤트 허브](ingest-data-event-hub.md#create-an-event-hub)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>이벤트 허브 데이터 연결 추가

다음 예제에서는 프로그래밍 방식으로 이벤트 허브 데이터 연결을 추가 하는 방법을 보여 줍니다. Azure Portal 사용 하 여 이벤트 허브 데이터 연결 추가에 대 한 [이벤트 허브에 연결을](ingest-data-event-hub.md#connect-to-the-event-hub) 참조 하세요.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용하는 구독 ID입니다.|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 ID입니다.|
| client_secret | *xxxxxxxxxxxxxx* | 테넌트의 리소스에 액세스할 수 있는 애플리케이션의 클라이언트 암호입니다. |
| resource_group_name | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| cluster_name | *mykustocluster* | 클러스터의 이름입니다.|
| database_name | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| data_connection_name | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| table_name | *StormEvents* | 대상 데이터베이스에 있는 대상 테이블의 이름입니다.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 대상 테이블과 관련 된 열 매핑의 이름입니다.|
| data_format | *csv* | 메시지의 데이터 형식입니다.|
| event_hub_resource_id | *리소스 ID* | 수집에 대 한 데이터를 보유 하는 이벤트 허브의 리소스 ID입니다. |
| consumer_group | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]