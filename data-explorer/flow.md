---
title: 파워 자동화를 위한 Azure 데이터 탐색기 커넥터 (미리 보기)
description: Azure 데이터 탐색기 커넥터를 사용 하 여 자동으로 예약 되거나 트리거된 작업의 흐름을 만드는 방법에 대해 알아봅니다.
author: orspod
ms.author: orspodek
ms.reviewer: dorcohen
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: 57f3ae587f449739f7e3aaaa0aa817530447097e
ms.sourcegitcommit: 98eabf249b3f2cc7423dade0f386417fb8e36ce7
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/06/2020
ms.locfileid: "82868730"
---
# <a name="azure-data-explorer-connector-to-power-automate-preview"></a>파워 자동화를 위한 Azure 데이터 탐색기 커넥터 (미리 보기)

Azure 데이터 탐색기 flow 커넥터를 사용 하면 Azure 데이터 탐색기에서 [Microsoft 파워 자동화](https://flow.microsoft.com/)의 흐름 기능을 사용할 수 있습니다. 예약 되거나 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행할 수 있습니다.

다음과 같습니다.

* 테이블 및 차트를 포함 하는 일별 보고서를 보냅니다.
* 쿼리 결과를 기반으로 알림을 설정 합니다.
* 클러스터에 대 한 제어 명령을 예약 합니다.
* Azure 데이터 탐색기와 다른 데이터베이스 간에 데이터를 내보내고 가져옵니다. 

자세한 내용은 [Azure 데이터 탐색기 flow 커넥터 사용 예제](flow-usage.md)를 참조 하세요.

##  <a name="sign-in"></a>로그인 

1. 처음 연결 하는 경우 로그인 하 라는 메시지가 표시 됩니다.

1. **로그인**을 선택 하 고 자격 증명을 입력 합니다.

![Azure 데이터 탐색기 로그인 프롬프트 스크린샷](./media/flow/flow-signin.png)

## <a name="authentication"></a>인증

사용자 자격 증명을 사용 하거나 Azure Active Directory (Azure AD) 응용 프로그램을 사용 하 여 인증할 수 있습니다.

> [!Note]
> 응용 프로그램이 [AZURE AD 응용 프로그램](kusto/management/access-control/how-to-provision-aad-app.md)이며 클러스터에서 쿼리를 실행할 수 있는 권한이 있는지 확인 합니다.

1. **제어 실행 명령 및 결과 시각화**에서 흐름 커넥터의 오른쪽 위에 있는 세 개의 점을 선택 합니다.

   ![컨트롤 실행 명령 및 결과 시각화의 스크린샷](./media/flow/flow-addconnection.png)

1. **서비스 사용자와** **새 연결** > 연결 추가를 선택 합니다.

   ![서비스 사용자로 연결 옵션을 사용 하 여 Azure 데이터 탐색기 로그인 프롬프트의 스크린샷](./media/flow/flow-signin.png)

1. 필요한 정보를 입력합니다.
   - 연결 이름: 새 연결에 대 한 설명 및 의미 있는 이름입니다.
   - 클라이언트 ID: 응용 프로그램 ID입니다.
   - 클라이언트 암호: 응용 프로그램 키입니다.
   - 테 넌 트: 응용 프로그램을 만든 Azure AD 디렉터리의 ID입니다.

   ![Azure 데이터 탐색기 응용 프로그램 인증 대화 상자 스크린샷](./media/flow/flow-appauth.png)

인증이 완료 되 면 흐름에서 새로 추가 된 연결을 사용 하는 것을 볼 수 있습니다.

![완료 된 응용 프로그램 인증 스크린샷](./media/flow/flow-appauthcomplete.png)

지금부터이 흐름은 이러한 응용 프로그램 자격 증명을 사용 하 여 실행 됩니다.

## <a name="find-the-azure-kusto-connector"></a>Azure Kusto 커넥터 찾기

Flow 커넥터를 사용 하려면 먼저 트리거를 추가 해야 합니다. 반복 된 기간에 따라 또는 이전 흐름 동작에 대 한 응답으로 트리거를 정의할 수 있습니다.

1. [새 흐름을 만들거나](https://flow.microsoft.com/manage/flows/new)Microsoft Power 자동화 홈 페이지에서 **내 흐름** > **+ 새로**만들기를 선택 합니다.

    ![내 흐름과 새로 강조 표시 된 Microsoft Power 자동화 홈 페이지의 스크린샷](./media/flow/flow-newflow.png)

1. **예약 됨--비어 있음을**선택 합니다.

    ![강조 표시 된 새 대화 상자의 스크린 샷](./media/flow/flow-scheduled-from-blank.png)

1. **예약 된 흐름 만들기**에서 필요한 정보를 입력 합니다.
    
    ![흐름 이름 옵션이 강조 표시 된 예약 된 흐름 페이지 작성의 스크린샷](./media/flow/flow-build-scheduled-flow.png)

1. **만들기** > **+ 새 단계**를 선택 합니다.
1. 검색 상자에 *Kusto*를 입력 하 고 **Azure 데이터 탐색기**를 선택 합니다.

    ![검색 상자 및 Azure 데이터 탐색기 강조 표시 된 작업 옵션 선택의 스크린샷](./media/flow/flow-actions.png)

## <a name="flow-actions"></a>흐름 작업

Azure 데이터 탐색기 커넥터를 열면 흐름에 추가할 수 있는 세 가지 작업을 수행할 수 있습니다. 이 섹션에서는 각 작업에 대 한 기능 및 매개 변수를 설명 합니다.

![Azure 데이터 탐색기 커넥터 작업 스크린샷](./media/flow/flow-adx-actions.png)

### <a name="run-control-command-and-visualize-results"></a>제어 명령 실행 및 결과 시각화

이 작업을 사용 하 여 [제어 명령을](kusto/management/index.md)실행 합니다.

1. 클러스터 URL을 지정 합니다. `https://clusterName.eastus.kusto.windows.net`)을 입력합니다.
1. 데이터베이스의 이름을 입력합니다.
1. 제어 명령을 지정 합니다.
   - 흐름에서 사용 되는 앱과 커넥터에서 동적 콘텐츠를 선택 합니다.
   - 식을 추가 하 여 값을 액세스, 변환 및 비교 합니다.
1. 이 작업의 결과를 전자 메일을 통해 테이블 또는 차트로 보내려면 차트 종류를 지정 합니다. 로컬 환경은 다음과 같은 시스템일 수 있습니다.
   - HTML 테이블입니다.
   - 원형 차트입니다.
   - 시간 차트입니다.
   - 가로 막대형 차트입니다.

![컨트롤 실행 명령 및 결과 시각화의 스크린샷](./media/flow/flow-runcontrolcommand.png)

> [!IMPORTANT]
> **클러스터 이름** 필드에 클러스터 URL을 입력 합니다.

### <a name="run-query-and-list-results"></a>쿼리 실행 및 결과 목록 표시

> [!Note]
> 쿼리가 점으로 시작 하는 경우 (즉, [제어 명령](kusto/management/index.md)) [컨트롤 실행 명령을 사용 하 고 결과를 시각화](#run-control-command-and-visualize-results)합니다.

이 작업은 Kusto 클러스터로 쿼리를 보냅니다. 이후에 추가 된 작업은 쿼리 결과의 각 줄을 반복 합니다.

다음 예에서는 1 분 마다 쿼리를 트리거하고 쿼리 결과를 기반으로 전자 메일을 보냅니다. 이 쿼리는 데이터베이스의 줄 수를 확인 한 다음 줄 수가 0 보다 큰 경우에만 전자 메일을 보냅니다. 

![쿼리 실행 및 목록 결과의 스크린샷](./media/flow/flow-runquerylistresults-2.png)

> [!Note]
> 열에 여러 줄이 있으면 해당 열의 각 줄에 대해 커넥터가 실행 됩니다.

### <a name="run-query-and-visualize-results"></a>쿼리 실행 및 결과 시각화
        
> [!Note]
> 쿼리가 점으로 시작 하는 경우 (즉, [제어 명령](kusto/management/index.md)) [컨트롤 실행 명령을 사용 하 고 결과를 시각화](#run-control-command-and-visualize-results)합니다.
        
이 작업을 사용 하 여 Kusto 쿼리 결과를 테이블 또는 차트로 시각화할 수 있습니다. 예를 들어이 흐름을 사용 하 여 일별 보고서를 전자 메일로 받을 수 있습니다. 
    
이 예에서는 쿼리 결과가 HTML 테이블로 반환 됩니다.
            
![쿼리 실행 및 결과 시각화 스크린샷](./media/flow/flow-runquery.png)

> [!IMPORTANT]
> **클러스터 이름** 필드에 클러스터 URL을 입력 합니다.

## <a name="email-kusto-query-results"></a>Kusto 쿼리 결과를 메일로 보내기

모든 흐름에 단계를 포함 하 여 전자 메일을 통해 보고서를 전자 메일 주소로 보낼 수 있습니다. 

1. **+ 새 단계** 를 선택 하 여 흐름에 새 단계를 추가 합니다.
1. 검색 상자에 *office 365* 를 입력 하 고 **office 365 Outlook**을 선택 합니다.
1. **전자 메일 보내기 (V2)** 를 선택 합니다.
1. 전자 메일 보고서를 보낼 전자 메일 주소를 입력 합니다.
1. 전자 메일의 제목을 입력 합니다.
1. **코드 보기**를 선택 합니다.
1. **본문** 필드에 커서를 놓고 **동적 콘텐츠 추가**를 선택 합니다.
1. **BodyHtml**를 선택 합니다.
    ![본문 필드 및 BodyHtml 강조 표시 된 전자 메일 보내기 대화 상자 스크린샷](./media/flow/flow-send-email.png)
1. **고급 옵션 표시**를 선택합니다.
1. **첨부 파일 이름-1**에서 **첨부 파일 이름**을 선택 합니다.
1. **첨부 파일 콘텐츠**에서 **첨부 파일 콘텐츠**를 선택 합니다.
1. 필요한 경우 첨부 파일을 더 추가 합니다. 
1. 필요한 경우 중요도 수준을 설정 합니다.
1. **저장**을 선택합니다.

![첨부 파일 이름, 첨부 파일 콘텐츠 및 강조 표시 된 전자 메일 보내기 대화 상자 스크린샷](./media/flow/flow-add-attachments.png)

## <a name="check-if-your-flow-succeeded"></a>흐름에 성공 했는지 확인 합니다.

흐름이 성공 했는지 확인 하려면 흐름의 실행 기록을 확인 합니다.
1. [Microsoft Power 자동화 홈 페이지로](https://flow.microsoft.com/)이동 합니다.
1. 주 메뉴에서 [내 흐름](https://flow.microsoft.com/manage/flows)을 선택 합니다.
   
   ![내 흐름이 강조 표시 된 Microsoft Power 자동화 주 메뉴의 스크린샷](./media/flow/flow-myflows.png)

1. 조사할 흐름의 행에서 추가 명령 아이콘을 선택 하 고 **실행 기록**을 선택 합니다.

    ![실행 기록이 강조 표시 된 내 흐름 탭의 스크린샷](./media/flow//flow-runhistory.png)

    시작 시간, 기간 및 상태에 대 한 정보가 포함 된 모든 흐름 실행이 나열 됩니다.
    ![실행 기록 결과 페이지의 스크린샷](./media/flow/flow-runhistoryresults.png)

    흐름에 대 한 자세한 내용을 보려면 흐름 **[에서 조사 하려는 흐름을 선택](https://flow.microsoft.com/manage/flows)** 합니다.

    ![실행 기록 전체 결과 페이지의 스크린샷](./media/flow/flow-fulldetails.png) 

실행이 실패 한 이유를 확인 하려면 실행 시작 시간을 선택 합니다. 흐름이 나타나고 실패 한 흐름의 단계는 빨간색 느낌표가 표시 됩니다. 실패 한 단계를 확장 하 여 세부 정보를 확인 합니다. 오른쪽의 **세부 정보** 창에는 문제를 해결할 수 있도록 오류에 대 한 정보가 포함 되어 있습니다.

![흐름 오류 페이지의 스크린샷](./media/flow/flow-error.png)

## <a name="timeout-exceptions"></a>시간 제한 예외

7 분 이상 실행 되는 경우 흐름은 실패 하 고 "RequestTimeout" 예외가 반환 됩니다.
    
![흐름 요청 시간 제한 예외 오류의 스크린샷](./media/flow/flow-requesttimeout.png)

시간 제한 문제를 해결 하려면 쿼리를 더 효율적으로 실행 하 여 더 빠르게 실행 하거나 청크로 분리 하는 것이 좋습니다. 각 청크는 쿼리의 다른 부분에서 실행할 수 있습니다. 자세한 내용은 [쿼리 모범 사례](kusto/query/best-practices.md)를 참조 하세요.

Azure 데이터 탐색기에서 동일한 쿼리가 성공적으로 실행 될 수 있으며,이는 시간이 제한 되지 않으며 변경 될 수 있습니다.

## <a name="limitations"></a>제한 사항

* 클라이언트에 반환 되는 결과는 50만 레코드로 제한 됩니다. 이러한 레코드에 대 한 전체 메모리는 64 MB를 초과할 수 없으며 실행 하는 데 7 분이 소요 됩니다.
* 커넥터는 [포크](kusto/query/forkoperator.md) 및 [패싯](kusto/query/facetoperator.md) 연산자를 지원 하지 않습니다.
* 흐름은 Microsoft Edge 및 Google Chrome에서 가장 잘 작동 합니다.

## <a name="next-steps"></a>다음 단계

예약 된 작업 또는 트리거된 작업의 일부로 Kusto 쿼리 및 명령을 자동으로 실행 하는 또 다른 방법인 [Azure Kusto 논리 앱 커넥터](kusto/tools/logicapps.md)에 대해 알아봅니다.
