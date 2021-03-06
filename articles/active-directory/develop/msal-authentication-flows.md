---
title: MSAL 인증 흐름 | Azure
titleSuffix: Microsoft identity platform
description: MSAL(Microsoft 인증 라이브러리)에서 사용하는 인증 흐름 및 권한 부여에 대해 알아봅니다.
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/08/2020
ms.author: marsma
ms.reviewer: saeeda
ms.openlocfilehash: 4a902ed53e92cd073d81626e80bdb3c8629ad072
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437873"
---
# <a name="authentication-flows"></a>인증 흐름

MSAL (Microsoft 인증 라이브러리)은 여러 응용 프로그램 시나리오에서 사용할 수 있는 여러 인증 흐름을 지원 합니다.

| 흐름 | Description | 사용 대상 |
|--|--|--|
| [인증 코드](#authorization-code) | 웹 API와 같은 보호된 리소스에 대한 액세스 권한을 얻기 위해 디바이스에 설치된 앱에서 사용됩니다. 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다. | [데스크톱 앱](scenario-desktop-overview.md), [모바일 앱](scenario-mobile-overview.md), [웹앱](scenario-web-app-call-api-overview.md) |
| [클라이언트 자격 증명](#client-credentials) | 애플리케이션의 ID를 사용하여 웹에 호스트된 리소스에 액세스할 수 있습니다. 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. | [디먼 앱](scenario-daemon-overview.md) |
| [디바이스 코드](#device-code) | 사용자가 스마트 TV, IoT 디바이스 또는 프린터와 같은 입력이 제한된 디바이스에 로그인할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#command-line-tool-without-a-web-browser) |
| [암시적 부여](#implicit-grant) | 앱이 백 엔드 서버 자격 증명 교환을 수행하지 않고 토큰을 가져올 수 있습니다. 응용 프로그램에서 사용자에 게 로그인 하 고, 세션을 유지 관리 하 고, 다른 web Api에 대 한 토큰을 가져올 수 있습니다. | [SPA(단일 페이지 애플리케이션)](scenario-spa-overview.md) |
| [On-behalf-of](#on-behalf-of) | 애플리케이션이 서비스 또는 웹 API를 호출하고 차례로 다른 서비스 또는 웹 API를 호출해야 합니다. 요청 체인을 통해 위임된 사용자 ID 및 사용 권한을 전파하는 개념입니다. | [Web API](scenario-web-api-call-api-overview.md) |
| [사용자 이름/암호](#usernamepassword) | 암호를 직접 처리하여 애플리케이션에 사용자가 로그인할 수 있도록 허용합니다. 이 흐름은 권장되지 않습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#username-and-password) |
| [Windows 통합 인증](#integrated-windows-authentication) | 도메인 또는 Azure AD(Azure Active Directory)에 연결된 컴퓨터의 애플리케이션에서 사용자의 UI 상호 작용 없이 자동으로 토큰을 획득할 수 있습니다. | [데스크톱/모바일 앱](scenario-desktop-acquire-token.md#integrated-windows-authentication) |

## <a name="how-each-flow-emits-tokens-and-codes"></a>각 흐름에서 토큰 및 코드를 내보내는 방법

클라이언트 응용 프로그램을 빌드하는 방법에 따라 Microsoft id 플랫폼에서 지원 되는 인증 흐름을 하나 이상 사용할 수 있습니다. 이러한 흐름은 여러 유형의 토큰 및 인증 코드를 생성할 수 있으며, 다른 토큰을 사용 하 여 작업을 수행 해야 합니다.

| 흐름                                                                               | 필수 사항            | id_token | 액세스 토큰 | 새로 고침 토큰 | 인증 코드 |
|------------------------------------------------------------------------------------|:-------------------:|:--------:|:------------:|:-------------:|:------------------:|
| [인증 코드 흐름](v2-oauth2-auth-code-flow.md)                             |                     | x        | x            | x             | x                  |
| [클라이언트 자격 증명](v2-oauth2-client-creds-grant-flow.md)                         |                     |          | x(앱 전용) |               |                    |
| [디바이스 코드 흐름](v2-oauth2-device-code.md)                                       |                     | x        | x            | x             |                    |
| [암시적 흐름](v2-oauth2-implicit-grant-flow.md)                                  |                     | x        | x            |               |                    |
| [On-Behalf-Of 흐름](v2-oauth2-on-behalf-of-flow.md)                                | 액세스 토큰        | x        | x            | x             |                    |
| [사용자 이름/암호](v2-oauth-ropc.md) (ropc)                                       | 사용자 이름 & 암호 | x        | x            | x             |                    |
| [하이브리드 OIDC 흐름](v2-protocols-oidc.md#protocol-diagram-access-token-acquisition) |                     | x        |              |               | x                  |
| [새로 고침 토큰 상환](v2-oauth2-auth-code-flow.md#refresh-the-access-token)   | 새로 고침 토큰       | x        | x            | x             |                    |

### <a name="interactive-and-non-interactive-authentication"></a>대화형 및 비 대화형 인증

이러한 흐름 중 몇 가지는 대화형 토큰 획득 및 비 대화형 토큰 획득을 모두 지원 합니다.

  - **대화형** 은 사용자에 게 입력을 요청 하는 메시지를 표시할 수 있음을 의미 합니다. 예를 들어 사용자에 게 로그인 하 라는 메시지를 표시 하거나 MFA (multi-factor authentication)를 수행 하거나 리소스에 추가 동의를 부여 합니다.
  - **비 대화형**또는 *자동*인증은 로그인 서버에서 사용자에 게 추가 정보를 확인할 *수 없는* 방식으로 토큰을 획득 하려고 시도 합니다.

MSAL 기반 응용 프로그램은 먼저 토큰을 *자동*으로 획득 한 다음 비 대화형 메서드가 실패 하는 경우에만 대화형으로 시도 해야 합니다. 이 패턴에 대 한 자세한 내용은 [MSAL (Microsoft 인증 라이브러리)을 사용 하 여 토큰 가져오기 및 캐시](msal-acquire-cache-tokens.md)를 참조 하세요.

## <a name="authorization-code"></a>인증 코드

[OAuth 2 인증 코드 부여](v2-oauth2-auth-code-flow.md) 는 웹 api와 같은 보호 된 리소스에 대 한 액세스 권한을 얻기 위해 장치에 설치 된 앱에서 사용할 수 있습니다. 이를 통해 모바일 및 데스크톱 앱에 로그인 및 API 액세스를 추가할 수 있습니다.

사용자가 웹 애플리케이션(웹 사이트)에 로그인하면 웹 애플리케이션은 인증 코드를 수신합니다. 인증 코드는 웹 API를 호출하는 토큰을 얻는 데 사용됩니다.

![인증 코드 흐름 다이어그램](media/msal-authentication-flows/authorization-code.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 액세스 토큰에 대해 사용되는 인증 코드를 요청합니다.
2. 액세스 토큰을 사용하여 웹 API를 호출합니다.

### <a name="considerations"></a>고려 사항

- 토큰을 사용하는 데 인증 코드는 한 번만 사용할 수 있습니다. 프로토콜 표준 사양에 의해 명시적으로 금지 되기 때문에 동일한 인증 코드를 사용 하 여 토큰을 여러 번 얻으려고 시도 하지 마세요. 의도적으로 또는 프레임 워크에서 사용자에 게이를 수행 하는 것을 인식 하지 못하기 때문에 코드를 여러 번 사용할 경우 다음 오류가 발생 합니다.

    `AADSTS70002: Error validating credentials. AADSTS54005: OAuth2 Authorization code was already redeemed, please retry with a new valid code or use an existing refresh token.`

## <a name="client-credentials"></a>클라이언트 자격 증명

[OAuth 2 클라이언트 자격 증명 흐름](v2-oauth2-client-creds-grant-flow.md) 을 사용 하면 응용 프로그램의 id를 사용 하 여 웹 호스팅 리소스에 액세스할 수 있습니다. 이 유형의 권한 부여는 일반적으로 사용자의 직접적인 상호 작용 없이 백그라운드에서 실행해야 하는 서버 간 상호 작용에 사용됩니다. 이러한 유형의 애플리케이션은 종종 디먼 또는 서비스 계정이라고 합니다.

클라이언트 자격 증명 부여 흐름은 사용자를 가장하는 대신 다른 웹 서비스를 호출할 때 웹 서비스(기밀 클라이언트)가 자체 자격 증명을 사용하여 인증하도록 허용합니다. 이 시나리오에서 클라이언트는 일반적으로 중간 계층 웹 서비스, 데몬 서비스 또는 웹 사이트입니다. 더 높은 수준의 보증을 위해 Microsoft ID 플랫폼은 호출 서비스가 자격 증명으로 인증서(공유 비밀 대신)를 사용할 수 있도록 합니다.

> [!NOTE]
> 공용 클라이언트 응용 프로그램만 지원 하므로 UWP, Xamarin.ios 및 Xamarin.ios와 같은 모바일 플랫폼에서는 기밀 클라이언트 흐름을 사용할 수 없습니다. 공용 클라이언트 응용 프로그램은 id 공급자에 대 한 응용 프로그램 id를 증명 하는 방법을 모릅니다. 웹 앱 또는 web API 백 엔드에서 인증서를 배포 하 여 보안 연결을 구현할 수 있습니다.

### <a name="application-secrets"></a>애플리케이션 비밀

![암호를 사용하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-password.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 애플리케이션 비밀 또는 암호 자격 증명을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

### <a name="certificates"></a>인증서

![인증서를 사용하는 기밀 클라이언트 다이어그램](media/msal-authentication-flows/confidential-client-certificate.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 인증서 자격 증명을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

이러한 클라이언트 자격 증명은 다음과 같아야 합니다.

- Azure AD에 등록했습니다.
- 코드에서 기밀 클라이언트 응용 프로그램 개체를 생성할 때 전달 됩니다.

## <a name="device-code"></a>디바이스 코드

[OAuth 2 장치 코드 흐름](v2-oauth2-device-code.md) 을 사용 하면 사용자가 스마트 Tv, IoT 장치 및 프린터와 같은 입력 제한 장치에 로그인 할 수 있습니다. Azure AD를 사용한 대화형 인증에는 웹 브라우저가 필요합니다. 장치 또는 운영 체제에서 웹 브라우저를 제공 하지 않는 경우 장치 코드 흐름을 통해 사용자는 컴퓨터 또는 휴대폰 같은 다른 장치를 사용 하 여 대화형으로 로그인 할 수 있습니다.

응용 프로그램은 장치 코드 흐름을 사용 하 여 이러한 장치 및 운영 체제용으로 설계 된 2 단계 프로세스를 통해 토큰을 가져옵니다. 이러한 응용 프로그램의 예로는 IoT 장치에서 실행 되는 응용 프로그램 및 CLI (명령줄 인터페이스) 도구를 들 수가 있습니다.

![디바이스 코드 흐름 다이어그램](media/msal-authentication-flows/device-code.png)

위의 다이어그램에서:

1. 사용자 인증이 필요한 경우 앱은 코드를 제공 하 고 사용자에 게 인터넷에 연결 된 스마트폰와 같은 다른 장치를 사용 하 여 URL을 방문 하도록 요청 합니다 (예: `https://microsoft.com/devicelogin` ). 그런 다음 사용자에 게 코드를 입력 하 라는 메시지가 표시 되 고, 필요한 경우 승인 프롬프트 및 [multi-factor authentication](../authentication/concept-mfa-howitworks.md)을 비롯 한 일반 인증 환경을 단계별로 진행 합니다.
1. 인증에 성공하면 명령줄 앱은 백 채널을 통해 필요한 토큰을 수신하고 이를 사용하여 필요한 웹 API 호출을 수행합니다.

### <a name="constraints"></a>제약 조건

- 장치 코드 흐름은 공용 클라이언트 응용 프로그램 에서만 사용할 수 있습니다.
- 공용 클라이언트 애플리케이션을 구성할 때 전달되는 인증 기관은 다음 중 하나여야 합니다.
  - 테 넌 트, 여기서은 테 넌 트 `https://login.microsoftonline.com/{tenant}/,` `{tenant}` ID 또는 테 넌 트와 연결 된 도메인 이름을 나타내는 GUID입니다.
  - 회사 및 학교 계정 (양식) `https://login.microsoftonline.com/organizations/` .

## <a name="implicit-grant"></a>암시적 부여

[OAuth 2 암시적 권한 부여](v2-oauth2-implicit-grant-flow.md) 흐름을 사용 하면 앱이 백 엔드 서버 자격 증명 교환을 수행 하지 않고도 Microsoft id 플랫폼에서 토큰을 가져올 수 있습니다. 이 흐름을 사용 하면 앱에서 사용자에 로그인 하 고, 세션을 유지 관리 하 고, 다른 web Api에 대 한 토큰을 클라이언트 JavaScript 코드 내에서 모두 가져올 수 있습니다.

![암시적 허용 흐름 다이어그램](media/msal-authentication-flows/implicit-grant.svg)

많은 최신 웹 응용 프로그램은 JavaScript로 작성 된 클라이언트 쪽의 SPA (단일 페이지 응용 프로그램) 또는 각도, Vue.js, React.js 등의 SPA 프레임 워크로 빌드됩니다. 이러한 애플리케이션은 웹 브라우저에서 실행되며 기존 서버 쪽 웹 애플리케이션과 다른 인증 특징이 있습니다. Microsoft ID 플랫폼을 사용하면 암시적 권한 부여 흐름을 사용하여 단일 페이지 애플리케이션에서 사용자를 로그인하고 토큰을 가져와서 백 엔드 서비스 또는 웹 API에 액세스할 수 있습니다. 암시적 흐름을 사용하면 애플리케이션에서 ID 토큰을 가져와 인증된 사용자를 나타낼 수 있으며, 보호된 API를 호출하는 데 필요한 토큰에도 액세스할 수 있습니다.

이 인증 흐름에는 기본 플랫폼과의 상호 작용을 위한 추가 기능이 필요 하기 때문에 전자 또는 React-Native 같은 플랫폼 간 JavaScript 프레임 워크를 사용 하는 응용 프로그램 시나리오가 포함 되지 않습니다.

암시적 흐름 모드를 통해 발급 된 토큰은 URL로 브라우저에 반환 되므로 **길이 제한이** 있습니다 `response_mode` . 여기서는 `query` 또는입니다 `fragment` . 일부 브라우저는 브라우저 모음에서 URL의 길이를 제한 하 고 너무 길면 실패 합니다. 따라서 이러한 암시적 흐름 토큰은 또는 클레임을 포함 하지 않습니다 `groups` `wids` .

## <a name="on-behalf-of"></a>On-behalf-of

[OAuth 2 just-in-time 인증 흐름](v2-oauth2-on-behalf-of-flow.md) 흐름은 응용 프로그램이 다른 서비스 또는 web api를 호출 해야 하는 서비스 또는 웹 api를 호출할 때 사용 됩니다. 이 개념은 요청 체인을 통해 위임된 사용자 ID 및 권한을 전파하는 것입니다. 중간 계층 서비스에서 다운스트림 서비스로 인증 된 요청을 수행 하려면 사용자를 *대신* 하 여 Microsoft id 플랫폼에서 액세스 토큰을 보호 해야 합니다.

![On-behalf-of 흐름 다이어그램](media/msal-authentication-flows/on-behalf-of.png)

위의 다이어그램에서:

1. 애플리케이션은 웹 API에 대한 액세스 토큰을 획득합니다.
2. 클라이언트(웹, 데스크톱, 모바일 또는 단일 페이지 애플리케이션)는 HTTP 요청의 인증 헤더에 액세스 토큰을 전달자 토큰으로 추가하여 보호된 웹 API를 호출합니다. 웹 API는 사용자를 인증합니다.
3. 클라이언트에서 웹 API를 호출하면 웹 API는 사용자를 대신해 다른 토큰을 요청합니다.
4. 보호된 웹 API는 이 토큰을 사용하여 사용자 대신 다운스트림 웹 API를 호출합니다. 웹 API는 나중에 다른 다운스트림 API에 대한 토큰을 요청할 수도 있습니다(계속해서 같은 사용자를 대신함).

## <a name="usernamepassword"></a>사용자 이름/암호

OAuth 2 ropc ( [리소스 소유자 암호 자격 증명](v2-oauth-ropc.md) ) 부여를 통해 응용 프로그램은 자신의 암호를 직접 처리 하 여 사용자에 게 로그인 할 수 있습니다. 데스크톱 애플리케이션에서 사용자 이름/암호 흐름을 사용하여 토큰을 자동으로 가져올 수 있습니다. 애플리케이션을 사용하는 경우 UI가 필요하지 않습니다.

![사용자 이름/암호 흐름 다이어그램](media/msal-authentication-flows/username-password.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. 사용자 이름 및 암호를 ID 공급자에게 보내 토큰을 가져옵니다.
2. 토큰을 사용하여 웹 API를 호출합니다.

> [!WARNING]
> 이 흐름은 권장되지 않습니다. 높은 수준의 신뢰 및 자격 증명 노출이 필요 합니다. 이 흐름은 더 안전한 흐름을 더 이상 사용할 수 없는 경우에 *만* 사용 해야 합니다. 자세한 내용은 [암호 문제 증가에 대한 해결책](https://news.microsoft.com/features/whats-solution-growing-problem-passwords-says-microsoft/)을 참조하세요.

Windows 도메인에 가입된 컴퓨터에서 자동으로 토큰을 획득하는 기본 흐름은 [Windows 통합 인증](#integrated-windows-authentication)입니다. 다른 경우에는 [장치 코드 흐름](#device-code)을 사용 합니다.

DevOps와 같은 일부 시나리오에서는 사용자 이름/암호 흐름이 유용할 수 있지만 사용자 고유의 UI를 제공 하는 대화형 시나리오에서 사용자 이름/암호를 사용 하려는 경우에는이 방법을 사용 하지 마세요.

사용자 이름/암호를 사용하여 다음을 수행할 수 없습니다.

- Multi-factor authentication을 수행 해야 하는 사용자는 상호 작용이 없으므로 로그인 할 수 없습니다.
- 사용자는 Single Sign-On을 수행할 수 없습니다.

### <a name="constraints"></a>제약 조건

[Windows 통합 인증 제약 조건](#integrated-windows-authentication) 외에도 다음과 같은 제약 조건이 적용됩니다.

- 사용자 이름/암호 흐름은 조건부 액세스 및 다단계 인증과 호환되지 않습니다. 따라서 테넌트 관리자가 다단계 인증을 필요로 하는 Azure AD 테넌트에서 앱이 실행되는 경우에는 이 흐름을 사용할 수 없습니다. 이 흐름을 사용할 수 없습니다.
- ROPC는 회사 및 학교 계정에 대해서만 작동 합니다. Microsoft 계정 (MSA)에는 ROPC를 사용할 수 없습니다.
- 이 흐름은 .NET 데스크톱 및 .NET Core에서 사용할 수 있지만 유니버설 Windows 플랫폼에서는 사용할 수 없습니다.
- Azure AD B2C에서 ROPC 흐름은 로컬 계정에 대해서만 작동 합니다. MSAL.NET 및 Azure AD B2C의 ROPC에 대 한 자세한 내용은 [Azure AD B2C에서 Ropc 사용](msal-net-aad-b2c-considerations.md#resource-owner-password-credentials-ropc)을 참조 하세요.

## <a name="integrated-windows-authentication"></a>Windows 통합 인증

MSAL은 도메인에 가입 되거나 Azure AD에 가입 된 Windows 컴퓨터에서 실행 되는 데스크톱 및 모바일 응용 프로그램에 대 한 IWA (Windows 통합 인증)를 지원 합니다. 이러한 응용 프로그램은 IWA를 사용 하 여 사용자의 UI 상호 작용 없이도 토큰을 자동으로 가져올 수 있습니다.

![Windows 통합 인증 다이어그램](media/msal-authentication-flows/integrated-windows-authentication.png)

위의 다이어그램에서 애플리케이션은 다음을 수행합니다.

1. Windows 통합 인증을 사용하여 토큰을 가져옵니다.
2. 토큰을 사용하여 리소스에 대한 요청을 수행합니다.

### <a name="constraints"></a>제약 조건

IWA (Windows 통합 인증)는 Active Directory에서 만들어지고 Azure AD에서 지 원하는 사용자 *만* 페더레이션 사용자를 지원 합니다. Active Directory 지원 하지 않고 Azure AD에서 직접 만든 사용자 (관리 되는 사용자)는이 인증 흐름을 사용할 수 없습니다. 이러한 제한은 [사용자 이름/암호 흐름](#usernamepassword)에 영향을 주지 않습니다.

IWA는 .NET Framework, .NET Core 및 유니버설 Windows 플랫폼 응용 프로그램에 대 한 것입니다.

IWA는 다단계 인증을 바이패스하지 않습니다. 다단계 인증이 구성된 경우 다단계 인증 질문이 필요하면 IWA가 실패할 수 있습니다. 다단계 인증을 사용하려면 사용자 조작이 필요합니다.

사용자는 ID 공급자가 언제 2단계 인증을 수행하도록 요청하는지 제어하지 않습니다. 테넌트 관리자가 제어합니다. 일반적으로 다른 국가/지역에서 로그인할 때, VPN을 통해 회사 네트워크에 연결되어 있지 않은 경우 2단계 인증이 필요하며 VPN을 통해 연결된 경우에도 간혹 필요한 경우가 있습니다. Azure AD는 AI를 사용하여 2단계 인증이 필요한 경우를 계속 학습합니다. IWA가 실패 하면 [대화형 사용자 프롬프트로](#interactive-and-non-interactive-authentication)대체 해야 합니다.

공용 클라이언트 응용 프로그램을 생성할 때 전달 된 기관은 다음 중 하나 여야 합니다.

- 테 넌 트, 여기서은 테 넌 트 `https://login.microsoftonline.com/{tenant}/,` `{tenant}` ID 또는 테 넌 트와 연결 된 도메인 이름을 나타내는 GUID입니다.
- 회사 및 학교 계정(`https://login.microsoftonline.com/organizations/`) Microsoft 개인 계정 (MSA)은 지원 되지 않습니다. `/common` 또는 테 넌 트를 사용할 수 없습니다 `/consumers` .

IWA는 자동 흐름이므로 다음 중 하나를 충족해야 합니다.

- 애플리케이션의 사용자가 사전에 애플리케이션 사용에 동의해야 합니다.
- 테넌트 관리자는 테넌트의 모든 사용자에게 사전에 애플리케이션 사용 동의를 받아야 합니다.

이는 다음 중 하나에 해당함을 의미합니다.

- 개발자는 자신에 대 한 Azure Portal **권한 부여** 를 선택 했습니다.
- 테 넌 트 관리자가 Azure Portal에서 앱 등록의 **API 사용 권한** 탭에서 **{테 넌 트 도메인}에 대 한 Grant/revoke 관리자 동의** 를 선택 했습니다 ( [웹 API에 액세스할 수 있는 권한 추가](quickstart-configure-app-access-web-apis.md#add-permissions-to-access-your-web-api)참조).
- 사용자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다. [개별 사용자 동의 요청](v2-permissions-and-consent.md#requesting-individual-user-consent)을 참조 하세요.
- 테 넌 트 관리자가 응용 프로그램에 동의할 수 있는 방법을 제공 했습니다. [관리자 동의](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)를 참조 하세요.

IWA 흐름이 .NET 데스크톱, .NET Core 및 Windows 유니버설 플랫폼 앱에 대해 사용하도록 설정됩니다. .Net Core는 운영 체제에서 사용자 이름을 가져올 수 없기 때문에 IWA에 대한 사용자 이름을 제공해야 합니다.

동의에 대한 자세한 내용은 [v2.0 권한 및 동의](v2-permissions-and-consent.md)를 참조하세요.

## <a name="next-steps"></a>다음 단계

이제 MSAL (Microsoft 인증 라이브러리)에서 지원 되는 인증 흐름을 검토 했으므로 이러한 흐름에서 사용 되는 토큰을 가져오고 캐싱하는 방법에 대해 알아봅니다.

[MSAL (Microsoft 인증 라이브러리)을 사용 하 여 토큰 가져오기 및 캐시](msal-acquire-cache-tokens.md)
