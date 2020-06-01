---
title: Kusto CLI-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 Kusto CLI에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/24/2020
ms.openlocfilehash: ab82698054e4bcb851f9f05acdd62af569e0704b
ms.sourcegitcommit: 9fe6e34ef3321390ee4e366819ebc9b132b3e03f
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 06/01/2020
ms.locfileid: "84258116"
---
# <a name="kusto-cli"></a>Kusto CLI

Kusto. Cli는 Kusto에 요청을 보내고 결과를 표시 하는 데 사용할 수 있는 명령줄 유틸리티입니다. Kusto. Cli는 다음과 같은 몇 가지 모드 중 하나에서 실행할 수 있습니다.

* *REPL 모드*: Kusto에 대해 실행 하는 쿼리 및 명령의 사용자 형식입니다. 그러면 도구에서 결과를 표시 한 후 다음 사용자 쿼리/명령을 기다립니다 합니다.
  "REPL"은 "읽기/평가/인쇄/루프"를 나타냅니다.

* *실행 모드*: 사용자는 도구에 명령줄 인수로 실행할 하나 이상의 쿼리와 명령을 제공 합니다. 이러한 설정은 자동으로 순서 대로 실행 되며 그 결과는 콘솔에 출력 됩니다. 모든 입력 쿼리와 명령을 실행 하는 경우 도구는 REPL 모드로 전환 됩니다.

* *스크립트 모드*: 실행 모드와 비슷하지만 파일 ("스크립트" 라고 함)을 통해 지정 된 쿼리 및 명령을 사용 합니다.

Kusto. Cli는 주로 코드를 작성 하는 데 필요한 Kusto 서비스에 대 한 작업을 자동화 하기 위해 제공 됩니다 (예: c # 프로그램 또는 PowerShell 스크립트).

## <a name="getting-the-tool"></a>도구 가져오기

Kusto. Cli는 NuGet 패키지의 일부 이며 `Microsoft.Azure.Kusto.Tools` [여기](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Tools/)에서 다운로드할 수 있습니다.
다운로드 한 후에는 패키지의 `tools` 폴더를 대상 폴더로 추출할 수 있습니다. 추가 설치는 필요 하지 않습니다 (즉, xcopy로 설치 가능 함).



## <a name="running-the-tool"></a>도구 실행

Kusto Cli를 실행 하려면 명령줄 인수가 하나 이상 필요 합니다. 일반적으로 해당 인수는 도구에서 연결 해야 하는 Kusto 서비스에 대 한 연결 문자열입니다. [Kusto 연결 문자열](../api/connection-strings/kusto.md)을 참조 하세요. 명령줄 인수를 사용 하지 않거나 알 수 없는 인수 집합을 사용 하거나 스위치를 사용 하 여 도구를 실행 `/help` 하면 도움말 메시지를 콘솔로 내보냅니다.

예를 들어 다음 명령을 사용 하 여 Kusto Cli를 실행 하 고 `help` kusto 서비스에 연결 하 고 데이터베이스 컨텍스트를 데이터베이스에 설정 합니다. `Samples`

```
Kusto.Cli.exe "https://help.kusto.windows.net/Samples;Fed=true"
```

> [!NOTE]
> PowerShell과 같은 셸 응용 프로그램에서 `;` 연결 문자열의 세미콜론 () 및 유사한 문자를 해석 하지 못하도록 연결 문자열 주위에 따옴표를 사용 했습니다.

## <a name="command-line-arguments"></a>명령줄 인수

`Kusto.Cli.exe`*ConnectionString* [*스위치*]

*ConnectionString*
* 모든 Kusto 연결 정보를 포함 하는 [Kusto 연결 문자열](../api/connection-strings/kusto.md) 입니다.
  기본값은 `net.tcp://localhost/NetDefaultDB`입니다.

`-execute:`*QueryOrCommand*
* 지정 된 경우 실행 모드에서 Kusto. Cli를 실행 합니다. 지정 된 쿼리 또는 명령이 실행 됩니다. 이 스위치는 반복 될 수 있으며,이 경우 쿼리/명령이 모양 순서 대로 순차적으로 실행 됩니다.
  이 스위치는 또는와 함께 사용할 수 없습니다 `-script` `-scriptml` .

`-keepRunning:`*EnableKeepRunning*
* 또는로 지정 된 경우 `true` `false` , 또는 값이 모두 처리 된 후에는 REPL 모드로의 전환을 사용 하거나 사용 하지 않도록 설정 `-script` `-execute` 합니다.

`-script:`*ScriptFile*
* 지정 하는 경우 스크립트 모드에서 Kusto. Cli를 실행 합니다. 지정 된 스크립트 파일이 로드 되 고 쿼리 또는 명령이 순차적으로 실행 됩니다.
  줄바꿈는 아래에 설명 된 대로 줄이 또는 조합으로 끝나는 경우를 제외 하 고 쿼리/명령을 구분 하는 데 사용 됩니다 `&` `&&` .
  이 스위치는와 함께 사용할 수 없습니다 `-execute` .

`-scriptml:`*ScriptFile*
* 지정 하는 경우 스크립트 모드에서 Kusto. Cli를 실행 합니다. 지정 된 스크립트 파일이 로드 되 고 쿼리 또는 명령이 순차적으로 실행 됩니다.
  전체 스크립트 파일은 단일 쿼리 또는 명령으로 간주 됩니다.
  이 스위치는와 함께 사용할 수 없습니다 `-execute` .

`-echo:`*EnableEchoMode*
* 또는로 지정 하면 `true` `false` 에코 모드를 사용 하거나 사용 하지 않도록 설정 합니다.
  Echo 모드를 사용 하도록 설정 하면 모든 쿼리 또는 명령이 출력에 반복 됩니다.

`-transcript:`*TranscriptFile*  
* 지정 하면 프로그램 출력을 *TranscriptFile*에 씁니다.

`-logToConsole:`*EnableLogToConsole*
* 지정 된 경우 ( `true` 또는 `false` )로 설정 하면 프로그램 출력을 콘솔에 쓰는 기능이 사용 되거나 사용 되지 않습니다.

`-lineMode:`*EnableLineMode*
* 지정 된 경우 줄 입력 모드 (기본값 `true` )와 블록 입력 모드 (로 설정 된 경우) 사이를 전환 `false` 합니다. 줄바꿈를 처리 하는 방법을 결정 하는 이러한 두 모드에 대 한 설명은 아래를 참조 하십시오.

**예제**

```
Kusto.Cli.exe "https://kustolab.kusto.windows.net/;Fed=true" -script:"c:\mycommands.txt"
```

콜론과 인수 값 사이에 공백이 없어야 합니다.

## <a name="directives"></a>지시문

Kusto. Cli는 처리를 위해 서비스로 전송 되는 것이 아니라 도구에서 실행 되는 다양 한 지시문을 지원 합니다.

|지시문                      |설명|
|-------------------------------|-----------|
|`?`<br>`#h`<br>`#help`         |짧은 도움말 메시지를 가져옵니다.|
|`q`<br>`#quit`<br>`#exit`      |도구를 종료 합니다.|
|`#a`<br>`#abort`               |Abortively 도구를 종료 합니다.|
|`#clip`                        |다음 쿼리 또는 명령의 결과가 클립보드에 복사 됩니다.|
|`#cls`                         |콘솔 화면을 지웁니다.|
|`#connect`*[ConnectionString*]|다른 Kusto 서비스에 연결 합니다. *ConnectionString* 이 생략 되 면 현재 항목을 표시 합니다.|
|`#crp`[*이름* [ `=` *값*]]   |클라이언트 요청 속성의 값을 설정 하거나, 표시 하거나, 모든 값을 표시 합니다.|
|`#crp`( `-list` \| `-doc` ) [*Prefix*]|클라이언트 요청 속성 (접두사 또는 모두)을 나열 합니다.|
|`#dbcontext`[*DatabaseName*]  |쿼리 및 명령에 사용 되는 "context" 데이터베이스를 *DatabaseName* 으로 변경 합니다 (생략 하면 현재 컨텍스트가 표시 됨).|
|`ke` *텍스트*                    |실행 중인 Kusto 탐색기 프로세스에 지정 된 텍스트를 보냅니다.|
|`#loop`*개수* *텍스트*         |텍스트를 여러 번 실행 합니다.|
|`#qp`[*이름* [ `=` *값*]]   |쿼리 매개 변수의 값을 설정 (또는 표시만 하거나 모든 값을 표시) 합니다. 시작/끝에서 작은따옴표 (1/2)가 잘립니다.|
|`#save`*파일 이름*             |다음 쿼리 또는 명령의 결과는 지정 된 CSV 파일에 저장 됩니다.|
|`#script`*파일 이름*           |표시 된 스크립트를 실행 합니다.|
|`#scriptml`*파일 이름*         |표시 된 여러 줄 스크립트를 실행 합니다.|

## <a name="line-input-mode-and-block-input-mode"></a>줄 입력 모드 및 블록 입력 모드

기본적으로 Kusto Cli는 **줄 입력 모드**에서 실행 되 고 있습니다. 각 줄 바꿈 문자는 쿼리/명령 사이에 구분 기호로 해석 되 고 줄은 실행을 위해 즉시 전송 됩니다.

이 모드에서는 긴 쿼리나 명령을 여러 줄로 나눌 수 있습니다. `&`줄의 마지막 문자 (줄 바꿈 앞)로 문자 모양 (줄 바꿈 전)은 Kusto Cli를 통해 다음 줄을 계속 읽습니다. `&&`줄의 마지막 문자 (줄 바꿈 앞)로 문자 모양 (줄 바꿈 전)은 Kusto. Cli에서 줄 바꿈 문자를 무시 하 고 다음 줄을 계속 읽습니다.

또는 Kusto. Cli는 **블록 입력 모드**에서의 실행도 지원 합니다. 명령줄 스위치를 사용 `-lineMode:false` 하거나 명령을 사용 하 여 `#blockmode` 모든 줄이 이전 줄의 연속으로 가정 하 여 쿼리 및 명령이 빈 입력 줄로만 구분 되도록 하는 cli를 지시할 수 있습니다.

## <a name="comments"></a>주석

Kusto. Cli는 `//` 새 줄을 주석 줄로 시작 하는 문자열을 해석 합니다. 나머지 줄을 무시 하 고 다음 줄을 계속 읽습니다.

## <a name="tool-only-options"></a>도구 전용 옵션

명령                        | 영향                                                                            | 중인
--------------------------------|-----------------------------------------------------------------------------------|-----------
#timeon|#timeoff                | ' 타이밍 ' 옵션 사용/사용 안 함: 요청 된 시간을 표시 합니다.                    | TRUE
#tableon|#tableoff              | ' tableView ' 옵션 사용/사용 안 함: 결과 집합을 테이블로 서식 지정                  | TRUE
#marson|#marsoff                | ' marsView ' 옵션 사용/사용 안 함: 두 번째-마지막 결과 집합을 표시 합니다.             | FALSE
#resultson|#resultsoff          | ' outputResultsSet ' 옵션 사용/사용 안 함: 결과 집합을 표시 합니다.                 | TRUE
#prettyon|#prettyoff            | ' prettyErrors ' 옵션 사용/사용 안 함: 오류에서 불필요 한 goo 제거          | TRUE
#markdownon|#markdownoff        | ' markdownView ' 옵션 사용/사용 안 함: MarkDown로 테이블 서식 지정                   | FALSE
#progressiveon|#progressiveoff  | ' progressiveView ' 옵션 사용/사용 안 함: 프로그레시브 결과를 요청 하 고 표시 합니다.  | FALSE
#linemode|#blockmode            | ' lineMode ' 옵션 사용/사용 안 함: 한 줄 입력 모드                          | TRUE

명령                              | 영향                                                                                                         | 기본
--------------------------------------|----------------------------------------------------------------------------------------------------------------|-----------
#cridon|#cridoff                      | (사용|' crid ' 옵션 사용 안 함: 요청을 보내기 전에 ClientRequestId를 표시 합니다.                         | FALSE
#csvheaderson|#csvheadersoff          | (사용|' csvHeaders ' 옵션 사용 안 함: CSV 출력에 헤더 포함)                                            | TRUE
#focuson|#focusoff                    | (사용|' focus ' 옵션 사용 안 함: 모든 추가 유익을 제거 하 고 올바른 기능에 집중 합니다.                       | FALSE
#linemode|#blockmode                  | (사용|' lineMode ' 옵션 사용 안 함: 한 줄 입력 모드)                                                     | TRUE
#markdownon|#markdownoff              | (사용|' markdownView ' 옵션 사용 안 함: 테이블 형식을 MarkDown로 지정 합니다.                                              | FALSE
#marson|#marsoff                      | (사용|' marsView ' 옵션 사용 안 함: 두 번째-마지막 결과 집합을 표시 합니다.                                        | FALSE
#prettyon|#prettyoff                  | (사용|' prettyErrors ' 옵션 사용 안 함: 오류에서 불필요 한 goo 제거                                     | TRUE
#querystreamingon|#querystreamingoff  | (사용|' queryStreaming ' 옵션 사용 안 함: queryStreaming 끝점을 사용 합니다 (Kusto 팀에만 해당)).                    | FALSE
#resultson|#resultsoff                | (사용|' outputResultsSet ' 옵션을 사용 하지 않도록 설정: 결과 집합을 표시 합니다.                                            | TRUE
#tableon|#tableoff                    | (사용|' tableView ' 옵션 사용 안 함: 결과 집합을 테이블로 서식 지정                                             | TRUE
#timeon|#timeoff                      | (사용|' 타이밍 ' 옵션 사용 안 함: 요청이 걸린 시간을 표시 합니다.                                               | TRUE
#typeon|#typeoff                      | (사용|' typeView ' 옵션을 사용 하지 않도록 설정: 테이블 뷰에 각 열의 유형을 표시 합니다 (강제 스트리밍 = true 임).  | TRUE
#v2protocolon|#v2protocoloff          | (사용|' v2protocol ' 옵션을 사용 하지 않도록 설정 합니다. v1이 아닌 v2 쿼리 프로토콜을 사용 합니다.                                        | TRUE

## <a name="using-kustocli-to-export-results-as-csv"></a>Kusto. Cli를 사용 하 여 결과를 CSV로 내보내기

Kusto. Cli는 `#save` **다음** 쿼리 결과를 CSV 형식의 로컬 파일로 내보내는 특수 한 클라이언트 쪽 명령를 지원 합니다. 예를 들어 다음은 Kusto. Cli를 호출 하 여 `StormEvents` `help.kusto.windows.net` 클러스터 (데이터베이스)의 테이블에서 10 개의 레코드를 내보내는 것입니다. `Samples`

```
Kusto.Cli.exe @help/Samples -execute:"#save c:\temp\test.log" -execute:"StormEvents | take 10"
```

## <a name="using-kustocli-to-control-a-running-instance-of-kustoexplorer"></a>Kusto Cli를 사용 하 여 실행 중인 Kusto 탐색기 인스턴스 제어

Kusto. Cli를 사용 하 여 컴퓨터에서 실행 중인 "기본" 인스턴스와 통신 하 고 실행 하는 데 사용할 수 있습니다. 이는 여러 Kusto 쿼리를 실행 하려는 프로그램에 매우 유용할 수 있지만, Kusto 탐색기 프로세스를 다시 시작 하지 않으려고 합니다. 다음 예제에서 Kusto. Cli는 도움말 클러스터 기입 쿼리를 실행 하는 데 사용 됩니다.

```
#connect cluster('help').database('Samples')

#ke StormEvents | count
```

구문은 매우 간단 `#ke` 하며, 그 뒤에 공백 및 실행할 쿼리가 있습니다.
그런 다음이 쿼리는 Kusto. s e r v e r. c o m에 설정 된 현재 클러스터/데이터베이스를 사용 하 여 .Iito 탐색기 (있는 경우)의 주 인스턴스로 전송 됩니다.
