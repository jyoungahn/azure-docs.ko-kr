---
title: '빠른 시작: Azure REST API 및 C#으로 이미지에서 얼굴 감지'
titleSuffix: Azure Cognitive Services
description: 이 빠른 시작에서는 이미지에서 얼굴을 감지하기 위해 C#과 함께 Azure Face REST API를 사용합니다.
services: cognitive-services
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: face-api
ms.topic: quickstart
ms.date: 11/23/2020
ms.author: pafarley
ms.openlocfilehash: f15fbc340aafa325f772a1fd50a2129fa5044c47
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: HT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "96020877"
---
# <a name="quickstart-detect-faces-in-an-image-using-the-face-rest-api-and-c"></a>빠른 시작: Face REST API 및 C#을 사용하여 이미지에서 얼굴 감지

이 빠른 시작에서는 이미지에서 사람 얼굴을 감지하기 위해 C#과 함께 Azure Face REST API를 사용합니다.

Azure 구독이 아직 없는 경우 시작하기 전에 [체험 계정](https://azure.microsoft.com/free/cognitive-services/)을 만듭니다.

## <a name="prerequisites"></a>사전 요구 사항

* Azure 구독 - [체험 구독 만들기](https://azure.microsoft.com/free/cognitive-services/)
* Azure 구독을 보유한 후에는 Azure Portal에서 <a href="https://portal.azure.com/#create/Microsoft.CognitiveServicesFace"  title="Face 리소스 만들기"  target="_blank">Face 리소스 <span class="docon docon-navigate-external x-hidden-focus"></span></a>를 만들어 키와 엔드포인트를 가져옵니다. 배포 후 **리소스로 이동** 을 클릭합니다.
    * 애플리케이션을 Face API에 연결하려면 만든 리소스의 키와 엔드포인트가 필요합니다. 이 빠른 시작의 뒷부분에 나오는 코드에 키와 엔드포인트를 붙여넣습니다.
    * 평가판 가격 책정 계층(`F0`)을 통해 서비스를 사용해보고, 나중에 프로덕션용 유료 계층으로 업그레이드할 수 있습니다.
- [Visual Studio](https://www.visualstudio.com/downloads/)의 모든 버전.

## <a name="create-the-visual-studio-project"></a>Visual Studio 프로젝트 만들기

1. Visual Studio에서 새 **콘솔 앱(.NET Framework)** 프로젝트를 만들고 **FaceDetection** 으로 이름을 지정합니다.
1. 솔루션에 다른 프로젝트가 있는 경우 이것을 단일 시작 프로젝트로 선택합니다.

## <a name="add-face-detection-code"></a>얼굴 감지 코드 추가

새 프로젝트의 *Program.cs* 파일을 엽니다. 여기에 이미지를 로드하고 얼굴을 감지하는 데 필요한 코드를 추가합니다.

### <a name="include-namespaces"></a>네임스페이스 포함

*Program.cs* 파일 위에 다음 `using` 문을 추가합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="dependencies":::

### <a name="add-essential-fields"></a>필수 필드 추가

다음 필드가 포함된 **Program** 클래스를 추가합니다. 이 데이터는 Face 서비스에 연결하는 방법과 입력 데이터를 가져올 위치를 지정합니다. `subscriptionKey` 필드를 구독 키의 값으로 업데이트해야 하며, 해당 리소스 엔드포인트 문자열이 포함되도록 `uriBase` 문자열을 변경해야 할 수도 있습니다.

[!INCLUDE [subdomains-note](../../../../includes/cognitive-services-custom-subdomains-note.md)]

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="environment":::

### <a name="receive-image-input"></a>이미지 입력 수신

**Program** 클래스의 **Main** 메서드에 다음 코드를 추가합니다. 이 코드는 사용자에게 로컬 이미지 파일 경로를 입력하라는 메시지를 콘솔에 표시합니다. 그런 다음, 다른 메서드인 **MakeAnalysisRequest** 를 호출하여 해당 위치에서 이미지를 처리합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="main":::

### <a name="call-the-face-detection-rest-api"></a>얼굴 감지 REST API 호출

**Program** 클래스에 다음 메서드를 추가합니다. Face API에 대한 REST 호출을 구성하여 원격 이미지의 얼굴 정보를 감지합니다(검색할 얼굴 특성을 지정하는 `requestParameters` 문자열). 그런 다음, 출력 데이터를 JSON 문자열에 기록합니다.

다음 단계에서는 도우미 메서드를 정의합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="request":::

### <a name="process-the-input-image-data"></a>입력 이미지 데이터 처리

**Program** 클래스에 다음 메서드를 추가합니다. 이 메서드는 지정된 파일 경로의 이미지를 바이트 배열로 변환합니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="getimage":::

### <a name="parse-the-json-response"></a>JSON 응답 구문 분석

**Program** 클래스에 다음 메서드를 추가합니다. 이 메서드는 JSON 입력을 보다 쉽게 읽을 수 있도록 형식화합니다. 앱이 이 문자열 데이터를 콘솔에 씁니다. 그런 다음, 클래스와 네임스페이스를 닫습니다.

:::code language="csharp" source="~/cognitive-services-quickstart-code/dotnet/Face/rest/detect.cs" id="print":::

## <a name="run-the-app"></a>앱 실행

성공적인 응답은 얼굴 데이터를 쉽게 읽을 수 있는 JSON 형식으로 표시합니다. 예를 들면 다음과 같습니다.

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      }
   }
]
```

## <a name="extract-face-attributes"></a>얼굴 특성 추출
 
얼굴 특성을 추출하려면 검색 모델 1을 사용하고 `returnFaceAttributes` 쿼리 매개 변수를 추가합니다.

```csharp
string requestParameters = "detectionModel=detection_01&returnFaceId=true&returnFaceLandmarks=false&returnFaceAttributes=age,gender,headPose,smile,facialHair,glasses,emotion,hair,makeup,occlusion,accessories,blur,exposure,noise";
```

이제 응답에 얼굴 특성이 포함됩니다. 예를 들면 다음과 같습니다.

```json
[
   {
      "faceId": "f7eda569-4603-44b4-8add-cd73c6dec644",
      "faceRectangle": {
         "top": 131,
         "left": 177,
         "width": 162,
         "height": 162
      },
      "faceAttributes": {
         "smile": 0.0,
         "headPose": {
            "pitch": 0.0,
            "roll": 0.1,
            "yaw": -32.9
         },
         "gender": "female",
         "age": 22.9,
         "facialHair": {
            "moustache": 0.0,
            "beard": 0.0,
            "sideburns": 0.0
         },
         "glasses": "NoGlasses",
         "emotion": {
            "anger": 0.0,
            "contempt": 0.0,
            "disgust": 0.0,
            "fear": 0.0,
            "happiness": 0.0,
            "neutral": 0.986,
            "sadness": 0.009,
            "surprise": 0.005
         },
         "blur": {
            "blurLevel": "low",
            "value": 0.06
         },
         "exposure": {
            "exposureLevel": "goodExposure",
            "value": 0.67
         },
         "noise": {
            "noiseLevel": "low",
            "value": 0.0
         },
         "makeup": {
            "eyeMakeup": true,
            "lipMakeup": true
         },
         "accessories": [

         ],
         "occlusion": {
            "foreheadOccluded": false,
            "eyeOccluded": false,
            "mouthOccluded": false
         },
         "hair": {
            "bald": 0.0,
            "invisible": false,
            "hairColor": [
               {
                  "color": "brown",
                  "confidence": 1.0
               },
               {
                  "color": "black",
                  "confidence": 0.87
               },
               {
                  "color": "other",
                  "confidence": 0.51
               },
               {
                  "color": "blond",
                  "confidence": 0.08
               },
               {
                  "color": "red",
                  "confidence": 0.08
               },
               {
                  "color": "gray",
                  "confidence": 0.02
               }
            ]
         }
      }
   }
]
```

## <a name="next-steps"></a>다음 단계

이 빠른 시작에서는 Azure Face 서비스와 함께 REST 호출을 사용하여 이미지의 얼굴을 감지하고 해당 특성을 반환하는 간단한 .NET 콘솔 애플리케이션을 만들었습니다. 다음으로, Face API 참조 설명서를 살펴보고 지원되는 시나리오에 대해 자세히 알아보세요.

> [!div class="nextstepaction"]
> [Face API](https://westus.dev.cognitive.microsoft.com/docs/services/563879b61984550e40cbbe8d/operations/563879b61984550f30395236)
