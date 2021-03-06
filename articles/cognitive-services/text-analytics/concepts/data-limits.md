---
title: Text Analytics API에 대한 데이터 제한
titleSuffix: Azure Cognitive Services
description: Azure Cognitive Services의 Text Analytics API에 대한 데이터 제한.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: overview
ms.date: 11/19/2020
ms.author: aahi
ms.reviewer: chtufts
ms.openlocfilehash: c60adb09da05ba945bcf6ccb55e71c395f064211
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965105"
---
# <a name="data-and-rate-limits-for-the-text-analytics-api"></a>Text Analytics API에 대한 데이터 및 속도 제한
<a name="data-limits"></a>

이 문서를 사용하여 Text Analytics API에 데이터를 보낼 수 있는 크기 및 속도에 대한 제한을 찾습니다. 

## <a name="data-limits"></a>데이터 제한

> [!NOTE]
> * 허용 한도보다 큰 문서를 분석해야 하는 경우 텍스트를 API로 보내기 전에 텍스트를 더 작은 텍스트 청크로 분할할 수 있습니다. 
> * 문서는 텍스트 문자의 단일 문자열입니다.  

| 제한 | 값 |
|------------------------|---------------|
| 단일 문서의 최대 크기 | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 5,120자. Text Analytics for health에도 적용됩니다. |
| 단일 문서의 최대 크기(`/analyze` 엔드포인트)  | [StringInfo.LengthInTextElements](/dotnet/api/system.globalization.stringinfo.lengthintextelements)에 의해 측정된 125K자. Text Analytics for health에 적용되지 않습니다. |
| 전체 요청의 최대 크기 | 1MB Text Analytics for health에도 적용됩니다. |

단일 요청으로 보낼 수 있는 최대 문서 수는 사용 중인 API 버전 및 기능에 따라 달라집니다. 문서가 최대 크기(125K자)를 초과하는 경우 `/analyze` 엔드포인트는 전체 요청을 거부합니다.

#### <a name="version-3"></a>[버전 3](#tab/version-3)

현재 v3 API에 대한 제한은 다음과 같습니다. 아래 제한을 초과하면 HTTP 400 오류 코드가 생성됩니다.


| 기능 | 요청당 최대 문서 수 | 
|----------|-----------|
| 언어 검색 | 1000 |
| 감정 분석 | 10 |
| 오피니언 마이닝 | 10 |
| 핵심 문구 추출 | 10 |
| 명명된 엔터티 인식 | 5 |
| 엔터티 연결 | 5 |
| Text Analytics for health  | 웹 기반 API의 경우 10, 컨테이너의 경우 1000입니다. |
| 엔드포인트 분석 | 모든 작업의 경우 25입니다. |

#### <a name="version-2"></a>[버전 2](#tab/version-2)

| 기능 | 요청당 최대 문서 수 | 
|----------|-----------|
| 언어 검색 | 1000 |
| 감정 분석 | 1000 |
| 핵심 문구 추출 | 1000 |
| 명명된 엔터티 인식 | 1000 |
| 엔터티 연결 | 1000 |

---

## <a name="rate-limits"></a>속도 제한

속도 제한은 [가격 책정 계층](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)에 따라 달라집니다. 이러한 제한은 API의 두 버전 모두에 동일합니다. 이러한 속도 제한은 설정된 속도 제한이 없는 Text Analytics for health 컨테이너에는 적용되지 않습니다.

| 계층          | 초당 요청 | 분당 요청 |
|---------------|---------------------|---------------------|
| S / 다중 서비스 | 1000                | 1000                |
| S0 / F0         | 100                 | 300                 |
| S1            | 200                 | 300                 |
| S2            | 300                 | 300                 |
| S3            | 500                 | 500                 |
| S4            | 1000                | 1000                |

요청 비율은 각 Text Analytics 기능에 대해 개별적으로 측정됩니다. 가격 책정 계층에 대한 최대 요청 수를 각 기능에 동시에 보낼 수 있습니다. 예를 들어 `S` 계층에 있고 한 번에 1000개의 요청을 보내는 경우 59초 동안 다른 요청을 보낼 수 없습니다.


## <a name="see-also"></a>참고 항목

* [Text Analytics API란?](../overview.md)
* [가격 정보](https://azure.microsoft.com/pricing/details/cognitive-services/text-analytics/)
