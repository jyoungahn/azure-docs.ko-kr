---
title: Azure Portal에서 Azure에 하이브리드 머신 연결
description: 이 문서에서는 Azure Portal에서 Azure Arc 사용 서버를 사용 하 여 에이전트를 설치 하 고 Azure에 컴퓨터를 연결 하는 방법에 대해 알아봅니다.
ms.date: 11/05/2020
ms.topic: conceptual
ms.openlocfilehash: ca3c08acdef1b2a1f7c3774f5755967d472c93ed
ms.sourcegitcommit: 0ce1ccdb34ad60321a647c691b0cff3b9d7a39c8
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/05/2020
ms.locfileid: "93398031"
---
# <a name="connect-hybrid-machines-to-azure-from-the-azure-portal"></a>Azure Portal에서 Azure에 하이브리드 머신 연결

일련의 단계를 수동으로 수행 하 여 환경에서 하나 또는 소수의 Windows 또는 Linux 컴퓨터에 대해 Azure Arc 사용 서버를 사용 하도록 설정할 수 있습니다. 또는 제공된 템플릿 스크립트를 실행하여 자동화된 방법을 사용할 수 있습니다. 이 스크립트는 두 에이전트의 다운로드 및 설치를 자동화합니다.

이 방법을 사용하려면 머신에 대한 관리자 권한으로 에이전트를 설치하고 구성할 수 있어야 합니다. Linux에서는 루트 계정을 사용하여 수행하고, Windows에서는 로컬 관리자 그룹의 멤버로 수행해야 합니다.

시작하려면 먼저 [사전 요구 사항](agent-overview.md#prerequisites)을 검토하고 구독 및 리소스에서 요구 사항을 충족하는지 확인해야 합니다. 지원 되는 지역 및 기타 관련 고려 사항에 대 한 자세한 내용은 [지원 되는 Azure 지역](overview.md#supported-regions)을 참조 하세요.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)을 만듭니다.

## <a name="generate-the-installation-script-from-the-azure-portal"></a>Azure Portal에서 설치 스크립트 생성

다운로드 및 설치를 자동화하고 Azure Arc와의 연결을 설정하는 스크립트는 Azure Portal에서 사용할 수 있습니다. 프로세스를 완료하려면 다음을 수행합니다.

1. 브라우저에서 [Azure Portal](https://portal.azure.com)로 이동합니다.

1. **서버-Azure Arc** 페이지에서 왼쪽 위에 있는 **추가** 를 선택 합니다.

1. **방법 선택** 페이지에서 **대화형 스크립트를 사용 하 여 서버 추가** 타일을 선택한 다음 **스크립트 생성** 을 선택 합니다.

1. **스크립트 생성** 페이지에서 머신을 Azure 내에서 관리하려는 구독 및 리소스 그룹을 선택합니다. 머신 메타데이터를 저장할 Azure 위치를 선택합니다. 이 위치는 리소스 그룹의 위치와 같을 수도 있고 다를 수도 있습니다.

1. **필수 구성 요소** 페이지에서 정보를 검토 한 후 **다음: 리소스 정보** 를 선택 합니다.

1. **리소스 정보** 페이지에서 다음을 제공 합니다.

    1. **리소스 그룹** 드롭다운 목록에서 컴퓨터가 관리 되는 리소스 그룹을 선택 합니다.
    1. **지역** 드롭다운 목록에서 서버 메타 데이터를 저장할 Azure 지역을 선택 합니다.
    1. **운영** 체제 드롭다운 목록에서 스크립트가 실행 되도록 구성 된 운영 체제를 선택 합니다.
    1. 컴퓨터에서 인터넷에 연결 하기 위해 프록시 서버를 통해 통신 하는 경우 프록시 서버 IP 주소 또는 컴퓨터에서 프록시 서버와 통신 하는 데 사용할 이름 및 포트 번호를 지정 합니다. 해당 값을 `http://<proxyURL>:<proxyport>` 형식으로 입력합니다.
    1. 완료되면 **다음: 태그** 를 선택합니다.

1. **태그** 페이지에서 제안 된 기본 **물리적 위치 태그** 를 검토 하 고 값을 입력 하거나 표준을 지원할 **사용자 지정 태그** 를 하나 이상 지정 합니다.

1. **다음: 스크립트 다운로드 및 실행을** 선택 합니다.

1. **스크립트 다운로드 및 실행** 페이지에서 요약 정보를 검토 하 고 **다운로드** 를 선택 합니다. 그래도 변경해야 하는 경우 **이전** 을 선택합니다.

## <a name="install-and-validate-the-agent-on-windows"></a>Windows에서 에이전트 설치 및 유효성 검사

### <a name="install-manually"></a>수동 설치

*AzureConnectedMachineAgent.msi* Windows Installer 패키지를 실행하여 Connected Machine 에이전트를 수동으로 설치할 수 있습니다. Microsoft 다운로드 센터에서 최신 버전의 [Windows 에이전트 Windows Installer 패키지](https://aka.ms/AzureConnectedMachineAgent)를 다운로드할 수 있습니다.

>[!NOTE]
>* 에이전트를 설치하거나 제거하려면 *관리자* 권한이 있어야 합니다.
>* 먼저 설치 관리자 패키지를 다운로드하여 대상 서버의 폴더 또는 공유 네트워크 폴더에 복사해야 합니다. 옵션 없이 설치 관리자 패키지를 실행하면 에이전트를 대화형으로 설치하기 위해 수행할 수 있는 설치 마법사가 시작됩니다.

컴퓨터가 프록시 서버를 통해 서비스와 통신 해야 하는 경우 에이전트를 설치한 후 다음 단계에서 설명 하는 명령을 실행 해야 합니다. 이 명령은 프록시 서버 시스템 환경 변수를 설정 합니다 `https_proxy` .

Windows Installer 패키지에 대한 명령줄 옵션에 익숙하지 않은 경우 [Msiexec 표준 명령줄](/windows/win32/msi/standard-installer-command-line-options) 및 [Msiexec 명령줄 옵션](/windows/win32/msi/command-line-options)을 검토하세요.

예를 들어 `/?` 매개 변수를 사용하여 설치 프로그램을 실행하고 도움말 및 빠른 참조 옵션을 검토합니다.

```dos
msiexec.exe /i AzureConnectedMachineAgent.msi /?
```

1. 에이전트를 자동으로 설치하고 이미 존재하는 `C:\Support\Logs` 폴더에 설치 로그 파일을 만들려면 다음 명령을 실행합니다.

    ```dos
    msiexec.exe /i AzureConnectedMachineAgent.msi /qn /l*v "C:\Support\Logs\Azcmagentsetup.log"
    ```

    설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. 로그 디렉터리는 *%ProgramData%\AzureConnectedMachineAgent\log* 입니다.

2. 컴퓨터가 프록시 서버를 통해 통신 해야 하는 경우 프록시 서버 환경 변수를 설정 하려면 다음 명령을 실행 합니다.

    ```powershell
    [Environment]::SetEnvironmentVariable("https_proxy", "http://{proxy-url}:{proxy-port}", "Machine")
    $env:https_proxy = [System.Environment]::GetEnvironmentVariable("https_proxy","Machine")
    # For the changes to take effect, the agent service needs to be restarted after the proxy environment variable is set.
    Restart-Service -Name himds
    ```

    >[!NOTE]
    >이 미리 보기에서는 에이전트에서 프록시 인증 설정을 지원하지 않습니다.
    >

3. 에이전트를 설치한 후 다음 명령을 실행하여 Azure Arc 서비스와 통신하도록 구성해야 합니다.

    ```dos
    "%ProgramFiles%\AzureConnectedMachineAgent\azcmagent.exe" connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID"
    ```

### <a name="install-with-the-scripted-method"></a>스크립팅된 메서드를 사용하여 설치

1. 서버에 로그인합니다.

1. 관리자 권한 PowerShell 명령 프롬프트를 엽니다.

    >[!NOTE]
    >이 스크립트는 64 비트 버전의 Windows PowerShell에서 실행 되는 것만 지원 합니다.
    >

1. 스크립트를 복사한 폴더 또는 공유로 변경하고, `./OnboardingScript.ps1` 스크립트를 실행하여 서버에서 실행합니다.

설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. 로그 디렉터리는 *%ProgramData%\AzureConnectedMachineAgent\log* 입니다.

## <a name="install-and-validate-the-agent-on-linux"></a>Linux에서 에이전트 설치 및 유효성 검사

Linux용 Connected Machine 에이전트는 Microsoft [패키지 리포지토리](https://packages.microsoft.com/)에서 호스팅되는 배포(.RPM 또는 .DEB)에 대한 기본 설정 패키지 형식으로 제공됩니다. [`Install_linux_azcmagent.sh` 셸 스크립트 번들](https://aka.ms/azcmagent)에서 수행하는 작업은 다음과 같습니다.

* packages.microsoft.com에서 에이전트 패키지를 다운로드하도록 호스트 머신을 구성합니다.

* 하이브리드 리소스 공급자 패키지를 설치합니다.

필요에 따라 `--proxy "{proxy-url}:{proxy-port}"` 매개 변수를 포함하여 에이전트를 프록시 정보로 구성할 수 있습니다.

또한 이 스크립트에는 지원되는 배포와 지원되지 않는 배포를 식별하는 논리가 포함되어 있으며 설치를 수행하는 데 필요한 권한을 확인합니다.

다음 예제에서는 에이전트를 다운로드하여 설치합니다.

```bash
# Download the installation package.
wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

# Install the connected machine agent.
bash ~/Install_linux_azcmagent.sh
```

1. 프록시 서버를 통해 통신하도록 에이전트를 구성하는 `--proxy` 매개 변수를 포함하여 에이전트를 다운로드하고 설치하려면 다음 명령을 실행합니다.

    ```bash
    # Download the installation package.
    wget https://aka.ms/azcmagent -O ~/Install_linux_azcmagent.sh

    # Install the connected machine agent.
    bash ~/Install_linux_azcmagent.sh --proxy "{proxy-url}:{proxy-port}"
    ```

2. 에이전트를 설치한 후 다음 명령을 실행하여 Azure Arc 서비스와 통신하도록 구성해야 합니다.

    ```bash
    azcmagent connect --resource-group "resourceGroupName" --tenant-id "tenantID" --location "regionName" --subscription-id "subscriptionID" --cloud "cloudName"
    if [ $? = 0 ]; then echo "\033[33mTo view your onboarded server(s), navigate to https://portal.azure.com/#blade/HubsExtension/BrowseResource/resourceType/Microsoft.HybridCompute%2Fmachines\033[m"; fi
    ```

### <a name="install-with-the-scripted-method"></a>스크립팅된 메서드를 사용하여 설치

1. 루트 액세스 권한이 있는 계정으로 서버에 로그인 합니다.

1. 스크립트를 복사한 폴더 또는 공유로 변경하고, `./OnboardingScript.sh` 스크립트를 실행하여 서버에서 실행합니다.

설치가 완료된 후 에이전트가 시작되지 않으면 자세한 오류 정보를 로그에서 확인합니다. 로그 디렉터리는 *var/opt/azcmagent/log* 입니다.

## <a name="verify-the-connection-with-azure-arc"></a>Azure Arc 연결 확인

에이전트가 설치되고 Azure Arc 사용 서버에 연결하도록 구성되면 Azure Portal로 이동하여 서버가 성공적으로 연결되었는지 확인합니다. [Azure Portal](https://aka.ms/hybridmachineportal)에서 머신을 확인합니다.

![성공적인 서버 연결](./media/onboard-portal/arc-for-servers-successful-onboard.png)

## <a name="next-steps"></a>다음 단계

* 문제 해결 정보는 [연결 된 컴퓨터 에이전트 문제 해결 가이드](troubleshoot-agent-onboard.md)에서 찾을 수 있습니다.

* [Azure Policy](../../governance/policy/overview.md)를 사용하여 머신을 관리하는 방법을 알아봅니다(예: VM [게스트 구성](../../governance/policy/concepts/guest-configuration.md), 머신이 예상되는 Log Analytics 작업 영역에 보고되는지 확인, [VM을 사용한 Azure Monitor](../../azure-monitor/insights/vminsights-enable-policy.md)로 모니터링 등).

* [Log Analytics 에이전트](../../azure-monitor/platform/log-analytics-agent.md)에 대해 자세히 알아보세요. Windows 및 Linux 용 Log Analytics 에이전트는 운영 체제 및 워크 로드 모니터링 데이터를 수집 하거나, 자동화 runbook 또는 업데이트 관리 같은 기능을 사용 하 여 관리 하거나, [Azure Security Center](../../security-center/security-center-introduction.md)같은 다른 Azure 서비스를 사용 하려는 경우에 필요 합니다.