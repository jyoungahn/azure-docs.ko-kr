---
title: 동적 Azure Maps StylesObject 스키마 참조 가이드
description: 동적 Azure Maps StylesObject 스키마 및 구문에 대 한 참조 가이드입니다.
author: anastasia-ms
ms.author: v-stharr
ms.date: 11/20/2020
ms.topic: reference
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: f6bc4c62febf24dee790ac6136b1661426d4d619
ms.sourcegitcommit: c95e2d89a5a3cf5e2983ffcc206f056a7992df7d
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95536951"
---
# <a name="stylesobject-schema-reference-guide-for-dynamic-maps"></a>동적 맵에 대 한 StylesObject 스키마 참조 가이드

 는 `StylesObject` `StyleObject` stateset 스타일을 나타내는 배열입니다. Azure Maps Creator [기능 상태 서비스](/rest/api/maps/featurestate) 를 사용 하 여 stateset 스타일을 실내 지도 데이터 기능에 적용 합니다. Stateset 스타일을 만들고 실내 지도 기능과 연결한 후에는이 스타일을 사용 하 여 동적 실내 지도를 만들 수 있습니다. 동적 실내 지도를 만드는 방법에 대 한 자세한 내용은 [Creator 실내 맵에 대 한 동적 스타일 지정 구현](indoor-map-dynamic-styling.md)을 참조 하세요.

## <a name="styleobject"></a>StyleObject

는 `StyleObject` 다음 스타일 규칙 중 하나입니다.

 * [`BooleanTypeStyleRule`](#booleantypestylerule)
 * [`NumericTypeStyleRule`](#numerictypestylerule)
 * [`StringTypeStyleRule`](#stringtypestylerule)

아래 JSON은 세 가지 스타일 형식의 각각을 사용 하는 예제를 보여 줍니다.  는 `BooleanTypeStyleRule` 속성이 true 및 false 인 기능의 동적 스타일을 결정 하는 데 사용 됩니다 `occupied` .  는 `NumericTypeStyleRule` 속성이 특정 범위 내에 속하는 기능의 스타일을 결정 하는 데 사용 됩니다 `temperature` . 마지막으로,를 사용 하 여 `StringTypeStyleRule` 특정 스타일을와 일치 시킵니다 `meetingType` .



```json
 "styles": [
     {
        "keyname": "occupied",
        "type": "boolean",
        "rules": [
            {
                "true": "#FF0000",
                "false": "#00FF00"
            }
        ]
    },
    {
        "keyname": "temperature",
        "type": "number",
        "rules": [
             {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "maximum": 70,
                "exclusiveMinimum": 30
              },
              "color": "#eba834"
            }
        ]
    },
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }
]
```

## <a name="numerictypestylerule"></a>NumericTypeStyleRule

 는 이며 `NumericTypeStyleRule`  [`StyleObject`](#styleobject) 다음과 같은 속성으로 구성 됩니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `keyName` | 문자열 | *상태* 또는 동적 속성 이름입니다. 는 `keyName` 배열 내에서 고유 해야 합니다 `StyleObject` .| 예 |
| `type` | 문자열 | 값은 "numeric"입니다. | 예 |
| `rules` | [`NumberRuleObject`](#numberruleobject)[]| 연결 된 색을 사용 하는 숫자 스타일 범위의 배열입니다. 각 범위는 *상태* 값이 범위를 만족 하는 경우 사용 되는 색을 정의 합니다.| 예 |

### <a name="numberruleobject"></a>NumberRuleObject

는 `NumberRuleObject` [`RangeObject`](#rangeobject) 및 속성으로 구성 `color` 됩니다. *상태* 값이 범위에 속하는 경우 표시의 색은 속성에 지정 된 색이 됩니다 `color` .

겹치는 범위를 여러 개 정의 하는 경우 선택한 색은 충족 되는 첫 번째 범위에서 정의 된 색이 됩니다.

다음 JSON 샘플에서는 *상태* 값이 50-60 사이인 경우 두 범위 모두 true가 됩니다. 그러나 사용 되는 색은 `#343deb` 충족 된 목록의 첫 번째 범위 이기 때문입니다.

```json

    {
        "rules":[
            {
                "range": {
                "minimum": 50,
                "exclusiveMaximum": 70
                },
                "color": "#343deb"
            },
            {
                "range": {
                "minimum": 50,
                "maximum": 60
                },
                "color": "#eba834"
            }
        ]
    }
]
```

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `range` | [범위 개체](#rangeobject) | 범위 [개체](#rangeobject) 는 논리 범위 조건 집합을 정의 합니다 .이 경우에는 `true` *상태* 표시 색을 속성에 지정 된 색으로 변경 합니다 `color` . `range`가 지정 되지 않은 경우 속성에 정의 된 색은 `color` 항상 사용 됩니다.   | 예 |
| `color` | 문자열 | 상태 값이 범위를 벗어날 때 사용할 색입니다. `color`속성은 다음 형식 중 하나에 해당 하는 JSON 문자열입니다. <ul><li> HTML 스타일의 16 진수 값 </li><li> RGB ("#ff0", "#ffff00", "rgb (255, 255, 0)")</li><li> RGBA ("rgba (255, 255, 0, 1)")</li><li> HSL ("hsl (100, 50%, 50%")</li><li> HSLA ("hsla (100, 50%, 50%, 1)")</li><li> 노랑, 파랑 등의 미리 정의 된 HTML 색 이름입니다.</li></ul> | 예 |

### <a name="rangeobject"></a>범위 개체

는 `RangeObject` 의 숫자 범위 값을 정의 합니다 [`NumberRuleObject`](#numberruleobject) . *상태* 값이 범위에 포함 되 게 하려면 정의 된 모든 조건이 true 여야 합니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `minimum` | double | X가 ≥ 모든 숫자 x입니다 `minimum` .| 아니요 |
| `maximum` | double | X가 ≤ 인 모든 숫자 x입니다 `maximum` . | 아니요 |
| `exclusiveMinimum` | double | X > 된 모든 숫자 x입니다 `exclusiveMinimum` .| 아니요 |
| `exclusiveMaximum` | double | X < 된 모든 숫자 x입니다 `exclusiveMaximum` .| 아니요 |

### <a name="example-of-numerictypestylerule"></a>NumericTypeStyleRule의 예

다음 JSON은 `NumericTypeStyleRule` 라는 *상태* 를 보여 줍니다 `temperature` . 이 예제에서에는 [`NumberRuleObject`](#numberruleobject) 정의 된 온도 범위 2 개와 연결 된 색 스타일이 포함 됩니다. 온도 범위가 50-69 이면 디스플레이에서 색을 사용 해야 합니다 `#343deb` .  온도 범위가 31-70 이면 디스플레이에서 색을 사용 해야 합니다 `#eba834` .

```json
{
    "keyname": "temperature",
    "type": "number",
    "rules":[
        {
            "range": {
            "minimum": 50,
            "exclusiveMaximum": 70
            },
            "color": "#343deb"
        },
        {
            "range": {
            "maximum": 70,
            "exclusiveMinimum": 30
            },
            "color": "#eba834"
        }
    ]
}
```

## <a name="stringtypestylerule"></a>StringTypeStyleRule

는 이며 `StringTypeStyleRule` [`StyleObject`](#styleobject) 다음과 같은 속성으로 구성 됩니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `keyName` | 문자열 |  *상태* 또는 동적 속성 이름입니다.  는 `keyName` 배열 내에서 고유 해야 합니다  `StyleObject` .| 예 |
| `type` | 문자열 |값은 "string"입니다. | 예 |
| `rules` | [`StringRuleObject`](#stringruleobject)[]| N 개 *상태* 값의 배열입니다.| 예 |

### <a name="stringruleobject"></a>StringRuleObject

는 `StringRuleObject` 기능 속성의 가능한 문자열 값인 최대 N 개의 상태 값으로 구성 됩니다. 기능의 속성 값이 정의 된 상태 값과 일치 하지 않으면 해당 기능에 동적 스타일이 포함 되지 않습니다. 중복 된 상태 값이 지정 된 경우 첫 번째 값이 우선적으로 적용 됩니다.

문자열 값 일치는 대/소문자를 구분 합니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `stateValue1` | 문자열 | 값 문자열이 stateValue1 색입니다. | 예 |
| `stateValue2` | 문자열 | 값 문자열이 stateValue 인 경우의 색입니다. | 예 |
| `stateValueN` | 문자열 | 값 문자열이 stateValueN 색입니다. | 아니요 |

### <a name="example-of-stringtypestylerule"></a>StringTypeStyleRule의 예

다음 JSON은 `StringTypeStyleRule` 특정 회의 형식과 연결 된 스타일을 정의 하는을 보여 줍니다.

```json
    {
      "keyname": "meetingType",
      "type": "string",
      "rules": [
        {
          "private": "#FF0000",
          "confidential": "#FF00AA",
          "allHands": "#00FF00",
          "brownBag": "#964B00"
        }
      ]
    }

```

## <a name="booleantypestylerule"></a>BooleanTypeStyleRule

는 이며 `BooleanTypeStyleRule` [`StyleObject`](#styleobject) 다음과 같은 속성으로 구성 됩니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `keyName` | 문자열 |  *상태* 또는 동적 속성 이름입니다.  는 `keyName` 배열 내에서 고유 해야 합니다 `StyleObject`  .| 예 |
| `type` | 문자열 |값은 "boolean"입니다. | 예 |
| `rules` | [`BooleanRuleObject`](#booleanruleobject)비슷합니다| 및 상태 값에 대 한 색이 있는 부울 쌍 `true` `false` *state* 입니다.| 예 |

### <a name="booleanruleobject"></a>BooleanRuleObject

는 `BooleanRuleObject` 및 값에 대 한 색을 정의 `true` `false` 합니다.

| 속성 | 유형 | 설명 | 필수 |
|-----------|----------|-------------|-------------|
| `true` | 문자열 | *상태* 값이 인 경우 사용할 색 `true` 입니다. `color`속성은 다음 형식 중 하나에 해당 하는 JSON 문자열입니다. <ul><li> HTML 스타일의 16 진수 값 </li><li> RGB ("#ff0", "#ffff00", "rgb (255, 255, 0)")</li><li> RGBA ("rgba (255, 255, 0, 1)")</li><li> HSL ("hsl (100, 50%, 50%")</li><li> HSLA ("hsla (100, 50%, 50%, 1)")</li><li> 노랑, 파랑 등의 미리 정의 된 HTML 색 이름입니다.</li></ul>| 예 |
| `false` | 문자열 | *상태* 값이 인 경우 사용할 색 `false` 입니다. | 예 |

### <a name="example-of-booleantypestylerule"></a>BooleanTypeStyleRule의 예

다음 JSON은 `BooleanTypeStyleRule` 라는 *상태* 를 보여 줍니다 `occupied` . 는 [`BooleanRuleObject`](#booleanruleobject) 및 값에 대 한 색을 정의 `true` `false` 합니다.

```json
{
    "keyname": "occupied",
    "type": "boolean",
    "rules": [
    {
        "true": "#FF0000",
        "false": "#00FF00"
    }
    ]
}
```