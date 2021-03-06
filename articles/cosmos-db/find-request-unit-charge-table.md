---
title: Azure Cosmos DB에서 Table API 쿼리의 요청 단위 (r) 요금을 찾습니다.
description: Azure Cosmos 컨테이너에 대해 실행 되는 Table API 쿼리의 요청 단위 (r) 요금을 찾는 방법에 대해 알아봅니다. Azure Portal, .NET, Java, Python 및 Node.js 언어를 사용 하 여 함께 비용을 찾을 수 있습니다.
author: ThomasWeiss
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: how-to
ms.date: 10/14/2020
ms.author: thweiss
ms.custom: devx-track-js
ms.openlocfilehash: 1d7a12e436fd3bc1700dc4a1d76dc2b80d861144
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/30/2020
ms.locfileid: "93078461"
---
# <a name="find-the-request-unit-charge-for-operations-executed-in-azure-cosmos-db-table-api"></a>Azure Cosmos DB에서 실행 된 작업에 대 한 요청 단위 요금을 찾습니다 Table API
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

Azure Cosmos DB는 SQL, MongoDB, Cassandra, Gremlin, Table 등의 많은 API를 지원합니다. 각 API에는 고유한 데이터베이스 작업 세트가 있습니다. 이러한 작업은 간단한 지점 읽기 및 쓰기에서 복잡한 쿼리에 이르기까지 다양합니다. 각 데이터베이스 작업은 작업의 복잡도에 따라 시스템 리소스를 사용합니다.

모든 데이터베이스 작업 비용은 Azure Cosmos DB에서 정규화되고 RU(요청 단위)를 기준으로 표시됩니다. RUs는 Azure Cosmos DB에서 지 원하는 데이터베이스 작업을 수행 하는 데 필요한 CPU, IOPS 및 메모리와 같은 시스템 리소스를 추상화 하는 성능 통화로 간주할 수 있습니다. Azure Cosmos 컨테이너 조작에 사용하는 API에 상관없이 비용은 항상 RU로 측정됩니다. 데이터베이스 작업이 쓰기, 지점 읽기 또는 쿼리 인지에 상관 없이 비용은 항상 RUs로 측정 됩니다. 자세히 알아보려면 [요청 단위 및 it 고려 사항](request-units.md) 문서를 참조 하세요.

이 문서에서는 Azure Cosmos DB Table API의 컨테이너에 대해 실행 된 모든 작업에 대해 작업을 수행 [하는 데 사용할 수 있는 여러](request-units.md) 가지 방법을 제공 합니다. 다른 API를 사용 하는 경우 MongoDB, [Cassandra API](find-request-unit-charge-cassandra.md), [Gremlin API](find-request-unit-charge-gremlin.md)및 [SQL Api](find-request-unit-charge.md) 문서 [에 대 한 api](find-request-unit-charge-mongodb.md)를 참조 하 여 r u/초 요금을 찾습니다.

## <a name="use-the-net-sdk"></a>.NET SDK 사용

현재 테이블 작업에 대한 RU 요금을 반환하는 유일한 SDK는 [.NET Standard SDK](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)입니다. `TableResult` 개체는 Azure Cosmos DB Table API에 대해 사용할 경우 SDK에 의해 자동으로 채워지는 `RequestCharge` 속성을 표시합니다.

```csharp
CloudTable tableReference = client.GetTableReference("table");
TableResult tableResult = tableReference.Execute(TableOperation.Insert(new DynamicTableEntity("partitionKey", "rowKey")));
if (tableResult.RequestCharge.HasValue) // would be false when using Azure Storage Tables
{
    double requestCharge = tableResult.RequestCharge.Value;
}
```

자세한 내용은 [빠른 시작: .NET SDK를 사용 하 여 Table API 앱 빌드 및 Azure Cosmos DB](create-table-dotnet.md)를 참조 하세요.

## <a name="next-steps"></a>다음 단계

RU 사용량을 최적화하는 방법에 대한 자세한 내용은 다음 문서를 참조하세요.

* [Azure Cosmos DB의 요청 단위 및 처리량](request-units.md)
* [Azure Cosmos DB의 프로비저닝된 처리량 비용 최적화](optimize-cost-throughput.md)
* [Azure Cosmos DB의 쿼리 비용 최적화](./optimize-cost-reads-writes.md)