---
title: parse_command_line ()-Azure 데이터 탐색기
description: 이 문서에서는 Azure 데이터 탐색기에서 parse_command_line ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: slneimer
ms.service: data-explorer
ms.topic: reference
ms.date: 06/28/2020
ms.openlocfilehash: da8fe0bfedf5766f4599509e83fa1c7c050d069f
ms.sourcegitcommit: 88f8ad67711a4f614d65d745af699d013d01af32
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/16/2020
ms.locfileid: "94638991"
---
# <a name="parse_command_line"></a>parse_command_line()

유니코드 명령줄 문자열을 구문 분석 하 고 명령줄 인수의 동적 배열을 반환 합니다.

## <a name="syntax"></a>구문

`parse_command_line(`*command_line* , *parser_type*`)`

## <a name="arguments"></a>인수

* *command_line* : 구문 분석할 명령줄입니다.
* *parser_type* : 현재 지원 되는 유일한 값은 `"windows"` [CommandLineToArgvW](/windows/win32/api/shellapi/nf-shellapi-commandlinetoargvw)와 동일한 방식으로 명령줄을 구문 분석 하는입니다.

## <a name="returns"></a>반환

명령줄 인수의 동적 배열입니다.

## <a name="example"></a>예제

<!-- csl: https://help.kusto.windows.net:443/Samples -->
```kusto
print parse_command_line("echo \"hello world!\"", "windows")
```

|결과|
|---|
|["echo", "hello 세계!"]|
