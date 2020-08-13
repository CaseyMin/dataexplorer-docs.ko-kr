---
title: Python을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결 만들기
description: 이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결을 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: lugoldbe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 89fc499e0e74bcd521c8c2fbc7c2038536c097da
ms.sourcegitcommit: f7f3ecef858c1e8d132fc10d1e240dcd209163bd
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 08/13/2020
ms.locfileid: "88201571"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-python-preview"></a>Python (미리 보기)을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결 만들기

> [!div class="op_single_selector"]
> * [포털](ingest-data-iot-hub.md)
> * [C#](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager 템플릿](data-connection-iot-hub-resource-manager.md)

[!INCLUDE [data-connector-intro](includes/data-connector-intro.md)]
이 문서에서는 Python을 사용 하 여 Azure 데이터 탐색기에 대 한 IoT Hub 데이터 연결을 만듭니다. 

## <a name="prerequisites"></a>필수 구성 요소

* 활성 구독이 있는 Azure 계정. [체험 계정을 만듭니다](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).
* [Python 3.4 이상](https://www.python.org/downloads/).
* [클러스터 및 데이터베이스](create-cluster-database-python.md)
* [테이블 및 열 매핑](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).
* [데이터베이스 및 테이블 정책](database-table-policies-python.md) (옵션).
* [공유 액세스 정책이 구성 된 IoT Hub](ingest-data-iot-hub.md#create-an-iot-hub)

[!INCLUDE [data-explorer-data-connection-install-package-python](includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>IoT Hub 데이터 연결 추가 

다음 예제에서는 프로그래밍 방식으로 IoT Hub 데이터 연결을 추가 하는 방법을 보여 줍니다. Azure Portal를 사용 하 여 Iot Hub 데이터 연결을 추가 [하려면 IoT Hub Azure 데이터 탐색기 테이블 연결을](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) 참조 하세요.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import IotHubDataConnection
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
#The IoT hub that is created as part of the Prerequisites
iot_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
shared_access_policy_name = "iothubforread"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=IotHubDataConnection(iot_hub_resource_id=iot_hub_resource_id, shared_access_policy_name=shared_access_policy_name, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**설정** | **제안 값** | **필드 설명**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테넌트 ID 디렉터리 ID 라고도 합니다.|
| subscriptionId | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 리소스를 만드는 데 사용 하는 구독 ID입니다.|
| client_id | *xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx-xxxxx-xxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 ID입니다.|
| client_secret | *xxxxxxxxxxxxxx* | 테 넌 트의 리소스에 액세스할 수 있는 응용 프로그램의 클라이언트 암호입니다. |
| resource_group_name | *testrg* | 클러스터를 포함 하는 리소스 그룹의 이름입니다.|
| cluster_name | *mykustocluster* | 클러스터의 이름입니다.|
| database_name | *mykustodatabase* | 클러스터에 있는 대상 데이터베이스의 이름입니다.|
| data_connection_name | *myeventhubconnect* | 원하는 데이터 연결 이름입니다.|
| table_name | *StormEvents* | 대상 데이터베이스에 있는 대상 테이블의 이름입니다.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | 대상 테이블과 관련 된 열 매핑의 이름입니다.|
| data_format | *csv* | 메시지의 데이터 형식입니다.|
| iot_hub_resource_id | *리소스 ID* | 수집할 데이터를 보유 하는 IoT hub의 리소스 ID입니다.|
| shared_access_policy_name | *iothubforread* | IoT Hub에 연결할 장치 및 서비스에 대 한 권한을 정의 하는 공유 액세스 정책의 이름입니다. |
| consumer_group | *$Default* | 이벤트 허브의 소비자 그룹입니다.|
| 위치 | *미국 중부* | 데이터 연결 리소스의 위치입니다.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](includes/data-explorer-data-connection-clean-resources-python.md)]
