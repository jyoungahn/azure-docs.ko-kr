---
title: Azure Data Factory에서 azure SQL Managed Instance를 Azure SQL Server Integration Services (SSIS)와 함께 사용
description: Azure Data Factory에서 SQL Server Integration Services (SSIS)를 사용 하 여 Azure SQL Managed Instance를 사용 하는 방법을 알아봅니다.
services: data-factory
documentationcenter: ''
author: chugugrace
ms.author: chugu
ms.reviewer: ''
manager: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 4/15/2020
ms.openlocfilehash: 86bff161e29384b10030ed3d524301f6dea6037e
ms.sourcegitcommit: fb3c846de147cc2e3515cd8219d8c84790e3a442
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/27/2020
ms.locfileid: "92634167"
---
# <a name="use-azure-sql-managed-instance-with-sql-server-integration-services-ssis-in-azure-data-factory"></a>Azure Data Factory에서 SQL Server Integration Services (SSIS)를 사용 하 여 Azure SQL Managed Instance 사용

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-xxx-md.md)]

이제 SSIS(SQL Server Integration Services) 프로젝트 및 워크로드를 Azure 클라우드로 이동할 수 있습니다. SSMS (SQL Server Management Studio)와 같은 친숙 한 도구를 사용 하 여 Azure SQL Database 또는 SQL Managed Instance에서 SSIS 프로젝트 및 패키지를 배포, 실행 및 관리 합니다. 이 문서에서는 azure SSIS IR (통합 런타임)을 사용 하 여 Azure SQL Managed Instance를 사용 하는 경우 다음과 같은 특정 영역을 강조 합니다.

- [Azure SQL Managed Instance에서 호스트 하는 SSISDB (SSIS 카탈로그)를 사용 하 여 Azure-SSIS IR 프로 비전](#provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance)
- [Azure SQL Managed Instance 에이전트 작업으로 SSIS 패키지 실행](how-to-invoke-ssis-package-managed-instance-agent.md)
- [Azure SQL Managed Instance 에이전트 작업으로 SSISDB 로그 정리](#clean-up-ssisdb-logs)
- [Azure SQL Managed Instance를 사용 하 여 장애 조치 (failover) Azure-SSIS IR](configure-bcdr-azure-ssis-integration-runtime.md#azure-ssis-ir-failover-with-a-sql-managed-instance)
- [Azure SQL Managed Instance를 데이터베이스 작업 대상으로 사용 하 여 ADF의 SSIS로 온-프레미스 SSIS 작업 마이그레이션](scenario-ssis-migration-overview.md#azure-sql-managed-instance-as-database-workload-destination)

## <a name="provision-azure-ssis-ir-with-ssisdb-hosted-by-azure-sql-managed-instance"></a>Azure SQL Managed Instance에서 호스트 하는 SSISDB를 사용 하 여 Azure-SSIS IR 프로 비전

### <a name="prerequisites"></a>사전 요구 사항

1. Azure Active Directory 인증을 선택 하는 경우 [AZURE SQL Managed Instance에서 Azure Active Directory (AZURE AD)를 사용 하도록 설정](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)합니다.

1. 개인 끝점을 통해 또는 공용 끝점을 통해 SQL Managed Instance를 연결 하는 방법을 선택 합니다.

    - 프라이빗 엔드포인트를 통해(기본 설정)

        1. 조인할 Azure-SSIS IR의 가상 네트워크를 선택합니다.
            - **다른 서브넷** 을 사용 하 여 관리 되는 인스턴스와 동일한 가상 네트워크 내에 있습니다.
            - 가상 네트워크 피어 링 (글로벌 VNet 피어 링 제약 조건으로 인해 동일한 지역으로 제한 됨) 또는 가상 네트워크에서 가상 네트워크로의 연결을 통해 관리 되는 인스턴스가 아닌 다른 가상 네트워크 내에 있습니다.

            SQL Managed Instance 연결에 대 한 자세한 내용은 [AZURE sql Managed Instance에 응용 프로그램 연결](https://review.docs.microsoft.com/azure/sql-database/sql-database-managed-instance-connect-app)을 참조 하세요.

        1. [가상 네트워크를 구성합니다](#configure-virtual-network).

    - 퍼블릭 엔드포인트를 통해

        Azure SQL 관리 되는 인스턴스는 [공용 끝점](../azure-sql/managed-instance/public-endpoint-configure.md)을 통해 연결을 제공할 수 있습니다. SQL Managed Instance와 Azure-SSIS IR 간에 트래픽을 허용 하려면 인바운드 및 아웃 바운드 요구 사항이 충족 되어야 합니다.

        - Azure-SSIS IR이 가상 네트워크 내부에 없는 경우(기본 설정)

            Azure-SSIS IR에서 인바운드 트래픽을 허용 하는 **SQL Managed Instance의 인바운드 요구 사항** 입니다.

            | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 |
            |---|---|---|---|---|
            |TCP|Azure 클라우드 서비스 태그|*|VirtualNetwork|3342|

            자세한 내용은 [네트워크 보안 그룹에서 퍼블릭 엔드포인트 트래픽 허용](../azure-sql/managed-instance/public-endpoint-configure.md#allow-public-endpoint-traffic-on-the-network-security-group)을 참조하세요.

        - Azure-SSIS IR이 가상 네트워크 내부에 있는 경우

            SQL Managed Instance이 Azure-SSIS IR 지원 하지 않는 지역에 있는 경우에는 글로벌 VNet 피어 링 제한으로 인해 VNet 피어 링 없이 가상 네트워크 내에 있는 Azure-SSIS IR 있습니다. 이 시나리오에서 **가상 네트워크 내의 Azure-SSIS IR** 는 **공용 끝점을 통해** SQL Managed Instance을 연결 합니다. 아래 NSG (네트워크 보안 그룹) 규칙을 사용 하 여 SQL Managed Instance와 Azure-SSIS IR 간의 트래픽을 허용 합니다.

            1. Azure-SSIS IR에서 인바운드 트래픽을 허용 하는 **SQL Managed Instance의 인바운드 요구 사항** 입니다.

                | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 |대상 포트 범위 |
                |---|---|---|---|---|
                |TCP|Azure-SSIS IR의 고정 IP 주소 <br> 자세한 내용은 [Azure-SSIS IR의 사용자 퍼블릭 IP 필요](join-azure-ssis-integration-runtime-virtual-network.md#publicIP)를 참조하세요.|*|VirtualNetwork|3342|

             1. SQL Managed Instance에 대 한 아웃 바운드 트래픽을 허용 하 **는 Azure-SSIS IR의 아웃 바운드 요구 사항**

                | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 |대상 포트 범위 |
                |---|---|---|---|---|
                |TCP|VirtualNetwork|*|[SQL Managed Instance 공용 끝점 IP 주소](../azure-sql/managed-instance/management-endpoint-find-ip-address.md)|3342|

### <a name="configure-virtual-network"></a>가상 네트워크 구성

1. **사용자 권한** . Azure-SSIS IR를 만드는 사용자는 아래 옵션 중 하나를 사용하여 적어도 Azure Data Factory 리소스에서 [역할 할당](../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-for-a-user-at-a-scope)이 있어야 합니다.

    - 기본 제공 네트워크 참가자 역할을 사용합니다. 이 역할에는 범위가 필요 이상으로 큰 _Microsoft.Network/\*_ 권한이 제공됩니다.
    - 필요한 _Microsoft.Network/virtualNetworks/\*/join/action_ 권한만 포함하는 사용자 지정 역할을 만듭니다. 또한 Azure Resource Manager 가상 네트워크에 조인하는 동안 Azure-SSIS IR의 고유한 퍼블릭 IP 주소를 요구하려면 역할에 _Microsoft.Network/publicIPAddresses/*/join/action_ 권한도 포함합니다.

1. **가상 네트워크** .

    1. 가상 네트워크의 리소스 그룹이 특정 Azure 네트워크 리소스를 만들고 삭제할 수 있는지 확인합니다.

        Azure-SSIS IR은 가상 네트워크와 동일한 리소스 그룹에 특정 네트워크 리소스를 만들어야 합니다. 해당 리소스는 다음과 같습니다.
        - 이름이 *\<Guid> -azurebatch-cloudserviceloadbalancer* 인 Azure 부하 분산 장치
        - 이름이 * \<Guid> -azurebatch-cloudservicenetworksecuritygroup 인 네트워크 보안 그룹
        - 이름이 -azurebatch-cloudservicepublicip인 Azure 퍼블릭 IP 주소

        해당 리소스는 Azure-SSIS IR이 시작될 때 생성됩니다. Azure-SSIS IR이 중지될 때 삭제됩니다. Azure-SSIS IR 중지가 차단되지 않게 하려면 다른 리소스에서 해당 네트워크 리소스를 다시 사용하지 마세요.

    1. 가상 네트워크가 속한 리소스 그룹/구독에 [리소스 잠금](../azure-resource-manager/management/lock-resources.md)이 없는지 확인합니다. 읽기 전용/삭제 잠금을 구성하는 경우 Azure-SSIS IR 시작 및 중지에 실패하거나 응답이 중지됩니다.

    1. 가상 네트워크가 속한 리소스 그룹/구독에 다음 리소스를 만들 수 없도록 하는 Azure 정책이 없는지 확인합니다.
        - Microsoft.Network/LoadBalancers
        - Microsoft.Network/NetworkSecurityGroups

    1. NSG (네트워크 보안 그룹) 규칙에서 트래픽을 허용 하 여 SQL Managed Instance와 Azure-SSIS IR 간에 트래픽을 허용 하 고 Azure-SSIS IR에 필요한 트래픽을 허용 합니다.
        1. Azure-SSIS IR에서 인바운드 트래픽을 허용 하는 **SQL Managed Instance의 인바운드 요구 사항** 입니다.

            | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
            |---|---|---|---|---|---|
            |TCP|VirtualNetwork|*|VirtualNetwork|1433, 11000-11999|SQL Database 서버 연결 정책이 **리디렉션** 대신 **프록시** 로 설정된 경우에는 포트 1433만 필요합니다.|

        1. **Azure-SSIS IR의 아웃 바운드 요구 사항은** SQL Managed Instance에 대 한 아웃 바운드 트래픽을 허용 하 고 Azure-SSIS IR에 필요한 기타 트래픽을 허용 합니다.

        | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
        |---|---|---|---|---|---|
        | TCP | VirtualNetwork | * | VirtualNetwork | 1433, 11000-11999 |SQL Managed Instance에 대 한 아웃 바운드 트래픽을 허용 합니다. 연결 정책이 **리디렉션** 대신 **프록시** 로 설정된 경우에는 포트 1433만 필요합니다. |
        | TCP | VirtualNetwork | * | AzureCloud | 443 | 가상 네트워크의 Azure-SSIS IR 노드는 이 포트를 사용하여 Azure 서비스(예: Azure Storage, Azure Event Hubs)에 액세스합니다. |
        | TCP | VirtualNetwork | * | 인터넷 | 80 | (선택 사항) 가상 네트워크의 Azure-SSIS IR 노드는 이 포트를 사용하여 인터넷에서 인증서 해지 목록을 다운로드합니다. 이 트래픽을 차단하면 IR을 시작할 때 성능이 다운그레이드되고 인증서 사용을 위해 인증서 해지 목록을 확인하는 기능이 손실될 수 있습니다. 대상 범위를 특정 FQDN으로 추가로 좁히려면 [Azure ExpressRoute 또는 UDR(사용자 정의 경로) 사용](./join-azure-ssis-integration-runtime-virtual-network.md#route)을 참조하세요.|
        | TCP | VirtualNetwork | * | 스토리지 | 445 | (선택 사항) 이 규칙은 Azure Files에 저장된 SSIS 패키지를 실행하려는 경우에만 필요합니다. |
        |||||||

        1. Azure-SSIS IR에 필요한 트래픽을 허용하기 위한 **Azure-SSIS IR의 인바운드 요구 사항** .

        | 전송 프로토콜 | 원본 | 원본 포트 범위 | 대상 | 대상 포트 범위 | 주석 |
        |---|---|---|---|---|---|
        | TCP | BatchNodeManagement | * | VirtualNetwork | 29876, 29877(IR을 Resource Manager 가상 네트워크에 조인하는 경우) <br/><br/>10100, 20100, 30100(IR을 클래식 가상 네트워크에 조인하는 경우)| Data Factory 서비스는 해당 포트를 사용하여 가상 네트워크의 Azure-SSIS IR 노드와 통신합니다. <br/><br/> 서브넷 수준 NSG를 만드는지 여부에 관계없이 Data Factory는 Azure-SSIS IR을 호스트하는 가상 머신에 연결된 NIC(네트워크 인터페이스 카드)의 수준에서 항상 NSG를 구성합니다. 지정된 포트에서 Data Factory IP 주소의 인바운드 트래픽만 해당 NIC 수준 NSG에서 허용됩니다. 서브넷 수준에서 인터넷 트래픽에 대해 해당 포트를 여는 경우라도 Data Factory IP 주소가 아닌 IP 주소에서의 트래픽은 NIC 수준에서 차단됩니다. |
        | TCP | CorpNetSaw | * | VirtualNetwork | 3389 | (선택 사항) 이 규칙은 Microsoft 지원에서 고객에게 고급 문제 해결을 위해 열도록 요청하고 문제 해결 후 바로 닫을 수 있는 경우에만 필요합니다. **CorpNetSaw** 서비스 태그는 Microsoft 회사 네트워크의 보안 액세스 워크스테이션만 원격 데스크톱을 사용하도록 허용합니다. 또한 이 서비스 태그는 포털에서 선택할 수 없으며 Azure PowerShell 또는 Azure CLI를 통해서만 사용할 수 있습니다. <br/><br/> NIC 수준 NSG에서 포트 3389는 기본적으로 열려 있으며, Microsoft에서는 서브넷 수준 NSG에서 포트 3389를 제어하도록 허용하지만 Azure-SSIS IR은 보호를 위해 각 IR 노드의 Windows 방화벽 규칙에서 기본적으로 포트 3389 아웃바운드를 허용하지 않았습니다. |
        |||||||

    1. 자세한 내용은 [가상 네트워크 구성](join-azure-ssis-integration-runtime-virtual-network.md#virtual-network-configuration)을 참조하세요.
        - Azure-SSIS IR의 고유한 퍼블릭 IP 주소가 필요한 경우
        - 고유한 DNS(Domain Name System) 서버를 사용하는 경우
        - Azure ExpressRoute 또는 UDR(사용자 정의 경로)을 사용하는 경우
        - 사용자 지정 Azure-SSIS IR을 사용하는 경우

### <a name="provision-azure-ssis-integration-runtime"></a>Azure-SSIS 통합 런타임 프로비저닝

1. SQL Managed Instance 개인 끝점 또는 공용 끝점을 선택 합니다.

    Azure Portal/ADF 앱에서 [Azure-SSIS IR 프로 비전](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime) 하는 경우 sql 설정 페이지에서 SSIS 카탈로그 (SSISDB)를 만들 때 sql Managed Instance **개인 끝점** 또는 **공용 끝점** 을 사용 합니다.

    퍼블릭 엔드포인트 호스트 이름은 <mi_name>.public.<dns_zone>.database.windows.net 형식으로 제공되며 연결에 사용되는 포트는 3342입니다.  

    ![스크린샷은 만들기 S s I s I s 카탈로그를 선택 하 고 카탈로그 데이터베이스 서버 끝점을 입력 하 여 통합 런타임 설정을 보여 줍니다.](./media/how-to-use-sql-managed-instance-with-ir/catalog-public-endpoint.png)

1. 적용되는 경우 Azure AD 인증을 선택합니다.

    ![catalog-public-endpoint](./media/how-to-use-sql-managed-instance-with-ir/catalog-aad.png)

    Azure AD 인증을 사용 하도록 설정 하는 방법에 대 한 자세한 내용은 [AZURE SQL Managed Instance에서 AZURE Ad 사용](enable-aad-authentication-azure-ssis-ir.md#configure-azure-ad-authentication-for-azure-sql-managed-instance)을 참조 하세요.

1. 적용되는 경우 Azure-SSIS IR을 가상 네트워크에 조인합니다.

    고급 설정 페이지에서 조인할 가상 네트워크 및 서브넷을 선택합니다.
    
    SQL Managed Instance와 동일한 가상 네트워크 내에 있는 경우 SQL Managed Instance와 **다른 서브넷** 을 선택 합니다. 

    Azure-SSIS IR을 가상 네트워크에 조인하는 방법에 관한 자세한 내용은 [Azure SSIS 통합 런타임을 가상 네트워크에 조인](join-azure-ssis-integration-runtime-virtual-network.md)을 참조하세요.

    ![스크린샷에는 런타임에 연결할 가상 네트워크를 선택할 수 있는 통합 런타임 설정 고급 설정이 표시 됩니다.](./media/how-to-use-sql-managed-instance-with-ir/join-virtual-network.png)

Azure-SSIS IR을 만드는 방법에 대한 자세한 정보는 [Azure Data Factory에서 Azure-SSIS 통합 런타임 만들기](create-azure-ssis-integration-runtime.md#provision-an-azure-ssis-integration-runtime)를 참조하세요.

## <a name="clean-up-ssisdb-logs"></a>SSISDB 로그 정리

SSISDB 로그 보존 정책은 [catalog.catalog_properties](/sql/integration-services/system-views/catalog-catalog-properties-ssisdb-database?view=sql-server-ver15)에서 아래 속성을 통해 정의됩니다.

- OPERATION_CLEANUP_ENABLED

    값이 TRUE이면 RETENTION_WINDOW(일)보다 오래된 작업 정보 및 작업 메시지가 카탈로그에서 삭제되고, 값이 FALSE이면 모든 작업 정보 및 작업 메시지가 카탈로그에 저장됩니다. 참고: SQL Server 작업이 작업 정리를 수행합니다.

- RETENTION_WINDOW

    작업 정보 및 작업 메시지가 카탈로그에 저장되는 일 수 입니다. 값이 -1이면 보존 기간이 무한합니다. 참고: 정리를 실행하지 않으려면 OPERATION_CLEANUP_ENABLED를 FALSE로 설정합니다.

관리자가 설정한 보존 기간을 벗어나는 SSISDB 로그를 제거하려면 저장 프로시저 `[internal].[cleanup_server_retention_window_exclusive]`를 트리거하면 됩니다. 필요에 따라 SQL Managed Instance 에이전트 작업 실행을 예약 하 여 저장 프로시저를 트리거할 수 있습니다.

## <a name="next-steps"></a>다음 단계

- [Azure SQL Managed Instance 에이전트 작업으로 SSIS 패키지 실행](how-to-invoke-ssis-package-managed-instance-agent.md)
- [BCDR(비즈니스 연속성 및 재해 복구) 설정](configure-bcdr-azure-ssis-integration-runtime.md)
- [온-프레미스 SSIS 워크로드를 ADF의 SSIS로 마이그레이션](scenario-ssis-migration-overview.md)