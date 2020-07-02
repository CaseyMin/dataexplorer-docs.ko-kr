---
title: R 플러그 인 (미리 보기)-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기의 R 플러그 인 (미리 보기)에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 04/01/2020
zone_pivot_group_filename: data-explorer/zone-pivot-groups.json
zone_pivot_groups: kql-flavors
ms.openlocfilehash: 429140a1ae74dc0d7189e4b8dec1a32428fe3bb3
ms.sourcegitcommit: 7dd20592bf0e08f8b05bd32dc9de8461d89cff14
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/02/2020
ms.locfileid: "85902144"
---
# <a name="r-plugin-preview"></a>R 플러그 인 (미리 보기)

::: zone pivot="azuredataexplorer"

R 플러그 인은 R 스크립트를 사용 하 여 UDF (사용자 정의 함수)를 실행 합니다. 이 스크립트는 테이블 형식 데이터를 입력으로 가져오고 테이블 형식 출력을 생성 합니다.
플러그 인의 런타임은 클러스터 노드의 [샌드박스에서](../concepts/sandboxes.md) 호스팅됩니다. 샌드박스는 격리 된 보안 환경을 제공 합니다.

## <a name="syntax"></a>구문

*T* `|` `evaluate` [ `hint.distribution` `=` ( `single`  |  `per_node` )] `r(` *output_schema* `,` *스크립트* [ `,` *script_parameters*]`)`

## <a name="arguments"></a>인수

* *output_schema*: `type` R 코드에서 반환 하는 테이블 형식 데이터의 출력 스키마를 정의 하는 리터럴입니다.
    * 형식은 다음과 같습니다. `typeof(` *ColumnName* `:` *ColumnType*[, ...]. 예를 `)` 들면와 같습니다 `typeof(col1:string, col2:long)` .
    * 입력 스키마를 확장 하려면 다음 구문을 사용 `typeof(*, col1:string, col2:long)` 합니다.
* *script*: `string` 실행할 유효한 R 스크립트에 해당 하는 리터럴입니다.
* *script_parameters*: `dynamic` R 스크립트에 예약 된 사전으로 전달 될 이름 및 값 쌍의 속성 모음 인 선택적 리터럴입니다 `kargs` . 자세한 내용은 [예약 된 R 변수](#reserved-r-variables)를 참조 하세요.
* *힌트. 배포*: 여러 클러스터 노드에 분산 될 플러그 인의 실행에 대 한 선택적 힌트입니다.
   기본값: `single`.
    * `single`: 스크립트의 단일 인스턴스는 전체 쿼리 데이터에 대해 실행 됩니다.
    * `per_node`: R 블록 이전의 쿼리가 배포 되는 경우 스크립트의 인스턴스는 포함 된 데이터에 대 한 각 노드에서 실행 됩니다.

## <a name="reserved-r-variables"></a>예약 된 R 변수

다음 변수는 Kusto 쿼리 언어와 R 코드 간의 상호 작용을 위해 예약 되어 있습니다.

* `df`: R 데이터 프레임 입력 표 형식 데이터 ( `T` 위의 값)입니다.
* `kargs`: *Script_parameters* 인수의 값 (R 사전)입니다.
* `result`: R 스크립트에서 만든 R 데이터 프레임. 값은 플러그 인을 따르는 Kusto 쿼리 연산자로 전송 되는 테이블 형식 데이터가 됩니다.

## <a name="enable-the-plugin"></a>플러그 인을 사용하도록 설정

* 플러그 인은 기본적으로 사용 하지 않도록 설정 되어 있습니다.
* 클러스터의 **구성** 탭에 있는 Azure Portal에서 플러그 인을 사용 하거나 사용 하지 않도록 설정 합니다. 자세한 내용은 [Azure 데이터 탐색기 클러스터에서 언어 확장 관리 (미리 보기)](../../language-extensions.md) 를 참조 하세요.

## <a name="notes-and-limitations"></a>참고 사항 및 제한 사항

* R 샌드박스 이미지는 *Windows 용 r 3.4.4*을 기반으로 하며 [Anaconda의 r Essentials 번들](https://docs.anaconda.com/anaconda/packages/r-language-pkg-docs/)의 패키지를 포함 합니다.
* R 샌드박스는 네트워크에 대 한 액세스를 제한 합니다. R 코드는 이미지에 포함 되지 않은 추가 패키지를 동적으로 설치할 수 없습니다. 특정 패키지가 필요한 경우 Azure Portal에서 **새 지원 요청** 을 여세요.

## <a name="examples"></a>예

```kusto
range x from 1 to 360 step 1
| evaluate r(
//
typeof(*, fx:double),               //  Output schema: append a new fx column to original table 
//
'result <- df\n'                    //  The R decorated script
'n <- nrow(df)\n'
'g <- kargs$gain\n'
'f <- kargs$cycles\n'
'result$fx <- g * sin(df$x / n * 2 * pi * f)'
//
, pack('gain', 100, 'cycles', 4)    //  dictionary of parameters
)
| render linechart 
```

:::image type="content" source="images/plugin/sine-demo.png" alt-text="사인 데모" border="false":::

## <a name="performance-tips"></a>성능 팁

* 플러그 인의 입력 데이터 집합을 필요한 최소 크기 (열/행)로 줄입니다.
    * 가능 하면 Kusto 쿼리 언어를 사용 하 여 원본 데이터 집합에서 필터를 사용 합니다.
    * 원본 열의 하위 집합에 대해 계산을 수행 하려면 플러그 인을 호출 하기 전에 해당 열만 프로젝션 합니다.
* `hint.distribution = per_node`스크립트의 논리를 배포할 때마다를 사용 합니다.
    * 또한 [partition 연산자](partitionoperator.md) 를 사용 하 여 입력 데이터 집합을 분할할 수 있습니다.
* 가능 하면 Kusto 쿼리 언어를 사용 하 여 R 스크립트의 논리를 구현 합니다.

    예를 들어:

    ```kusto    
    .show operations
    | where StartedOn > ago(1d) // Filtering out irrelevant records before invoking the plugin
    | project d_seconds = Duration / 1s // Projecting only a subset of the necessary columns
    | evaluate hint.distribution = per_node r( // Using per_node distribution, as the script's logic allows it
        typeof(*, d2:double),
        'result <- df\n'
        'result$d2 <- df$d_seconds\n' // Negative example: this logic should have been written using Kusto's query language
      )
    | summarize avg = avg(d2)
    ```

## <a name="usage-tips"></a>사용 팁

* Kusto 문자열 구분 기호와 R 문자열 구분 기호 사이의 충돌을 방지 하려면 다음을 수행 합니다.  
    * `'`Kusto 쿼리에서 kusto 문자열 리터럴에 작은따옴표 ()를 사용 합니다.
    * R `"` 스크립트에서 r 문자열 리터럴에 큰따옴표 문자 ()를 사용 합니다.
* 외부 [데이터 연산자](externaldata-operator.md) 를 사용 하 여 Azure blob storage 또는 공용 GitHub 리포지토리와 같은 외부 위치에 저장 한 스크립트의 콘텐츠를 가져올 수 있습니다.
  
  예를 들어:

    ```kusto
    let script = 
        externaldata(script:string)
        [h'https://kustoscriptsamples.blob.core.windows.net/samples/R/sample_script.r']
        with(format = raw);
    range x from 1 to 360 step 1
    | evaluate r(
        typeof(*, fx:double),
        toscalar(script), 
        pack('gain', 100, 'cycles', 4))
    | render linechart 
    ```

---

::: zone-end

::: zone pivot="azuremonitor"

이 기능은에서 지원 되지 않습니다 Azure Monitor

::: zone-end

