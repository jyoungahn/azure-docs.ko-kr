---
title: Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 데이터 쿼리
description: Azure 데이터 탐색기를 사용 하 여 Azure Monitor에서 Azure 데이터 탐색기, Log Analytics 작업 영역 및 클래식 Application Insights 응용 프로그램 간에 교차곱 쿼리를 수행 합니다.
author: orens
ms.author: bwren
ms.reviewer: bwren
ms.subservice: logs
ms.topic: conceptual
ms.date: 10/13/2020
ms.openlocfilehash: dd29b18dda46a00966a0e5adea7e06be8f43ad35
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96444942"
---
# <a name="query-data-in-azure-monitor-using-azure-data-explorer-preview"></a>Azure 데이터 탐색기 (미리 보기)를 사용 하 여 Azure Monitor에서 데이터 쿼리

Azure 데이터 탐색기는 Azure 데이터 탐색기, [Application Insights (AI)](/azure/azure-monitor/app/app-insights-overview)및 [Log Analytics (LA)](/azure/azure-monitor/platform/data-platform-logs)사이에서 서비스 간 쿼리를 지원 합니다. 그런 다음 Azure 데이터 탐색기 도구를 사용 하 여 Log Analytics/Application Insights 작업 영역을 쿼리하고 크로스 서비스 쿼리에서이 작업 영역을 참조할 수 있습니다. 이 문서에서는 서비스 간 쿼리를 만드는 방법 및 Azure 데이터 탐색기 웹 UI에 Log Analytics/Application Insights 작업 영역을 추가 하는 방법을 보여 줍니다.

Azure 데이터 탐색기 크로스 서비스 쿼리 흐름: :::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-monitor-flow.png" alt-text="azure 데이터 탐색기 프록시 흐름입니다.":::

> [!NOTE]
> * Azure 데이터 탐색기 클라이언트 도구에서 직접 또는 azure 데이터 탐색기 클러스터에서 쿼리를 실행 하 여 간접적으로 Azure 데이터 탐색기에서 Azure Monitor 데이터를 쿼리 하는 기능은 미리 보기 모드입니다.
>* [서비스 간 쿼리](mailto:adxproxy@microsoft.com) 팀에 질문을 합니다.

## <a name="add-a-log-analyticsapplication-insights-workspace-to-azure-data-explorer-client-tools"></a>Azure 데이터 탐색기 클라이언트 도구에 Log Analytics/Application Insights 작업 영역 추가

1. Log Analytics 또는 Application Insights 클러스터에 연결 하기 전에 Azure 데이터 탐색기 기본 클러스터 (예: *help* cluster)가 왼쪽 메뉴에 나타나는지 확인 합니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-data-explorer-web-ui-help-cluster.png" alt-text="Azure 데이터 탐색기 native cluster.":::

 Azure 데이터 탐색기 UI (에서 https://dataexplorer.azure.com/clusters) **클러스터 추가** 를 선택 합니다.

2. **클러스터 추가** 창에서 LA 또는 AI 클러스터의 URL을 추가 합니다.

    * LA의 경우: `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>`
    * AI의 경우: `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>`

    * **추가** 를 선택합니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-add-cluster.png" alt-text="클러스터를 추가 합니다.":::
 
>[!NOTE]
>둘 이상의 Log Analytics/Application insights 작업 영역에 대 한 연결을 추가 하는 경우 각각 다른 이름을 지정 합니다. 그렇지 않으면 모두 왼쪽 창에 같은 이름을 갖게 됩니다.

 연결이 설정 되 면 기본 Azure 데이터 탐색기 클러스터와 함께 Log Analytics 또는 Application Insights 작업 영역이 왼쪽 창에 표시 됩니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-azure-data-explorer-clusters.png" alt-text="Log Analytics 및 Azure 데이터 탐색기 클러스터":::
 
> [!NOTE]
> 매핑될 수 있는 Azure Monitor 작업 영역 수는 100 개로 제한 됩니다.

## <a name="create-queries-using-azure-monitor-data"></a>Azure Monitor 데이터를 사용 하 여 쿼리 만들기

Kusto 탐색기, Azure 데이터 탐색기 웹 UI, Jupyter Kqlmagic, Flow, PowerQuery, PowerShell, Lens REST API와 같이 Kusto 쿼리를 지 원하는 클라이언트 도구를 사용 하 여 쿼리를 실행할 수 있습니다.

> [!NOTE]
> 서비스 간 쿼리 기능은 데이터 검색에만 사용 됩니다. 자세한 내용은 [함수 지원 가능성](#function-supportability)을 참조 하세요.

> [!TIP]
> * 데이터베이스 이름에는 cross service 쿼리에 지정 된 리소스와 같은 이름을 지정 해야 합니다. 이름은 대/소문자를 구분합니다.
> * 클러스터 간 쿼리에서 Application Insights 앱 및 Log Analytics 작업 영역의 이름이 올바른지 확인 합니다.
> * 이름에 특수 문자가 포함 되어 있으면 교차 서비스 쿼리에서 URL 인코딩으로 바꿉니다.
> * 이름에 [KQL 식별자 이름 규칙](https://docs.microsoft.com/azure/data-explorer/kusto/query/schema-entities/entity-names)을 충족 하지 않는 문자가 포함 된 경우에는 대시 문자로 대체 됩니다 **-** .

### <a name="direct-query-on-your-log-analytics-or-application-insights-workspaces-from-azure-data-explorer-client-tools"></a>Azure 데이터 탐색기 클라이언트 도구에서 Log Analytics 또는 Application Insights 작업 영역에 대 한 직접 쿼리

Log Analytics 또는 Application Insights 작업 영역에 대해 쿼리를 실행 합니다. 왼쪽 창에서 작업 영역을 선택 했는지 확인 합니다.
 
```kusto
Perf | take 10 // Demonstrate cross service query on the Log Analytics workspace
```

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-query-la.png" alt-text="쿼리 Log Analytics 작업 영역입니다.":::

### <a name="cross-query-of-your-log-analytics-or-application-insights-and-the-azure-data-explorer-native-cluster"></a>Log Analytics 또는 Application Insights 및 Azure 데이터 탐색기 native 클러스터의 크로스 쿼리

클러스터 간 서비스 쿼리를 실행 하는 경우 왼쪽 창에서 Azure 데이터 탐색기 native cluster가 선택 되어 있는지 확인 합니다. 다음 예에서는 Log Analytics workspace와 [union을 사용 하 여](/azure/data-explorer/kusto/query/unionoperator) Azure 데이터 탐색기 클러스터 테이블을 결합 하는 방법을 보여 줍니다.

```kusto
union StormEvents, cluster('https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>').Perf
| take 10
```

```kusto
let CL1 = 'https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>';
union <Azure Data Explorer table>, cluster(CL1).database(<workspace-name>).<table name>
```

:::image type="content" source="media\azure-data-explorer-monitor-proxy\azure-data-explorer-cross-query-proxy.png" alt-text="Azure 데이터 탐색기에서 서비스 간 쿼리":::

연산자를 사용 하는 대신 [ `join` 연산자](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator)를 사용 하 여 [`hint`](https://docs.microsoft.com/azure/data-explorer/kusto/query/joinoperator#join-hints) Azure 데이터 탐색기 기본 클러스터에서 실행 해야 할 수 있습니다.

### <a name="join-data-from-an-azure-data-explorer-cluster-in-one-tenant-with-an-azure-monitor-resource-in-another"></a>다른 테 넌 트의 Azure 데이터 탐색기 클러스터에서 다른 Azure Monitor 리소스를 사용 하 여 데이터 조인

서비스 간 교차 테 넌 트 쿼리는 지원 되지 않습니다. 두 리소스를 모두 확장 하는 쿼리를 실행 하기 위해 단일 테 넌 트에 로그인 되어 있습니다.

Azure 데이터 탐색기 리소스가 테 넌 트 ' A '에 있고 Log Analytics 작업 영역이 테 넌 트 ' B '에 있는 경우 다음 두 가지 방법 중 하나를 사용 합니다.

1. Azure 데이터 탐색기를 사용 하면 다른 테 넌 트의 보안 주체에 대 한 역할을 추가할 수 있습니다. 테 넌 트 ' B '의 사용자 ID를 Azure 데이터 탐색기 클러스터의 권한 있는 사용자로 추가 합니다. Azure 데이터 탐색기 클러스터의 *[' TrustedExternalTenant '](https://docs.microsoft.com/powershell/module/az.kusto/update-azkustocluster)* 속성에 테 넌 트 ' B '가 포함 되어 있는지 확인 합니다. ' B ' 테 넌 트에서 완전히 쿼리를 실행 합니다.

2. [Lighthouse](https://docs.microsoft.com/azure/lighthouse/) 를 사용 하 여 Azure Monitor 리소스를 테 넌 트 ' A '에 프로젝션 합니다.
### <a name="connect-to-azure-data-explorer-clusters-from-different-tenants"></a>다른 테 넌 트에서 Azure 데이터 탐색기 클러스터에 연결

Kusto Explorer는 사용자 계정이 원래 속한 테 넌 트에 자동으로 로그인 합니다. 동일한 사용자 계정을 사용 하 여 다른 테 넌 트의 리소스에 액세스 하려면 `tenantId` 연결 문자열에을 명시적으로 지정 해야 합니다. `Data Source=https://ade.applicationinsights.io/subscriptions/SubscriptionId/resourcegroups/ResourceGroupName;Initial Catalog=NetDefaultDB;AAD Federated Security=True;Authority ID=` **TenantId**

## <a name="function-supportability"></a>함수 지원 가능성

Azure 데이터 탐색기 크로스 서비스 쿼리는 Application Insights 및 Log Analytics 둘 다에 대 한 함수를 지원 합니다.
이 기능을 통해 클러스터 간 쿼리는 Azure Monitor 테이블 형식 함수를 직접 참조할 수 있습니다.
Cross service 쿼리에서 지원 되는 명령은 다음과 같습니다.

* `.show functions`
* `.show function {FunctionName}`
* `.show database {DatabaseName} schema as json`

다음 이미지는 Azure 데이터 탐색기 웹 UI에서 테이블 형식 함수를 쿼리 하는 예를 보여 줍니다.
함수를 사용 하려면 쿼리 창에서 이름을 실행 합니다.

:::image type="content" source="media/azure-data-explorer-monitor-proxy/azure-monitor-proxy-function-query.png" alt-text="Azure 데이터 탐색기 웹 UI에서 테이블 형식 함수를 쿼리 합니다.":::

## <a name="additional-syntax-examples"></a>추가 구문 예제

Log Analytics 또는 Application Insights 클러스터를 호출 하는 경우 다음 구문 옵션을 사용할 수 있습니다.

|구문 설명  |Application Insights  |Log Analytics  |
|----------------|---------|---------|
| 이 구독에 정의 된 리소스를 포함 하는 클러스터 내의 데이터베이스 (**클러스터 간 쿼리에 권장**) |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>').database('<ai-app-name>` ) | cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>').database('<workspace-name>` )     |
| 이 구독의 모든 앱/작업 영역을 포함 하는 클러스터입니다.    |     cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>` )    |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>` )     |
|구독의 모든 앱/작업 영역을 포함 하 고이 리소스 그룹의 구성원 인 클러스터    |   cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |    cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>` )      |
|이 구독에 정의 된 리소스를 포함 하는 클러스터      |    cluster ( `https://ade.applicationinsights.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.insights/components/<ai-app-name>` )    |  cluster ( `https://ade.loganalytics.io/subscriptions/<subscription-id>/resourcegroups/<resource-group-name>/providers/microsoft.operationalinsights/workspaces/<workspace-name>` )     |

## <a name="next-steps"></a>다음 단계

- [Log Analytics 작업 영역 및 Application Insights의 데이터 구조](data-platform-logs.md)에 대해 자세히 알아보세요.
- [Azure 데이터 탐색기에서 쿼리를 작성](https://docs.microsoft.com/azure/data-explorer/write-queries)하는 방법을 알아봅니다.