---
title: Logic Apps를 사용 하 여 Azure에서 Kusto 쿼리를 자동으로 실행 데이터 탐색기
description: Logic Apps를 사용 하 여 Kusto 쿼리 및 명령을 자동으로 실행 하 고 예약 하는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: docohe
ms.service: data-explorer
ms.topic: how-to
ms.date: 04/14/2020
ms.openlocfilehash: a4f79fc367e2769dfe3bf51ed5ad035a7d233ea4
ms.sourcegitcommit: a7458819e42815a0376182c610aba48519501d92
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/28/2020
ms.locfileid: "92902687"
---
# <a name="microsoft-logic-app-and-azure-data-explorer-preview"></a>Microsoft 논리 앱 및 Azure 데이터 탐색기 (미리 보기)

Azure Kusto 논리 앱 커넥터를 사용 하면 [Microsoft 논리 앱](/azure/logic-apps/logic-apps-what-are-logic-apps) 커넥터를 사용 하 여 예약 되거나 트리거된 작업의 일부로 kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다.

논리 앱과 전원 자동화는 동일한 커넥터에서 빌드됩니다. 따라서 전원 자동화에 적용 되는 [제한 사항](../../flow.md#limitations), [작업](../../flow.md#flow-actions), [인증](../../flow.md#authentication) 및 [사용 예제](../../flow-usage.md) 는 [전원 자동화 설명서 페이지](../../flow.md)에 설명 된 대로 Logic Apps에도 적용 됩니다.

## <a name="how-to-create-a-logic-app-with-azure-data-explorer"></a>Azure 데이터 탐색기을 사용 하 여 논리 앱을 만드는 방법

1. [Microsoft Azure portal](https://ms.portal.azure.com/)를 엽니다. 
1. `logic app`를 검색하여 선택합니다.

    :::image type="content" source="./images/logicapps/logicapp-search.png" alt-text="Azure Portal Azure 데이터 탐색기에서 ' 논리 앱 '을 검색 하는 중" lightbox="./images/logicapps/logicapp-search.png#lightbox":::

1. **+추가** 를 선택합니다.

    ![논리 앱 추가](./Images/logicapps/logicapp-add.png)

1. 양식에 필요한 세부 정보를 입력 합니다.
    * 구독
    * 리소스 그룹
    * 논리 앱 이름
    * 지역 또는 통합 서비스 환경
    * 위치
    * 로그 분석 설정 또는 해제
1. **검토 + 만들기** 를 선택합니다.

    ![논리 앱 만들기](./Images/logicapps/logicapp-create-new.png)

1. 논리 앱을 만들 때 **편집** 을 선택 합니다.

    ![논리 앱 디자이너 편집](./Images/logicapps/logicapp-editdesigner.png "logicapp-editdesigner")

1. 빈 논리 앱을 만듭니다.

    ![논리 앱 빈 템플릿](./Images/logicapps/logicapp-blanktemplate.png "logicapp-blanktemplate")

1. 되풀이 작업을 추가 하 고 **Azure Kusto를** 선택 합니다.

    ![논리 앱 Kusto Flow 커넥터](./Images/logicapps/logicapp-kustoconnector.png "logicapp-kustoconnector")

## <a name="next-steps"></a>다음 단계

* 되풀이 작업을 구성 하는 방법에 대 한 자세한 내용은 [Power 자동화 설명서 페이지](../../flow.md) 를 참조 하세요.
* 논리 앱 작업을 구성 하는 방법에 대 한 몇 가지 [사용 예](../../flow-usage.md) 를 살펴보세요.
