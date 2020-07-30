---
title: 'C를 사용 하 여 고객 관리 키 구성 #'
description: 이 문서에서는 Azure 데이터 탐색기의 데이터에서 고객이 관리 하는 키 암호화를 구성 하는 방법을 설명 합니다.
author: orspod
ms.author: orspodek
ms.reviewer: itsagui
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 84d21c7cedd77a409e0d5e1c1ca0cc5f93fdad16
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87350250"
---
# <a name="configure-customer-managed-keys-using-c"></a>C를 사용 하 여 고객 관리 키 구성 #

> [!div class="op_single_selector"]
> * [포털](customer-managed-keys-portal.md)
> * [C#](customer-managed-keys-csharp.md)
> * [Azure Resource Manager 템플릿](customer-managed-keys-resource-manager.md)
> * [CLI](customer-managed-keys-cli.md)
> * [PowerShell](customer-managed-keys-powershell.md)

[!INCLUDE [data-explorer-configure-customer-managed-keys](includes/data-explorer-configure-customer-managed-keys.md)]

[!INCLUDE [data-explorer-configure-customer-managed-keys part 2](includes/data-explorer-configure-customer-managed-keys-b.md)]

## <a name="configure-encryption-with-customer-managed-keys"></a>고객 관리 키를 사용 하 여 암호화 구성

이 섹션에서는 Azure 데이터 탐색기 c # 클라이언트를 사용 하 여 고객 관리 키 암호화를 구성 하는 방법을 보여 줍니다. 

### <a name="prerequisites"></a>필수 구성 요소

* Visual Studio 2019가 설치되지 않은 경우 **체험판** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)을 다운로드하고 사용할 수 있습니다. Visual Studio를 설치하는 동안 **Azure 개발**을 사용하도록 설정합니다.

* Azure 구독이 아직 없는 경우 시작하기 전에 [Azure 체험 계정](https://azure.microsoft.com/free/)을 만듭니다.

### <a name="install-c-nuget"></a>C # NuGet 설치

* [Azure 데이터 탐색기 (Kusto) NuGet 패키지](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)를 설치 합니다.

* 인증을 위해 [system.identitymodel. ActiveDirectory NuGet 패키지](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) 를 설치 합니다.

### <a name="authentication"></a>인증

이 문서의 예제를 실행 하려면 리소스에 액세스할 수 있는 [AZURE AD 응용 프로그램](/azure/active-directory/develop/howto-create-service-principal-portal) 및 서비스 주체를 만듭니다. 구독 범위에서 역할 할당을 추가 하 고 필수, 및를 가져올 수 있습니다 `Directory (tenant) ID` `Application ID` `Client Secret` .

### <a name="configure-cluster"></a>클러스터 구성

기본적으로 Azure 데이터 탐색기 암호화는 Microsoft 관리 키를 사용 합니다. Azure 데이터 탐색기 클러스터가 고객이 관리 하는 키를 사용 하도록 구성 하 고 클러스터와 연결할 키를 지정 합니다.

1. 다음 코드를 사용 하 여 클러스터를 업데이트 합니다.

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
    var clusterName = "mykustocluster";
    var keyName = "myKey";
    var keyVersion = "5b52b20e8d8a42e6bd7527211ae32654";
    var keyVaultUri = "https://mykeyvault.vault.azure.net/";
    var keyVaultProperties = new KeyVaultProperties (keyName, keyVersion, keyVaultUri);
    var clusterUpdate = new ClusterUpdate(keyVaultProperties: keyVaultProperties);
    await kustoManagementClient.Clusters.UpdateAsync(resourceGroupName, clusterName, clusterUpdate);
    ```

1. 다음 명령을 실행 하 여 클러스터가 성공적으로 업데이트 되었는지 확인 합니다.

    ```csharp
    kustoManagementClient.Clusters.Get(resourceGroupName, clusterName);
    ```

    결과에 값이 포함 되어 있으면 `ProvisioningState` `Succeeded` 클러스터가 성공적으로 업데이트 된 것입니다.

## <a name="update-the-key-version"></a>키 버전 업데이트

새 버전의 키를 만드는 경우 새 버전을 사용 하도록 클러스터를 업데이트 해야 합니다. 먼저를 호출 `Get-AzKeyVaultKey` 하 여 최신 버전의 키를 가져옵니다. 그런 다음 클러스터 [구성](#configure-cluster)에 표시 된 것 처럼 새 버전의 키를 사용 하도록 클러스터의 주요 자격 증명 모음 속성을 업데이트 합니다.

## <a name="next-steps"></a>다음 단계

* [Azure에서 Azure 데이터 탐색기 클러스터 보호](security.md)
* [Azure 데이터 탐색기 클러스터에 대 한 관리 id 구성](managed-identities.md)
* 미사용 암호화를 사용 하도록 설정 하 여 [Azure 데이터 탐색기-Azure Portal에서 클러스터를 보호](manage-cluster-security.md) 합니다.
* [Azure Resource Manager 템플릿을 사용 하 여 고객 관리 키 구성](customer-managed-keys-resource-manager.md)


