---
title: 리소스 그룹에 리소스 배포
description: Azure Resource Manager 템플릿에서 리소스를 배포 하는 방법을 설명 합니다. 둘 이상의 리소스 그룹을 대상으로 하는 방법을 보여 줍니다.
ms.topic: conceptual
ms.date: 11/24/2020
ms.openlocfilehash: 9d0bec51fa55ee377eb647a11fb554ec3b81e9eb
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: ko-KR
ms.lasthandoff: 11/24/2020
ms.locfileid: "95807716"
---
# <a name="resource-group-deployments-with-arm-templates"></a>ARM 템플릿을 사용 하 여 리소스 그룹 배포

이 문서에서는 리소스 그룹에 대 한 배포 범위를 결정 하는 방법을 설명 합니다. 배포에 Azure Resource Manager 템플릿 (ARM 템플릿)을 사용 합니다. 또한이 문서에서는 배포 작업에서 리소스 그룹을 벗어나는 범위를 확장 하는 방법을 보여 줍니다.

## <a name="supported-resources"></a>지원되는 리소스

대부분의 리소스는 리소스 그룹에 배포할 수 있습니다. 사용 가능한 리소스 목록은 [ARM 템플릿 참조](/azure/templates)를 참조 하세요.

## <a name="schema"></a>스키마

템플릿의 경우 다음 스키마를 사용 합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

매개 변수 파일의 경우 다음을 사용합니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>배포 명령

리소스 그룹에 배포 하려면 리소스 그룹 배포 명령을 사용 합니다.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Azure CLI의 경우 [az deployment group create](/cli/azure/deployment/group#az_deployment_group_create)를 사용 합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

PowerShell 배포 명령에 대해 [AzResourceGroupDeployment](/powershell/module/az.resources/new-azresourcegroupdeployment)를 사용 합니다. 다음 예제에서는 리소스 그룹을 만드는 템플릿을 배포합니다.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

ARM 템플릿 배포에 대 한 배포 명령 및 옵션에 대 한 자세한 내용은 다음을 참조 하세요.

* [ARM 템플릿을 사용 하 여 리소스 배포 및 Azure Portal](deploy-portal.md)
* [ARM 템플릿 및 Azure CLI를 사용하여 리소스 배포](deploy-cli.md)
* [ARM 템플릿 및 Azure PowerShell을 사용하여 리소스 배포](deploy-powershell.md)
* [ARM 템플릿 및 Azure Resource Manager REST API를 사용 하 여 리소스 배포](deploy-rest.md)
* [배포 단추를 사용 하 여 GitHub 리포지토리에서 템플릿 배포](deploy-to-azure-button.md)
* [Cloud Shell에서 ARM 템플릿 배포](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>배포 범위

리소스 그룹에 배포 하는 경우 다음에 리소스를 배포할 수 있습니다.

* 작업의 대상 리소스 그룹
* 동일한 구독 또는 다른 구독에 있는 다른 리소스 그룹
* 테 넌 트의 모든 구독
* 리소스 그룹에 대 한 테 넌 트
* 리소스에 [확장 리소스](scope-extension-resources.md) 를 적용할 수 있습니다.

템플릿을 배포 하는 사용자에 게는 지정 된 범위에 대 한 액세스 권한이 있어야 합니다.

이 섹션에서는 다양 한 범위를 지정 하는 방법을 보여 줍니다. 단일 템플릿에서 이러한 여러 범위를 결합할 수 있습니다.

### <a name="scope-to-target-resource-group"></a>대상 리소스 그룹의 범위

대상 리소스에 리소스를 배포 하려면 템플릿의 리소스 섹션에 해당 리소스를 추가 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

예제 템플릿에 대해서는 [대상 리소스 그룹에 배포](#deploy-to-target-resource-group)를 참조 하세요.

### <a name="scope-to-resource-group-in-same-subscription"></a>동일한 구독의 리소스 그룹에 대 한 범위

동일한 구독의 다른 리소스 그룹에 리소스를 배포 하려면 중첩 된 배포를 추가 하 고 속성을 포함 `resourceGroup` 합니다. 구독 ID 또는 리소스 그룹을 지정하지 않으면 부모 템플릿의 구독 및 리소스 그룹이 사용됩니다. 모든 리소스 그룹은 배포를 실행하기 전에 존재해야 합니다.

다음 예제에서 중첩 된 배포는 이라는 리소스 그룹을 대상으로 `demoResourceGroup` 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

예제 템플릿은 [여러 리소스 그룹에 배포를](#deploy-to-multiple-resource-groups)참조 하세요.

### <a name="scope-to-resource-group-in-different-subscription"></a>다른 구독의 리소스 그룹에 대 한 범위

다른 구독의 리소스 그룹에 리소스를 배포 하려면 중첩 된 배포를 추가 하 고 `subscriptionId` 및 속성을 포함 `resourceGroup` 합니다. 다음 예제에서 중첩 된 배포는 이라는 리소스 그룹을 대상으로 `demoResourceGroup` 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

예제 템플릿은 [여러 리소스 그룹에 배포를](#deploy-to-multiple-resource-groups)참조 하세요.

### <a name="scope-to-subscription"></a>구독 범위

구독에 리소스를 배포 하려면 중첩 된 배포를 추가 하 고 속성을 포함 `subscriptionId` 합니다. 구독은 대상 리소스 그룹 또는 테 넌 트의 다른 구독에 대 한 구독 일 수 있습니다. 또한 `location` 중첩 된 배포에 대 한 속성을 설정 합니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-subscription.json" highlight="9,10,14":::

예제 템플릿은 [리소스 그룹 만들기](#create-resource-group)를 참조 하세요.

### <a name="scope-to-tenant"></a>테 넌 트로 범위

로 설정 된를 설정 하 여 테 넌 트에서 리소스를 만들 수 있습니다 `scope` `/` . 템플릿을 배포 하는 사용자에 게는 [테 넌 트에 배포 하는 데 필요한 액세스](deploy-to-tenant.md#required-access)권한이 있어야 합니다.

에서 중첩 된 배포를 사용 하 `scope` 고를 설정할 수 있습니다 `location` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-to-tenant.json" highlight="9,10,14":::

또는 `/` 관리 그룹과 같은 일부 리소스 형식에 대해 범위를로 설정할 수 있습니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/resource-group-create-mg.json" highlight="12,15":::

## <a name="deploy-to-target-resource-group"></a>대상 리소스 그룹에 배포

대상 리소스 그룹에 리소스를 배포 하려면 템플릿의 **리소스** 섹션에서 해당 리소스를 정의 합니다. 다음 템플릿은 배포 작업에 지정 된 리소스 그룹에 저장소 계정을 만듭니다.

:::code language="json" source="~/resourcemanager-templates/get-started-with-templates/add-outputs/azuredeploy.json":::

## <a name="deploy-to-multiple-resource-groups"></a>여러 리소스 그룹에 배포

단일 ARM 템플릿에서 둘 이상의 리소스 그룹에 배포할 수 있습니다. 부모 템플릿의 리소스 그룹과 다른 리소스 그룹을 대상으로 하려면 [중첩되거나 연결된 템플릿](linked-templates.md)을 사용합니다. 배포 리소스 형식 내에서 중첩된 템플릿을 배포하려는 구독 ID 및 리소스 그룹의 값을 지정합니다. 리소스 그룹은 서로 다른 구독에 존재할 수 있습니다.

> [!NOTE]
> 단일 배포의 **800개 리소스 그룹** 에만 배포할 수 있습니다. 일반적으로 이 제한 사항으로 인해 부모 템플릿에 지정된 하나의 리소스 그룹 및 중첩되거나 연결된 배포에서 최대 799개의 리소스 그룹에 배포할 수 있습니다. 그러나 부모 템플릿에 중첩되거나 연결된 템플릿만이 포함되고 자체적으로 리소스를 배포하지 않는 경우 중첩되거나 연결된 배포에서 최대 800개의 리소스 그룹이 포함될 수 있습니다.

다음 예제에서는 두 개의 스토리지 계정을 만듭니다. 첫 번째 스토리지 계정은 배포 작업에 지정된 리소스 그룹에 배포됩니다. 두 번째 스토리지 계정은 `secondResourceGroup` 및 `secondSubscriptionID` 매개 변수에 지정된 리소스 그룹에 배포됩니다.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

`resourceGroup`을 존재하지 않는 리소스 그룹의 이름으로 설정하면 배포에 실패합니다.

위의 템플릿을 테스트하고 결과를 확인하려면 PowerShell 또는 Azure CLI를 사용합니다.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

두 개의 스토리지 계정을 **동일한 구독** 의 두 리소스 그룹에 배포하려면 다음을 사용합니다.

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

New-AzResourceGroup -Name $firstRG -Location southcentralus
New-AzResourceGroup -Name $secondRG -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus
```

두 개의 스토리지 계정을 **두 개의 구독** 에 배포하려면 다음을 사용합니다.

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Set-AzContext -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Set-AzContext -Subscription $firstSub
New-AzResourceGroup -Name $firstRG -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName $firstRG `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json `
  -storagePrefix storage `
  -secondResourceGroup $secondRG `
  -secondStorageLocation eastus `
  -secondSubscriptionID $secondSub
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

두 개의 스토리지 계정을 **동일한 구독** 의 두 리소스 그룹에 배포하려면 다음을 사용합니다.

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

az group create --name $firstRG --location southcentralus
az group create --name $secondRG --location eastus
az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=tfstorage secondResourceGroup=$secondRG secondStorageLocation=eastus
```

두 개의 스토리지 계정을 **두 개의 구독** 에 배포하려면 다음을 사용합니다.

```azurecli-interactive
firstRG="primarygroup"
secondRG="secondarygroup"

firstSub="<first-subscription-id>"
secondSub="<second-subscription-id>"

az account set --subscription $secondSub
az group create --name $secondRG --location eastus

az account set --subscription $firstSub
az group create --name $firstRG --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group $firstRG \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crosssubscription.json \
  --parameters storagePrefix=storage secondResourceGroup=$secondRG secondStorageLocation=eastus secondSubscriptionID=$secondSub
```

---

## <a name="create-resource-group"></a>리소스 그룹 만들기

리소스 그룹 배포에서 구독의 수준으로 전환 하 고 리소스 그룹을 만들 수 있습니다. 다음 템플릿은 저장소 계정을 대상 리소스 그룹에 배포 하 고 지정 된 구독에 새 리소스 그룹을 만듭니다.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "storagePrefix": {
            "type": "string",
            "maxLength": 11
        },
        "newResourceGroupName": {
            "type": "string"
        },
        "nestedSubscriptionID": {
            "type": "string"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "variables": {
        "storageName": "[concat(parameters('storagePrefix'), uniqueString(resourceGroup().id))]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2019-06-01",
            "name": "[variables('storageName')]",
            "location": "[parameters('location')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {
            }
        },
        {
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2020-06-01",
            "name": "demoSubDeployment",
            "location": "westus",
            "subscriptionId": "[parameters('nestedSubscriptionID')]",
            "properties": {
                "mode": "Incremental",
                "template": {
                    "$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
                    "contentVersion": "1.0.0.0",
                    "parameters": {},
                    "variables": {},
                    "resources": [
                        {
                            "type": "Microsoft.Resources/resourceGroups",
                            "apiVersion": "2020-06-01",
                            "name": "[parameters('newResourceGroupName')]",
                            "location": "[parameters('location')]",
                            "properties": {}
                        }
                    ],
                    "outputs": {}
                }
            }
        }
    ]
}
```

## <a name="next-steps"></a>다음 단계

* Azure Security Center에 대한 작업 영역 설정을 배포하는 예제는 [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json)을 참조하세요.
