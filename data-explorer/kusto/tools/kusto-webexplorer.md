---
title: Kusto. WebExplorer-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto. WebExplorer에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/24/2020
ms.openlocfilehash: 70b25a1dbfcc3d5cba134875a63c2ac24a018277
ms.sourcegitcommit: 7fa9d0eb3556c55475c95da1f96801e8a0aa6b0f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/11/2020
ms.locfileid: "91942287"
---
# <a name="kustowebexplorer"></a>Kusto. WebExplorer

Kusto. WebExplorer는 Kusto 서비스에 쿼리 및 제어 명령을 보내는 데 사용할 수 있는 웹 응용 프로그램입니다. 응용 프로그램은에서 호스트 되 https://dataexplorer.azure.com/ 고에 의해 short로 연결 됩니다 https://aka.ms/kwe .



Kusto. WebExplorer는 HTML IFRAME의 다른 웹 포털에서 호스팅될 수도 있습니다.
예를 들어이 작업은 [Azure Portal](https://portal.azure.com)에 의해 수행 됩니다. 호스트 하는 방법 및이에서 사용 하는 모나코 편집기에 대 한 자세한 내용은 [모나코 IDE](../api/monaco/monaco-kusto.md) 를 참조 하세요.

## <a name="connect-to-multiple-clusters"></a>여러 클러스터에 연결

이제 여러 클러스터를 연결 하 고 데이터베이스와 클러스터 간을 전환할 수 있습니다.
이 도구는 연결 된 클러스터와 데이터베이스를 쉽게 식별할 수 있도록 설계 되었습니다.

![애니메이션 GIF. Azure 데이터 탐색기에서 클러스터 추가를 클릭 하 고 대화 상자에 클러스터 이름을 입력 하면 왼쪽 창에 클러스터가 표시 됩니다.](./Images/KustoTools-WebExplorer/AddingCluster.gif "AddingCluster")

## <a name="recall-results"></a>회수 결과

분석 하는 동안 여러 쿼리를 실행 하 고 이전 쿼리의 결과를 다시 검토 해야 하는 경우가 많습니다. 이 기능을 사용 하 여 쿼리를 다시 실행 하지 않고도 결과를 회수할 수 있습니다. 데이터는 로컬 클라이언트 쪽 캐시에서 제공 됩니다.

![애니메이션 GIF. 두 개의 Azure 데이터 탐색기 쿼리가 실행 된 후 마우스는 첫 번째 쿼리로 이동 하 고 회수를 클릭 합니다. 초기 결과가 다시 표시 됩니다.](./Images/KustoTools-WebExplorer/RecallResults.gif "결과 반환")

## <a name="enhanced-results-grid-control"></a>향상 된 결과 표 형태 컨트롤

표 표를 사용 하 여 여러 행, 열 및 셀을 선택할 수 있습니다. Excel과 같은 여러 셀을 선택 하 고 데이터를 피벗 하 여 집계를 계산 합니다.

![애니메이션 GIF. Azure 데이터 탐색기에서 피벗 모드가 설정 되 고 열이 피벗 테이블 대상 영역으로 끌어오면 요약 된 데이터가 표시 됩니다.](./Images/KustoTools-WebExplorer/EnhancedGrid.gif "EnhancedGrid")

## <a name="intellisense--formatting"></a>Intellisense & 서식 지정

"Shift + Alt + F" 바로 가기 키, 코드 접기 (개요) 및 IntelliSense를 사용 하 여 예쁜 인쇄 형식을 사용할 수 있습니다.

![Azure 데이터 탐색기 쿼리를 보여 주는 애니메이션 GIF입니다. 쿼리가 확장 된 후에는 열 이름이 분홍색 인 한 줄에 표시 되는 형식을 변경 합니다.](./Images/KustoTools-WebExplorer/Formating.gif "서식")

## <a name="deep-linking"></a>딥 링크

딥 링크 또는 딥 링크와 쿼리를 복사할 수 있습니다. 다음 템플릿을 사용 하 여 URL의 형식을 지정 하 여 클러스터, 데이터베이스 및 쿼리를 포함할 수도 있습니다.

`https://dataexplorer.azure.com/` [ `clusters/` *Cluster* [ `/databases/` *Database* [ `?` *Options*]]]

다음 옵션을 지정할 수 있습니다.

* `workspace=empty`: 비어 있는 새 작업 영역을 만들도록 나타냅니다. 이전 클러스터, 탭 및 쿼리가 회수 되지 않습니다.



![애니메이션 GIF. Azure 데이터 탐색기 공유 메뉴가 열립니다. 클립보드 항목에 대 한 쿼리 링크는 텍스트와 클립보드 항목에 대 한 링크와 같이 표시 됩니다.](./Images/KustoTools-WebExplorer/DeepLink.gif "DeepLink")

## <a name="how-to-provide-feedback"></a>피드백을 제공하는 방법

도구를 통해 피드백을 제출할 수 있습니다.
![Azure 데이터 탐색기을 표시 하는 애니메이션 GIF입니다. 사용자 의견 아이콘을 클릭 하면 사용자 의견 보내기 대화 상자가 열립니다.](./Images/KustoTools-WebExplorer/Feedback.gif "사용자 의견")
