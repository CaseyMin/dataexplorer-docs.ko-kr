---
title: Azure 데이터 탐색기 도구 및 통합 개요-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 도구 및 통합에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: olgolden
ms.service: data-explorer
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 71f00cf921609432860a94061be7411e484fd0c9
ms.sourcegitcommit: 455d902bad0aae3e3d72269798c754f51442270e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/04/2020
ms.locfileid: "93349327"
---
# <a name="azure-data-explorer-tools-and-integrations-overview"></a>Azure 데이터 탐색기 도구 및 통합 개요

Azure 데이터 탐색기는 응용 프로그램, 웹 사이트, IoT 장치 등에서 다량의 데이터 스트리밍을 실시간으로 분석 하기 위한 완전히 관리 되는 데이터 분석 서비스입니다. Azure 데이터 탐색기는 다양 한 데이터를 수집, 저장 및 분석 하 여 제품을 개선 하 고, 고객 환경을 개선 하 고, 장치를 모니터링 하 고, 작업을 향상 시킵니다 

Azure 데이터 탐색기는 데이터 수집, 쿼리, 시각화, 오케스트레이션 등을 수행 하기 위한 다양 한 도구 및 통합을 제공 합니다. Azure 데이터 탐색기는 기본 서비스 외에도 다양 한 제품 및 플랫폼을 사용 하 여 쉽게 통합 하 고, 다양 한 고객 사용 사례를 지원 하 고, 워크플로를 간소화 하 고, 비용을 절감 하 여 비즈니스 프로세스를 최적화할 수 있습니다. 

이 문서에서는 추가 정보를 제공 하는 관련 문서에 대 한 링크와 함께 Azure 데이터 탐색기 도구, 커넥터 및 통합의 목록을 제공 합니다.

## <a name="ingest-data"></a>데이터 수집 

데이터 수집은 하나 이상의 소스에서 Azure 데이터 탐색기로 데이터 레코드를 로드 하는 데 사용 되는 프로세스입니다. 수집한 후에는 데이터를 쿼리에 사용할 수 있게 됩니다. Azure 데이터 탐색기는 데이터 수집을 위한 몇 가지 도구와 커넥터를 제공 합니다. 

### <a name="azure-data-explorer-ingestion-tools"></a>Azure 데이터 탐색기 수집 도구

* [LightIngest](lightingest.md) - Azure Data Explorer로의 임시 데이터 수집에 대한 도움말 유틸리티
* 한 번 클릭으로 수집: [개요](ingest-data-one-click.md) 및 [컨테이너에서 새 테이블](one-click-ingestion-new-table.md) 또는 [로컬 파일에서 기존 테이블로](one-click-ingestion-existing-table.md) 데이터 수집

### <a name="ingestion-integrations"></a>수집 통합

* 이벤트 허브: [이벤트 허브 개요에서 수집](ingest-data-event-hub-overview.md) 하 고 [Azure Portal](ingest-data-event-hub.md), [c #](data-connection-event-hub-csharp.md), [Python](data-connection-event-hub-python.md) 또는 [Azure Resource Manager 템플릿을](data-connection-event-hub-resource-manager.md) 사용 합니다.
* Event Grid: [Event Grid 개요에서 수집](ingest-data-event-grid-overview.md) 하 고 [Azure Portal](ingest-data-event-grid.md), [c #](data-connection-event-grid-csharp.md), [Python](data-connection-event-grid-python.md) 또는 [Azure Resource Manager 템플릿](data-connection-event-grid-resource-manager.md) 사용
* IoT Hub: [IoT Hub 개요에서 수집](ingest-data-iot-hub-overview.md) 하 고 [Azure Portal](ingest-data-iot-hub.md), [c #](data-connection-iot-hub-csharp.md), [Python](data-connection-iot-hub-python.md) 또는 [Azure Resource Manager 템플릿](data-connection-iot-hub-resource-manager.md) 사용
* [Logstash](ingest-data-logstash.md)
* Azure Data Factory: [통합 개요](data-factory-integration.md), [데이터 복사](data-factory-load-data.md), [Azure Data Factory 템플릿을 사용 하 여 대량 복사](data-factory-template.md)및 [Azure Data Factory 명령 작업을 사용 하 여 Azure 데이터 탐색기 제어 명령 실행](data-factory-command-activity.md)
* [Azure Synapse Apache Spark](/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Apache Spark](spark-connector.md)
* [Apache Kafka](ingest-data-kafka.md)
* [Cosmos DB](https://github.com/Azure/azure-kusto-labs/tree/master/cosmosdb-adx-integration)
* [Power Automate](flow.md)

## <a name="query-data"></a>쿼리 데이터

### <a name="azure-data-explorer-query-tools"></a>Azure 데이터 탐색기 쿼리 도구

Azure 데이터 탐색기에서 쿼리를 실행 하는 데 사용할 수 있는 몇 가지 도구가 있습니다.

* Kusto.Explorer
    * [설치 및 사용자 인터페이스](kusto/tools/kusto-explorer.md), [Kusto 탐색기 사용](kusto/tools/kusto-explorer-using.md)
    * 추가 항목으로는 [옵션](kusto/tools/kusto-explorer-options.md), [문제 해결](kusto/tools/kusto-explorer-troubleshooting.md), [바로 가기 키](kusto/tools/kusto-explorer-shortcuts.md), [코드 리팩터링](kusto/tools/kusto-explorer-refactor.md), [코드 탐색](kusto/tools/kusto-explorer-codenav.md), 코드 [분석](kusto/tools/kusto-explorer-code-analyzer.md) 등이 있습니다.
* [웹 UI](web-query-data.md)
* [Kusto.Cli](kusto/tools/kusto-cli.md)

### <a name="query-integrations"></a>통합 쿼리

* [Azure Monitor](query-monitor-data.md)
* [Azure Data Lake](data-lake-query-data.md)
* [Azure Synapse Apache Spark](/azure/synapse-analytics/quickstart-connect-azure-data-explorer?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Apache Spark](spark-connector.md)
* [Microsoft Power Apps](power-apps-connector.md)
* Azure Data Studio: [kusto 확장 개요](/sql/azure-data-studio/extensions/kusto-extension?context=%252fazure%252fdata-explorer%252fcontext%252fcontext), [Kusto 사용](/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)및 [Kqlmagic 사용](/sql/azure-data-studio/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)

## <a name="visualizations-dashboards-and-reporting"></a>시각화, 대시보드 및 보고

[시각화 개요](viz-overview.md) 는 데이터 시각화, 대시보드 및 보고 옵션에 대해 자세히 설명 합니다. 

## <a name="notebook-connectivity"></a>노트북 연결

* [Azure 노트](/sql/azure-data-studio/notebooks/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext%253fcontext%253d%252fazure%252fdata-explorer%252fcontext%252fcontext)
* [Jupyter Notebook](kqlmagic.md)
* Azure Data Studio: [kusto 확장 개요](/sql/azure-data-studio/extensions/kusto-extension?context=%252fazure%252fdata-explorer%252fcontext%252fcontext), [Kusto 사용](/sql/azure-data-studio/notebooks/notebooks-kusto-kernel?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)및 [Kqlmagic 사용](/sql/azure-data-studio/notebooks-kqlmagic?context=%252fazure%252fdata-explorer%252fcontext%252fcontext)

## <a name="orchestration"></a>오케스트레이션

* Power Automate
    * [Power Automate 커넥터](flow.md)
    * [Power Automate 커넥터 사용 예](flow-usage.md)
* [Microsoft 논리 앱](kusto/tools/logicapps.md) 
* [Azure Data Factory](data-factory-integration.md)

## <a name="share-data"></a>데이터 공유

* [Azure Data Share](data-share.md)

## <a name="source-control-integration"></a>소스 제어 통합

* [Azure Pipelines](devops.md) 
* [Kusto 동기화](kusto/tools/synckusto.md) 

<!--Open Source Tools-->