---
title: parse 연산자-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 구문 분석 연산자에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 02/13/2020
ms.openlocfilehash: 8255f3d0c3dc0006029f06c7a0da4b41dfbaa1b7
ms.sourcegitcommit: 733bde4c6bc422c64752af338b29cd55a5af1f88
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 05/13/2020
ms.locfileid: "83271340"
---
# <a name="parse-operator"></a>parse 연산자

문자열 식을 평가하고 해당 값을 계산 열 하나 이상으로 구문 분석합니다. 구문 분석에 실패 한 문자열의 경우 계산 열에 null이 포함 됩니다.
구문 분석 되지 않은 문자열을 필터링 하는 [구문 분석-where](parsewhereoperator.md) 연산자를 참조 하세요.

```kusto
T | parse Text with "ActivityName=" name ", ActivityType=" type
```

**구문**

*T* `| parse` [ `kind=regex` [ `flags=regex_flags` ] | `simple` | `relaxed` ] *식* `with` `*` (*stringconstant* *ColumnName* [ `:` *ColumnType*]) `*` ...

**인수**

* *T*: 입력 테이블입니다.
* 종류로 

    * simple (기본값): StringConstant는 일반 문자열 값 이며, 일치는 모든 문자열 구분 기호가 구문 분석 된 문자열에 표시 되어야 하 고 모든 확장 열이 필요한 형식과 일치 해야 함을 의미 하는 strict입니다.
        
    * regex: StringConstant는 정규식 일 수 있으며, 일치 항목은 모든 문자열 구분 기호 (이 모드의 regex 일 수 있음)를 구문 분석 된 문자열에 표시 하 고 모든 확장 열이 필요한 형식과 일치 해야 함을 의미 합니다.
    
    * flags: `U` RE2 플래그에서 (ungreedy), ( `m` 여러 줄 모드), `s` (새 줄 일치 `\n` ), ( `i` 대/소문자 구분 안 [RE2 flags](re2.md)함) 등의 regex 모드에서 사용할 플래그입니다.
        
    * 완화 됨: StringConstant는 일반 문자열 값 이며 일치는 완화 됩니다. 즉, 모든 문자열 구분 기호가 구문 분석 된 문자열에 표시 되어야 하지만 확장 된 열이 필요한 형식과 부분적으로 일치 하는 것을 의미 합니다. (필수 형식과 일치 하지 않는 확장 열은 null 값을 받습니다.)

* *Expression*: 문자열로 계산 되는 식입니다.

* *ColumnName:* 문자열 식에서 가져온 값을에 할당할 열의 이름입니다. 
  
* *ColumnType:* 값을 변환할 형식을 나타내는 선택적 스칼라 형식 이어야 합니다 (기본적으로 문자열 형식).

**반환**

입력 테이블은 연산자에 제공 되는 열 목록에 따라 확장 됩니다.

**팁**

* [`project`](projectoperator.md)일부 열을 삭제 하거나 이름을 변경 하려는 경우에 사용 합니다.

* 정크 값을 건너뛰려면 패턴에서 *를 사용 합니다 (문자열 열 다음에는 사용할 수 없음).

* Parse 패턴은 *Stringconstant*를 사용 하는 것이 아니라 *ColumnName* 으로 시작 될 수 있습니다. 

* 구문 분석 된 *식이* 문자열 형식이 아닌 경우 문자열 형식으로 변환 됩니다.

* Regex 모드를 사용 하는 경우 구문 분석에 사용 되는 regex 전체를 제어 하기 위해 regex 플래그를 추가 하는 옵션이 있습니다.

* Regex 모드에서 구문 분석은 패턴을 regex로 변환 하 고, 내부적으로 처리 되는 번호가 매겨진 캡처된 그룹을 사용 하 여 일치를 수행 하기 위해 [RE2 구문을](re2.md) 사용 합니다.
  예를 들어,이 구문 분석 문은 다음과 같습니다.
  
    ```kusto
    parse kind=regex Col with * <regex1> var1:string <regex2> var2:long
    ```

    내부적으로 구문 분석에서 생성 되는 regex는 `.*?<regex1>(.*?)<regex2>(\-\d+)` 입니다.
        
    - `*`가로 변환 되었습니다 `.*?` .
        
    - `string`가로 변환 되었습니다 `.*?` .
        
    - `long`가로 변환 되었습니다 `\-\d+` .

**예**

`parse`연산자는 `extend` 동일한 식에서 여러 응용 프로그램을 사용 하 여 테이블에 간소화 된 방법을 제공 `extract` `string` 합니다.
이 방법은 테이블에 `string` 개발자 추적 (" `printf` "/"") 문에 의해 생성 된 열과 같이 개별 열로 나눌 여러 값이 포함 된 열이 있는 경우에 가장 유용 `Console.WriteLine` 합니다.

아래 예에서는 테이블의 열에 형식의 `EventText` `Traces` 문자열이 포함 되어 있다고 가정 합니다 `Event: NotifySliceRelease (resourceName={0}, totalSlices= {1}, sliceNumber={2}, lockTime={3}, releaseTime={4}, previousLockTime={5})` .
아래 작업을 수행 하면 테이블에,,,, `resourceName` , `totalSlices` `sliceNumber` `lockTime ` `releaseTime` `previouLockTime` `Month` 및 `Day` 열이 6 개 있는 것으로 확장 됩니다. 

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|20|02/17/2016 08:40:01|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler|27|16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|

regex 모드의 경우:

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces  
| parse kind = regex EventText with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previouLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previouLockTime
```

|resourceName|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|
|PipelineScheduler|15|02/17/2016 08:40:00, |02/17/2016 08:40:00, |2016-02-17 08:39:00.0000000|
|PipelineScheduler|23|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|20|02/17/2016 08:40:01, |02/17/2016 08:40:01, |2016-02-17 08:39:01.0000000|
|PipelineScheduler|16|02/17/2016 08:41:00, |02/17/2016 08:41:00, |2016-02-17 08:40:00.0000000|
|PipelineScheduler|22|02/17/2016 08:41:01, |02/17/2016 08:41:00, |2016-02-17 08:40:01.0000000|


regex 플래그를 사용 하는 regex 모드의 경우:

Context.resourcename을 가져오는 데 관심이 있는 경우 다음 쿼리를 사용 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex  EventText with * "resourceName=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler, totalSlices = 27, sliceNumber = 23, lockTime = 02/17/2016 08:40:01, releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler, totalSlices = 27, sliceNumber = 15, lockTime = 02/17/2016 08:40:00, releaseTime = 02/17/2016 08:40:00|
|PipelineScheduler, totalSlices = 27, sliceNumber = 20, lockTime = 02/17/2016 08:40:01, releaseTime = 02/17/2016 08:40:01|
|PipelineScheduler, totalSlices = 27, sliceNumber = 22, lockTime = 02/17/2016 08:41:01, releaseTime = 02/17/2016 08:41:00|
|PipelineScheduler, totalSlices = 27, sliceNumber = 16, lockTime = 02/17/2016 08:41:00, releaseTime = 02/17/2016 08:41:00|

그러나 기본 모드가 greedy 이기 때문에 예상 되는 결과를 얻을 수 없습니다.
또는 경우에 따라 Context.resourcename이 표시 되는 경우에도 일부 값에 대 한 null을 얻을 수 있는 경우도 있습니다.
원하는 결과를 얻기 위해 non-greedy ( `U` ) 및 대/소문자 구분 ( `i` ) regex 플래그를 사용 하 여이 작업을 실행할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=02/17/2016 08:40:00, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=02/17/2016 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind = regex flags = Ui EventText with * "RESOURCENAME=" resourceName ',' *
| project resourceName
```

|resourceName|
|---|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|
|PipelineScheduler|


구문 분석 된 문자열에 줄바꿈이 있으면 플래그를 사용 `s` 하 여 텍스트를 예상 대로 구문 분석 해야 합니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=23\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=15\nlockTime=02/17/2016 08:40:00\nreleaseTime=02/17/2016 08:40:00\npreviousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=20\nlockTime=02/17/2016 08:40:01\nreleaseTime=02/17/2016 08:40:01\npreviousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=22\nlockTime=02/17/2016 08:41:01\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler\ntotalSlices=27\nsliceNumber=16\nlockTime=02/17/2016 08:41:00\nreleaseTime=02/17/2016 08:41:00\npreviousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=regex flags=s EventText with * "resourceName=" resourceName:string "(.*?)totalSlices=" totalSlices:long "(.*?)lockTime=" lockTime:datetime "(.*?)releaseTime=" releaseTime:datetime "(.*?)previousLockTime=" previousLockTime:datetime "\\)" 
| project-away EventText
```

|resourceName|totalSlices|lockTime|releaseTime|previousLockTime|
|---|---|---|---|---|
|PipelineScheduler<br>|27|2016-02-17 08:40:00.0000000|2016-02-17 08:40:00.0000000|2016-02-17 08:39:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:40:01.0000000|2016-02-17 08:40:01.0000000|2016-02-17 08:39:01.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:00.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler<br>|27|2016-02-17 08:41:01.0000000|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|


완화 된 모드에 대 한이 예제에서는 totalSlices 확장 열이 long 형식 이어야 하지만 구문 분석 된 문자열에 nonValidLongValue 값이 있습니다.
releaseTime 확장 열에 동일한 문제가 있습니다. 비 유효 날짜/시간 값은 datetime으로 구문 분석할 수 없습니다.
이 경우 두 개의 확장 된 열은 null 값을 얻게 되 고 다른 값 (예: sliceNumber)은 여전히 올바른 값을 가져옵니다.

아래의 동일한 쿼리에 kind = simple을 사용 하면 확장 된 열에 대 한 strict 이므로 모든 확장 열에 대해 null이 제공 됩니다 .이는 완화 된 모드와 단순 모드의 차이 이며 확장 된 열은 부분적으로 일치할 수 있습니다.

<!-- csl: https://help.kusto.windows.net/Samples -->
```kusto
let Traces = datatable(EventText:string)
[
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00, releaseTime=nonValidDateTime, previousLockTime=02/17/2016 08:39:00)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=20, lockTime=02/17/2016 08:40:01, releaseTime=nonValidDateTime 08:40:01, previousLockTime=02/17/2016 08:39:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:01)",
"Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=nonValidLongValue, sliceNumber=16, lockTime=02/17/2016 08:41:00, releaseTime=02/17/2016 08:41:00, previousLockTime=02/17/2016 08:40:00)"
];
Traces
| parse kind=relaxed EventText with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *
| project-away EventText
```

|resourceName|totalSlices|sliceNumber|lockTime|releaseTime|previouLockTime|
|---|---|---|---|---|---|
|PipelineScheduler|27|15|02/17/2016 08:40:00||2016-02-17 08:39:00.0000000|
|PipelineScheduler|27|23|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||20|02/17/2016 08:40:01||2016-02-17 08:39:01.0000000|
|PipelineScheduler||16|02/17/2016 08:41:00|2016-02-17 08:41:00.0000000|2016-02-17 08:40:00.0000000|
|PipelineScheduler|27|22|02/17/2016 08:41:01|2016-02-17 08:41:00.0000000|2016-02-17 08:40:01.0000000|
