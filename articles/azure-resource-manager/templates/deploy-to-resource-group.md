---
title: Развертывание ресурсов в группах ресурсов
description: Описывает развертывание ресурсов в шаблоне Azure Resource Manager. В нем показано, как ориентироваться на несколько групп ресурсов.
ms.topic: conceptual
ms.date: 10/26/2020
ms.openlocfilehash: fd211641d7fcc02a1db154053597497583b21ae5
ms.sourcegitcommit: 4cb89d880be26a2a4531fedcc59317471fe729cd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92681670"
---
# <a name="resource-group-deployments-with-arm-templates"></a>Развертывание группы ресурсов с помощью шаблонов ARM

В этой статье описывается область развертывания для группы ресурсов. Для развертывания используется шаблон Azure Resource Manager (шаблон ARM). В статье также показано, как расширить область за пределами группы ресурсов в операции развертывания.

## <a name="supported-resources"></a>Поддерживаемые ресурсы

Большинство ресурсов можно развернуть в группе ресурсов. Список доступных ресурсов см. в [справочнике по шаблонам ARM](/azure/templates).

## <a name="schema"></a>схема

Для шаблонов используйте следующую схему:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    ...
}
```

Для файлов параметров используйте:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentParameters.json#",
    ...
}
```

## <a name="deployment-commands"></a>Команды развертывания

Для развертывания в группе ресурсов используйте команды развертывания группы ресурсов.

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Для Azure CLI используйте команду [AZ Deployment Group Create](/cli/azure/deployment/group#az_deployment_group_create). В следующем примере выполняется развертывание шаблона для создания группы ресурсов.

```azurecli-interactive
az deployment group create \
  --name demoRGDeployment \
  --resource-group ExampleGroup \
  --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json" \
  --parameters storageAccountType=Standard_GRS
```

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Для команды развертывания PowerShell используйте [New-азресаурцеграупдеплоймент](/powershell/module/az.resources/new-azresourcegroupdeployment). В следующем примере выполняется развертывание шаблона для создания группы ресурсов.

```azurepowershell-interactive
New-AzResourceGroupDeployment `
  -Name demoRGDeployment `
  -ResourceGroupName ExampleGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json `
  -storageAccountType Standard_GRS `
```

---

Более подробные сведения о командах и параметрах развертывания для развертывания шаблонов ARM см. в следующих статьях:

* [Развертывание ресурсов с помощью шаблонов ARM и портал Azure](deploy-portal.md)
* [Развертывание ресурсов с помощью шаблонов ARM и Azure CLI](deploy-cli.md)
* [Развертывание ресурсов с помощью шаблонов ARM и Azure PowerShell](deploy-powershell.md)
* [Развертывание ресурсов с помощью шаблонов ARM и Azure Resource Manager REST API](deploy-rest.md)
* [Использование кнопки развертывания для развертывания шаблонов из репозитория GitHub](deploy-to-azure-button.md)
* [Развертывание шаблонов ARM из Cloud Shell](deploy-cloud-shell.md)

## <a name="deployment-scopes"></a>Области развертывания

При развертывании в группе ресурсов можно развернуть ресурсы в:

* Целевая группа ресурсов из операции
* другие группы ресурсов в той же подписке или других подписках
* [ресурсы расширения](scope-extension-resources.md) можно применять к ресурсам

Пользователь, развертывающий шаблон, должен иметь доступ к указанной области.

В этом разделе показано, как указать различные области. Эти различные области можно объединить в один шаблон.

### <a name="scope-to-target-resource-group"></a>Область для целевой группы ресурсов

Чтобы развернуть ресурсы в целевом ресурсе, добавьте эти ресурсы в раздел ресурсов шаблона.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/default-rg.json" highlight="5":::

### <a name="scope-to-resource-group-in-same-subscription"></a>Область действия группы ресурсов в одной подписке

Чтобы развернуть ресурсы в другой группе ресурсов в той же подписке, добавьте вложенное развертывание и включите `resourceGroup` свойство. Если не указать идентификатор подписки или группу ресурсов, используются подписка и группа ресурсов из родительского шаблона. Все группы ресурсов необходимо создать до выполнения развертывания.

В следующем примере вложенное развертывание предназначено для группы ресурсов с именем `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/same-sub-to-resource-group.json" highlight="9,13":::

### <a name="scope-to-resource-group-in-different-subscription"></a>Область действия группы ресурсов в другой подписке

Чтобы развернуть ресурсы в группе ресурсов в другой подписке, добавьте вложенное развертывание и включите `subscriptionId` `resourceGroup` Свойства и. В следующем примере вложенное развертывание предназначено для группы ресурсов с именем `demoResourceGroup` .

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/scope/different-sub-to-resource-group.json" highlight="9,10,14":::

## <a name="cross-resource-groups"></a>Перекрестные группы ресурсов

Можно выполнить развертывание в несколько групп ресурсов в одном шаблоне ARM. Чтобы выбрать группу ресурсов, которая отличается от указанной в родительском шаблоне, используйте [вложенный или связанный шаблон](linked-templates.md). В обозначении типа ресурса для развертывания укажите идентификатор подписки и группу ресурсов, в которых вы намерены развернуть этот вложенный шаблон. Эти группы ресурсов могут находиться в разных подписках.

> [!NOTE]
> В рамках одного развертывания можно выполнять развертывание **в 800 группах ресурсов** . Как правило, это ограничение означает, что для развертывания используется одна группа ресурсов из родительского шаблона и не более 799 групп ресурсов из вложенных или связанных развертываний. Но если родительский шаблон содержит только вложенные или связанные шаблоны и сам не развертывает ресурсы, то вложенные или связанные развертывания могут включать до 800 групп ресурсов.

Следующий пример создает две учетные записи хранения. Первая учетная запись хранения развертывается в той группе ресурсов, которая указана в операции развертывания. Вторая учетная запись хранения развертывается в группе ресурсов, которая указана в параметрах `secondResourceGroup` и `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Если в качестве значения параметра `resourceGroup` задать имя несуществующей группы ресурсов, развертывание завершится сбоем.

Чтобы протестировать предыдущий шаблон и просмотреть результаты, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке** , используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в **двух подписках** , используйте следующий код:

```azurepowershell-interactive
$firstRG = "primarygroup"
$secondRG = "secondarygroup"

$firstSub = "<first-subscription-id>"
$secondSub = "<second-subscription-id>"

Select-AzSubscription -Subscription $secondSub
New-AzResourceGroup -Name $secondRG -Location eastus

Select-AzSubscription -Subscription $firstSub
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

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке** , используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в **двух подписках** , используйте следующий код:

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

## <a name="next-steps"></a>Дальнейшие действия

* Пример развертывания параметров рабочей области для центра безопасности Azure см. в разделе о [deployASCwithWorkspaceSettings.json](https://github.com/krnese/AzureDeploy/blob/master/ARM/deployments/deployASCwithWorkspaceSettings.json).
