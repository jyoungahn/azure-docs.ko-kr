---
title: Studio를 사용 하 여 디자이너에 학습 된 모델 배포
titleSuffix: Azure Machine Learning
description: Azure Machine Learning studio를 사용 하 여 코드를 한 줄도 작성 하지 않고 Machine Learning 모델을 배포 합니다.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.author: keli19
author: likebupt
ms.reviewer: peterlu
ms.date: 10/29/2020
ms.topic: conceptual
ms.custom: how-to, deploy, studio
ms.openlocfilehash: 23c6417741d0753fcdaaf30c89c8f51348cc5dc5
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/12/2020
ms.locfileid: "94554685"
---
# <a name="use-the-studio-to-deploy-models-trained-in-the-designer"></a>Studio를 사용 하 여 디자이너에 학습 된 모델 배포

이 문서에서는 디자이너의 학습 된 모델을 Azure Machine Learning studio에서 실시간 끝점으로 배포 하는 방법에 대해 알아봅니다.

스튜디오에서 배포는 다음 단계로 구성 됩니다.

1. 학습 된 모델을 등록 합니다.
1. 모델에 대 한 entry 스크립트 및 conda 종속성 파일을 다운로드 합니다.
1. 필드 항목 스크립트를 구성 합니다.
1. 계산 대상에 모델을 배포 합니다.

모델 등록 및 파일 다운로드 단계를 건너뛰도록 디자이너에서 직접 모델을 배포할 수도 있습니다. 이는 신속한 배포에 유용할 수 있습니다. 자세한 내용은 [designer를 사용 하 여 모델 배포](tutorial-designer-automobile-price-deploy.md)를 참조 하세요.

디자이너에서 학습 한 모델은 SDK 또는 CLI (명령줄 인터페이스)를 통해 배포할 수도 있습니다. 자세한 내용은 [Azure Machine Learning를 사용 하 여 기존 모델 배포](how-to-deploy-existing-model.md)를 참조 하세요.

## <a name="prerequisites"></a>필수 구성 요소

* [Azure Machine Learning 작업 영역](how-to-manage-workspace.md)

* 다음 모듈 중 하나를 포함 하는 완료 된 학습 파이프라인:
    - [모델 학습 모듈](./algorithm-module-reference/train-model.md)
    - [변칙 검색 모델 학습 모듈](./algorithm-module-reference/train-anomaly-detection-model.md)
    - [클러스터링 모델 학습 모듈](./algorithm-module-reference/train-clustering-model.md)
    - [Pytorch 모델 학습 모듈](./algorithm-module-reference/train-pytorch-model.md)
    - [.SVD 추천 모듈 학습](./algorithm-module-reference/train-svd-recommender.md)
    - [Vowpal Wabbit 모델 학습 모듈](./algorithm-module-reference/train-vowpal-wabbit-model.md)
    - [넓은 & 심화 모델 모듈 학습](./algorithm-module-reference/train-wide-and-deep-recommender.md)

## <a name="register-the-model"></a>모델 등록

학습 파이프라인이 완료 된 후에는 학습 된 모델을 Azure Machine Learning 작업 영역에 등록 하 여 다른 프로젝트의 모델에 액세스할 수 있습니다.

1. [모델 학습 모듈](./algorithm-module-reference/train-model.md)을 선택 합니다.
1. 오른쪽 창에서 **출력 + 로그** 탭을 선택 합니다.
1. 기어 아이콘의 **모델 등록** 아이콘 스크린샷을 선택 ![ ](./media/how-to-deploy-model-designer/register-model-icon.png) 합니다.

    ![모델 학습 모듈의 오른쪽 창 스크린샷](./media/how-to-deploy-model-designer/train-model-right-pane.png)

1. 모델의 이름을 입력 하 고 **저장** 을 선택 합니다.

모델을 등록 한 후에는 스튜디오의 **모델** 자산 페이지에서 찾을 수 있습니다.
    
![모델 자산 페이지에서 등록 된 모델의 스크린샷](./media/how-to-deploy-model-designer/models-asset-page.png)


## <a name="download-the-entry-script-file-and-conda-dependencies-file"></a>항목 스크립트 파일 및 conda 종속성 파일 다운로드

Azure Machine Learning studio에서 모델을 배포 하려면 다음 파일이 필요 합니다.

- **입력 스크립트 파일** -학습 된 모델을 로드 하 고, 요청에서 입력 데이터를 처리 하 고, 실시간 추론를 수행 하 고, 결과를 반환 합니다. `score.py` **모델 학습** 모듈을 완료 하면 디자이너에서 자동으로 입력 스크립트 파일을 생성 합니다.

- **Conda 종속성 파일** -webservice가 종속 된 pip 및 Conda 패키지를 지정 합니다. `conda_env.yaml` **모델 학습** 모듈이 완료 되 면 디자이너에서 자동으로 파일을 만듭니다.

**모델 학습** 모듈의 오른쪽 창에서 다음 두 파일을 다운로드할 수 있습니다.

1. **모델 학습** 모듈을 선택합니다.
1. **출력 + 로그** 탭에서 폴더를 선택 합니다 `trained_model_outputs` .
1. `conda_env.yaml`파일 및 파일을 다운로드 `score.py` 합니다.

    ![오른쪽 창에 배포용 파일 다운로드 스크린샷](./media/how-to-deploy-model-designer/download-artifacts-in-right-pane.png)

또는 모델을 등록 한 후 **모델** 자산 페이지에서 파일을 다운로드할 수 있습니다.

1. **모델** 자산 페이지로 이동 합니다.
1. 배포 하려는 모델을 선택 합니다.
1. **아티팩트** 탭을 선택 합니다.
1. `trained_model_outputs` 폴더를 선택합니다.
1. `conda_env.yaml`파일 및 파일을 다운로드 `score.py` 합니다.  

    ![모델 세부 정보 페이지에서 배포용 다운로드 파일의 스크린샷](./media/how-to-deploy-model-designer/download-artifacts-in-models-page.png)

> [!NOTE]
> 이 `score.py` 파일은 **모델 점수 매기기** 모듈과 거의 동일한 기능을 제공 합니다. 그러나 [점수 .Svd 추천](./algorithm-module-reference/score-svd-recommender.md), [점수 넓게 및 딥 추천](./algorithm-module-reference/score-wide-and-deep-recommender.md), [점수 Vowpal wabbit 모델](./algorithm-module-reference/score-vowpal-wabbit-model.md) 등의 일부 모듈에는 다른 점수 매기기 모드의 매개 변수가 있습니다. 항목 스크립트에서 이러한 매개 변수를 변경할 수도 있습니다.
>
>파일에서 매개 변수를 설정 하는 방법에 대 한 자세한 내용은 `score.py` [항목 스크립트 구성](#configure-the-entry-script)섹션을 참조 하세요.

## <a name="deploy-the-model"></a>모델 배포

필요한 파일을 다운로드 한 후에는 모델을 배포할 준비가 된 것입니다.

1. **모델** 자산 페이지에서 등록 된 모델을 선택 합니다.
1. **배포** 단추를 선택 합니다.
1. 구성 메뉴에서 다음 정보를 입력 합니다.

    - 끝점의 이름을 입력 합니다.
    - [Azure Kubernetes Service](how-to-deploy-azure-kubernetes-service.md) 또는 [azure Container Instance](how-to-deploy-azure-container-instance.md)에 모델을 배포 하려면 선택 합니다.
    - `score.py` **항목 스크립트 파일** 에 대 한를 업로드 합니다.
    - `conda_env.yml` **Conda 종속성 파일** 에 대해를 업로드 합니다. 

    >[!TIP]
    > **고급** 설정에서 CPU/메모리 용량 및 기타 배포 매개 변수를 설정할 수 있습니다. 이러한 설정은 상당한 양의 memery (약 4gb)를 사용 하는 PyTorch 모델과 같은 특정 모델에 중요 합니다.

1. **배포** 를 선택 하 여 모델을 실시간 끝점으로 배포 합니다.

    ![모델 자산에 모델 배포 페이지의 스크린샷](./media/how-to-deploy-model-designer/deploy-model.png)

## <a name="consume-the-real-time-endpoint"></a>실시간 끝점 사용

배포가 성공 하면 **끝점** 자산 페이지에서 실시간 끝점을 찾을 수 있습니다. 이 경우 클라이언트가 실시간 끝점에 요청을 전송 하는 데 사용할 수 있는 REST 끝점을 찾을 수 있습니다. 

> [!NOTE]
> 또한 디자이너는 테스트를 위해 샘플 데이터 json 파일을 생성 하 고 `_samples.json` **trained_model_outputs** 폴더에서 다운로드할 수 있습니다.

다음 코드 샘플을 사용 하 여 실시간 끝점을 사용 합니다.

```python

import json
from pathlib import Path
from azureml.core.workspace import Workspace, Webservice
 
service_name = 'YOUR_SERVICE_NAME'
ws = Workspace.get(
    name='WORKSPACE_NAME',
    subscription_id='SUBSCRIPTION_ID',
    resource_group='RESOURCEGROUP_NAME'
)
service = Webservice(ws, service_name)
sample_file_path = '_samples.json'
 
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)
score_result = service.run(json.dumps(sample_data))
print(f'Inference result = {score_result}')
```

### <a name="consume-computer-vision-related-real-time-endpoints"></a>컴퓨터 시각 관련 실시간 끝점 사용

웹 서비스는 문자열을 입력으로 허용 하기 때문에 컴퓨터 비전 관련 실시간 끝점을 사용 하는 경우 이미지를 바이트로 변환 해야 합니다. 다음은 샘플 코드입니다.

```python
import base64
import json
from copy import deepcopy
from pathlib import Path
from azureml.studio.core.io.image_directory import (IMG_EXTS, image_from_file, image_to_bytes)
from azureml.studio.core.io.transformation_directory import ImageTransformationDirectory

# image path
image_path = Path('YOUR_IMAGE_FILE_PATH')

# provide the same parameter setting as in the training pipeline. Just an example here.
image_transform = [
    # format: (op, args). {} means using default parameter values of torchvision.transforms.
    # See https://pytorch.org/docs/stable/torchvision/transforms.html
    ('Resize', 256),
    ('CenterCrop', 224),
    # ('Pad', 0),
    # ('ColorJitter', {}),
    # ('Grayscale', {}),
    # ('RandomResizedCrop', 256),
    # ('RandomCrop', 224),
    # ('RandomHorizontalFlip', {}),
    # ('RandomVerticalFlip', {}),
    # ('RandomRotation', 0),
    # ('RandomAffine', 0),
    # ('RandomGrayscale', {}),
    # ('RandomPerspective', {}),
]
transform = ImageTransformationDirectory.create(transforms=image_transform).torch_transform

# download _samples.json file under Outputs+logs tab in the right pane of Train Pytorch Model module
sample_file_path = '_samples.json'
with open(sample_file_path, 'r') as f:
    sample_data = json.load(f)

# use first sample item as the default value
default_data = sample_data[0]
data_list = []
for p in image_path.iterdir():
    if p.suffix.lower() in IMG_EXTS:
        data = deepcopy(default_data)
        # convert image to bytes
        data['image'] = base64.b64encode(image_to_bytes(transform(image_from_file(p)))).decode()
        data_list.append(data)

# use data.json as input of consuming the endpoint
data_file_path = 'data.json'
with open(data_file_path, 'w') as f:
    json.dump(data_list, f)
```

## <a name="configure-the-entry-script"></a>항목 스크립트 구성

[점수 .Svd 추천](./algorithm-module-reference/score-svd-recommender.md), [점수 넓게 및 딥 추천](./algorithm-module-reference/score-wide-and-deep-recommender.md), [점수 Vowpal wabbit 모델](./algorithm-module-reference/score-vowpal-wabbit-model.md) 등 디자이너의 일부 모듈에는 다른 점수 매기기 모드의 매개 변수가 있습니다. 

이 섹션에서는 entry 스크립트 파일에서 이러한 매개 변수를 업데이트 하는 방법에 대해 알아봅니다.

다음 예에서는 학습 된 **넓은 & 심층 추천** 모델에 대 한 기본 동작을 업데이트 합니다. 기본적으로이 `score.py` 파일은 사용자와 항목 간의 등급을 예측 하도록 웹 서비스에 지시 합니다. 

항목 스크립트 파일을 수정 하 여 항목 추천을 만든 다음 매개 변수를 변경 하 여 권장 항목을 반환할 수 있습니다 `recommender_prediction_kind` .


```python
import os
import json
from pathlib import Path
from collections import defaultdict
from azureml.studio.core.io.model_directory import ModelDirectory
from azureml.designer.modules.recommendation.dnn.wide_and_deep.score. \
    score_wide_and_deep_recommender import ScoreWideAndDeepRecommenderModule
from azureml.designer.serving.dagengine.utils import decode_nan
from azureml.designer.serving.dagengine.converter import create_dfd_from_dict

model_path = os.path.join(os.getenv('AZUREML_MODEL_DIR'), 'trained_model_outputs')
schema_file_path = Path(model_path) / '_schema.json'
with open(schema_file_path) as fp:
    schema_data = json.load(fp)


def init():
    global model
    model = ModelDirectory.load(load_from_dir=model_path)


def run(data):
    data = json.loads(data)
    input_entry = defaultdict(list)
    for row in data:
        for key, val in row.items():
            input_entry[key].append(decode_nan(val))

    data_frame_directory = create_dfd_from_dict(input_entry, schema_data)

    # The parameter names can be inferred from Score Wide and Deep Recommender module parameters:
    # convert the letters to lower cases and replace whitespaces to underscores.
    score_params = dict(
        trained_wide_and_deep_recommendation_model=model,
        dataset_to_score=data_frame_directory,
        training_data=None,
        user_features=None,
        item_features=None,
        ################### Note #################
        # Set 'Recommender prediction kind' parameter to enable item recommendation model
        recommender_prediction_kind='Item Recommendation',
        recommended_item_selection='From All Items',
        maximum_number_of_items_to_recommend_to_a_user=5,
        whether_to_return_the_predicted_ratings_of_the_items_along_with_the_labels='True')
    result_dfd, = ScoreWideAndDeepRecommenderModule().run(**score_params)
    result_df = result_dfd.data
    return json.dumps(result_df.to_dict("list"))
```

**넓은 & 심층 추천** 및 **Vowpal wabbit** 모델의 경우 다음 메서드를 사용 하 여 점수 매기기 모드 매개 변수를 구성할 수 있습니다.

- 매개 변수 이름은 [점수 Vowpal Wabbit 모델](./algorithm-module-reference/score-vowpal-wabbit-model.md) 에 [대 한 매개](./algorithm-module-reference/score-wide-and-deep-recommender.md)변수 이름의 소문자 및 밑줄 조합입니다.
- 모드 형식 매개 변수 값은 해당 하는 옵션 이름의 문자열입니다. 위의 코드에서 **추천 예측 종류** 를 예로 들어 보겠습니다. 값은 또는 일 수 있습니다 `'Rating Prediction'` `'Item Recommendation'` . 다른 값은 허용 되지 않습니다.

**.Svd 추천** 학습 된 모델의 경우 매개 변수 이름과 값이 명확 하지 않을 수 있으며, 아래 표를 조회 하 여 매개 변수를 설정 하는 방법을 결정할 수 있습니다.

| [점수 .Svd 추천](./algorithm-module-reference/score-svd-recommender.md) 의 매개 변수 이름                           | 항목 스크립트 파일의 매개 변수 이름 |
| ------------------------------------------------------------ | --------------------------------------- |
| 추천 예측 종류                                  | prediction_kind                         |
| 추천 항목 선택                                   | recommended_item_selection              |
| 한 사용자에 대한 권장 풀의 최소 크기    | min_recommendation_pool_size            |
| 사용자에게 권장할 최대 항목 수               | max_recommended_item_count              |
| 레이블과 함께 항목의 예측 등급을 반환할지 여부 | return_ratings                          |

다음 코드에서는 6 개의 매개 변수를 모두 사용 하 여 예측 등급이 첨부 된 등급 항목을 권장 하는 .SVD 추천에 대 한 매개 변수를 설정 하는 방법을 보여 줍니다.

```python
score_params = dict(
        learner=model,
        test_data=DataTable.from_dfd(data_frame_directory),
        training_data=None,
        # RecommenderPredictionKind has 2 members, 'RatingPrediction' and 'ItemRecommendation'. You
        # can specify prediction_kind parameter with one of them.
        prediction_kind=RecommenderPredictionKind.ItemRecommendation,
        # RecommendedItemSelection has 3 members, 'FromAllItems', 'FromRatedItems', 'FromUndatedItems'.
        # You can specify recommended_item_selection parameter with one of them.
        recommended_item_selection=RecommendedItemSelection.FromRatedItems,
        min_recommendation_pool_size=1,
        max_recommended_item_count=3,
        return_ratings=True,
    )
```


## <a name="next-steps"></a>다음 단계

* [디자이너에서 모델 학습](tutorial-designer-automobile-price-train-score.md)
* [Azure Machine Learning SDK를 사용 하 여 모델 배포](how-to-deploy-and-where.md)
* [실패한 배포 문제 해결](how-to-troubleshoot-deployment.md)
* [Azure Kubernetes Service로 배포](how-to-deploy-azure-kubernetes-service.md)
* [웹 서비스를 사용 하는 클라이언트 응용 프로그램 만들기](how-to-consume-web-service.md)
* [웹 서비스 업데이트](how-to-deploy-update-web-service.md)
