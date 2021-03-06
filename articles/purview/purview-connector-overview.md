---
title: 부서의 범위 커넥터 개요
description: 이 문서에서는 부서의 범위에서 지원 되는 다양 한 데이터 저장소 및 기능을 간략하게 설명 합니다.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 11/13/2020
ms.openlocfilehash: 88fb9c823df6ae5df345911ccce1c579009fba02
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/03/2020
ms.locfileid: "96555142"
---
# <a name="supported-data-stores"></a>지원되는 데이터 저장소

부서의 범위는 다음과 같은 데이터 저장소를 지원 합니다. 각 데이터 저장소를 클릭 하 여 지원 되는 기능 및 해당 구성에 대해 자세히 알아보세요.

## <a name="purview-data-sources"></a>부서의 범위 데이터 원본

|**범주**|  **데이터 저장소**  |**메타 데이터 추출**|**전체 검색**|**증분 검색**|**범위 검색**|**분류**|**계보**|
|---|---|---|---|---|---|---|---|
| Azure | [Azure Blob Storage](register-scan-azure-blob-storage-source.md)| 예| 예| 예| 예| 예| 예|
||[Azure Cosmos DB](register-scan-azure-cosmos-database.md)|예| 예| 예| 예| 예| 예|
||[Azure Data Explorer](register-scan-azure-data-explorer.md)|예| 예| 예| 예| 예| 예|
||[Azure Data Lake Storage Gen1](register-scan-adls-gen1.md)|예| 예| 예| 예| 예| 예|
||[Azure Data Lake Storage Gen2](register-scan-adls-gen2.md)|예| 예| 예| 예| 예| 예|
||[Azure SQL Database](register-scan-azure-sql-database.md)|예| 예| 아니요| 예| 예| 예|
||[Azure SQL Database Managed Instance](register-scan-azure-sql-database-managed-instance.md)|예| 예| 아니요| 예| 예| 예|
||[Azure Synapse Analytics (이전의 SQL DW)](register-scan-azure-synapse-analytics.md)|예| 예| 아니요| 예| 예| 예|
|데이터베이스|[SQL Server](register-scan-on-premises-sql-server.md)|예| 예| 아니요| 예| 예| 예|
|Power BI|[Power BI](register-scan-power-bi-tenant.md)|예| 예| 아니요| 아니요| 아니요| 예|

## <a name="next-steps"></a>다음 단계

- [Azure Blob storage 원본 등록 및 검색](register-scan-azure-blob-storage-source.md)