---
title: 모니터링-Azure Database for MariaDB
description: 이 문서에서는 CPU, 스토리지, 연결 통계를 포함하여 Azure Database for MariaDB에 대해 모니터링 및 경고를 제공하기 위한 메트릭을 설명합니다.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: conceptual
ms.custom: references_regions
ms.date: 10/21/2020
ms.openlocfilehash: 7420a7c6355fbcd70ce57fbb36ab29d1241df31f
ms.sourcegitcommit: 6a350f39e2f04500ecb7235f5d88682eb4910ae8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 12/01/2020
ms.locfileid: "96453325"
---
# <a name="monitoring-in-azure-database-for-mariadb"></a>Azure Database for MariaDB의 모니터링
서버에 대한 데이터를 모니터링하면 워크로드에 대한 문제를 해결하고 최적화할 수 있습니다. Azure Database for MariaDB는 서버의 동작에 대한 인사이트를 제공하는 다양한 메트릭을 제공합니다.

## <a name="metrics"></a>메트릭
모든 Azure 메트릭의 빈도는 1분이고 각 메트릭은 30일의 기록을 제공합니다. 메트릭에 대한 경고를 구성할 수 있습니다. 다른 작업에는 자동화된 작업 설정, 고급 분석 수행 및 기록 보관이 포함됩니다. 자세한 내용은 [Azure 메트릭 개요](../azure-monitor/platform/data-platform.md)를 참조하세요.

단계별 지침은 [How to set up alerts](howto-alert-metric.md)(경고를 설정하는 방법)를 참조하세요.

### <a name="list-of-metrics"></a>메트릭 목록
Azure Database for MariaDB에서는 다음 메트릭을 사용할 수 있습니다.

|메트릭|메트릭 표시 이름|단위|설명|
|---|---|---|---|
|cpu_percent|CPU 백분율|백분율|사용 중인 CPU의 비율|
|memory_percent|메모리 백분율|백분율|사용 중인 메모리의 비율|
|io_consumption_percent|IO 백분율|백분율|사용 중인 IO의 비율 (기본 계층 서버에는 적용 되지 않음)|
|storage_percent|스토리지 비율|백분율|서버의 최대값을 초과하여 사용된 스토리지의 비율|
|storage_used|스토리지 사용됨|바이트|사용 중인 스토리지의 양 서비스에서 사용되는 스토리지에는 데이터베이스 파일, 트랜잭션 로그 및 서버 로그가 포함될 수 있습니다.|
|serverlog_storage_percent|서버 로그 스토리지 비율|백분율|서버의 최대 서버 로그 스토리지에서 사용된 서버 로그 스토리지의 백분율입니다.|
|serverlog_storage_percent|사용된 서버 로그 스토리지|바이트|서버 로그 스토리지에서 사용된 크기입니다.|
|serverlog_storage_limit|서버 로그 스토리지 제한|바이트|이 서버에 대한 서버 로그 스토리지의 최대 크기입니다.|
|storage_limit|스토리지 제한|바이트|이 서버의 최대 스토리지|
|active_connections|활성 연결 수|개수|서버에 대한 활성 연결 수|
|connections_failed|실패한 연결|개수|서버에 대해 실패한 연결 수|
|seconds_behind_master|복제 지연 시간(초)|개수|원본 서버에 대해 복제본 서버가 지연 된 시간 (초)입니다. (기본 계층 서버에는 적용 되지 않음)|
|network_bytes_egress|네트워크 아웃|바이트|활성 연결을 통한 네트워크 출력의 크기입니다.|
|network_bytes_ingress|네트워크 인|바이트|활성 연결을 통한 네트워크 입력의 크기입니다.|
|backup_storage_used|사용된 백업 스토리지|바이트|사용된 백업 스토리지 양. 이 메트릭은 서버에 대해 설정 된 백업 보존 기간에 따라 유지 되는 모든 전체 데이터베이스 백업, 차등 백업 및 로그 백업에서 사용 하는 저장소의 합계를 나타냅니다. 백업 빈도는 서비스에서 관리 되며 [개념 문서](concepts-backup.md)에서 설명 합니다. 지역 중복 저장소의 경우 백업 저장소 사용량이 로컬 중복 저장소의 두 배가 됩니다.|

## <a name="server-logs"></a>서버 로그

서버에 느린 쿼리 로그를 사용할 수 있습니다. 이러한 로그는 Azure Monitor 로그, Event Hubs 및 저장소 계정의 Azure 진단 로그를 통해 사용할 수도 있습니다. 로깅에 대한 자세한 내용은 [서버 로그](concepts-server-logs.md) 페이지를 참조하세요.

## <a name="query-store"></a>쿼리 저장소

[쿼리 저장소](concepts-query-store.md) 쿼리 런타임 통계 및 대기 이벤트를 포함 하 여 시간에 따른 쿼리 성능을 추적 합니다. 기능은 **mysql** 스키마에서 쿼리 런타임 성능 정보를 유지 합니다. 데이터 수집 및 스토리지은 다양한 구성 노브를 통해 제어할 수 있습니다.

## <a name="query-performance-insight"></a>쿼리

[Query Performance Insight](concepts-query-performance-insight.md)는 쿼리 저장소와 함께 작동하여 Azure Portal에서 액세스할 수 있는 시각화를 제공합니다. 이러한 차트를 사용하면 성능에 영향을 주는 주요 쿼리를 식별할 수 있습니다. Query Performance Insight은 Azure Database for MariaDB 서버의 포털 페이지의 **지능형 성능** 섹션에서 액세스할 수 있습니다.

## <a name="performance-recommendations"></a>성능 권장 사항

[성능 권장 사항](concepts-performance-recommendations.md) 기능은 워크로드 성능을 향상시킬 수 있는 기회를 식별합니다. 성능 권장 사항은 워크 로드의 성능을 향상 시킬 수 있는 새 인덱스를 만들기 위한 권장 사항을 제공 합니다. 이 기능은 인덱스 권장 사항을 생성하기 위해 쿼리 저장소에서 보고한 스키마와 워크로드를 포함하여 다양한 데이터베이스 특성을 고려하고 있습니다. 성능 권장 사항이 구현되면 고객이 성능을 테스트하여 변경에 따른 영향을 평가해야 합니다.

## <a name="planned-maintenance-notification"></a>계획 된 유지 관리 알림

[계획 된 유지 관리 알림을](./concepts-planned-maintenance-notification.md) 통해 예정 된 예정 된 유지 관리에 대 한 경고를 Azure Database for MariaDB 받을 수 있습니다. 이러한 알림은 [Service Health의](../service-health/overview.md) 계획 된 유지 관리와 통합 되어 있으므로 구독에 대해 예약 된 유지 관리 작업을 한 곳에서 모두 볼 수 있습니다. 또한 다양 한 리소스를 담당 하는 여러 연락처가 있을 수 있으므로 다양 한 리소스 그룹에 대 한 올바른 대상으로 알림을 확장 하는 데 도움이 됩니다. 이벤트 전에 예정 된 유지 관리 72 시간에 대 한 알림을 받게 됩니다.

[계획 된 유지 관리 알림](./concepts-planned-maintenance-notification.md) 문서에서 알림을 설정 하는 방법에 대해 자세히 알아보세요.

## <a name="next-steps"></a>다음 단계

- Azure Portal, REST API 또는 CLI를 사용하여 메트릭에 액세스하고 내보내는 방법에 대한 자세한 내용은 [Azure 메트릭 개요](../azure-monitor/platform/data-platform.md)를 참조하세요.
- 메트릭에 대 한 경고를 만드는 방법에 대 한 지침은 [경고를 설정 하는 방법을](howto-alert-metric.md) 참조 하세요.
- Azure Database for MariaDB의 [계획 된 유지 관리 알림에](./concepts-planned-maintenance-notification.md) 대해 자세히 알아보세요.