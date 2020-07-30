---
title: exp10 ()-Azure 데이터 탐색기 | Microsoft Docs
description: 이 문서에서는 Azure 데이터 탐색기의 exp10 ()에 대해 설명 합니다.
services: data-explorer
author: orspod
ms.author: orspodek
ms.reviewer: rkarlin
ms.service: data-explorer
ms.topic: reference
ms.date: 03/25/2019
ms.openlocfilehash: 260fbc24bb5cb653cf6df9e976c2c78682eb9ab6
ms.sourcegitcommit: 09da3f26b4235368297b8b9b604d4282228a443c
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 07/28/2020
ms.locfileid: "87348193"
---
# <a name="exp10"></a>exp10()

X의 밑이 10 인 지 수 함수 이며, 10은 거듭제곱 x: 10 ^ x에 발생 합니다.  

## <a name="syntax"></a>Syntax

`exp10(`*.x*`)`

## <a name="arguments"></a>인수

* *x*: 지 수 값의 실수입니다.

## <a name="returns"></a>반환

* X의 지 수 값입니다.
* 자연 (밑수 10)의 경우 [log10 ()](log10-function.md)를 참조 하세요.
* Base-e 및 밑이 2 인 지 수 함수의 경우 [exp ()](exp-function.md), [exp2 ()](exp2-function.md) 를 참조 하세요.