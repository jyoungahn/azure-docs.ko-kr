---
title: 포함 파일
description: 포함 파일
services: functions
author: craigshoemaker
manager: gwallace
ms.service: azure-functions
ms.topic: include
ms.date: 08/02/2019
ms.author: cshoe
ms.custom: include file
ms.openlocfilehash: 09e56395122f3be897d8bfb34b1d51cee034b505
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/30/2020
ms.locfileid: "96356230"
---
다음 형식에 바인딩하여 blob을 쓸 수 있습니다.

* `TextWriter`
* `out string`
* `out Byte[]`
* `CloudBlobStream`
* `Stream`
* `CloudBlobContainer`<sup>1</sup>
* `CloudBlobDirectory`
* `ICloudBlob`<sup>sr-2</sup>
* `CloudBlockBlob`<sup>sr-2</sup>
* `CloudPageBlob`<sup>sr-2</sup>
* `CloudAppendBlob`<sup>sr-2</sup>

<sup>1</sup>*function.json* 에서 `direction` 또는 C# 클래스 라이브러리에서 `FileAccess.Read`의 “in” 바인딩이 필요합니다. 그러나 런타임에서 제공하는 컨테이너를 사용하여 컨테이너에 BLOB 업로드 등의 쓰기 작업을 수행할 수 있습니다.

<sup>2</sup>*function.json* 에서 `direction` 또는 C# 클래스 라이브러리에서 `FileAccess.ReadWrite`의 “inout” 바인딩이 필요합니다.

스토리지 SDK 형식 중 하나에 바인딩하려고 하면 오류 메시지가 표시되는 경우 [올바른 Storage SDK 버전](../articles/azure-functions/functions-bindings-storage-blob.md#azure-storage-sdk-version-in-functions-1x)에 대한 참조가 있는지 확인합니다.

`string` 또는 `Byte[]`에 바인딩하는 방식은 전체 Blob 내용이 메모리에 로드되므로 Blob 크기가 작은 경우에만 권장됩니다. 일반적으로 `Stream` 또는 `CloudBlockBlob` 형식을 사용하는 것이 좋습니다. 자세한 내용은 이 문서의 앞부분에 나오는 [동시성 및 메모리 사용량](../articles/azure-functions/functions-bindings-storage-blob-trigger.md#concurrency-and-memory-usage)을 참조하세요.
