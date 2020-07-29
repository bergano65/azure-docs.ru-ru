---
title: Развертывание ресурсов в разных областях
description: Показывает, как ориентироваться на более одной области во время развертывания. Областью может быть клиент, группы управления, подписки и группы ресурсов.
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 6161401ac039551a814b595715f56df1ac62dd6c
ms.sourcegitcommit: f353fe5acd9698aa31631f38dd32790d889b4dbb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/29/2020
ms.locfileid: "87374560"
---
# <a name="deploy-azure-resources-across-scopes"></a>Развертывание ресурсов Azure в разных областях

С помощью шаблонов Azure Resource Manager (шаблоны ARM) можно развернуть несколько областей в одном развертывании. Доступные области — клиент, группы управления, подписки и группы ресурсов. Например, можно развернуть ресурсы в одной группе ресурсов и в том же шаблоне развернуть ресурсы в другой группе ресурсов. Кроме того, можно развернуть ресурсы в группе управления, а также развернуть ресурсы в группе ресурсов в этой группе управления.

[Вложенные или связанные шаблоны](linked-templates.md) используются для указания областей, которые отличаются от первичной области действия развертывания.

## <a name="available-scopes"></a>Доступные области

Область, используемая для операции развертывания, определяет, какие другие области доступны. Можно выполнить развертывание в [клиент](deploy-to-tenant.md), [группу управления](deploy-to-management-group.md), [подписку](deploy-to-subscription.md)или [группу ресурсов](deploy-powershell.md). На основном уровне развертывания нельзя переходить к уровням в иерархии. Например, при развертывании в подписке невозможно выполнить шаг с заходом на уровень для развертывания ресурсов в группе управления. Однако для развертывания в подписке или группе ресурсов можно выполнить развертывание в группе управления и на уровне шага.

Для каждой области пользователь, развертывающий шаблон, должен иметь необходимые разрешения для создания ресурсов.

## <a name="cross-resource-groups"></a>Перекрестные группы ресурсов

Чтобы выбрать группу ресурсов, которая отличается от указанной в родительском шаблоне, используйте [вложенный или связанный шаблон](linked-templates.md). В обозначении типа ресурса для развертывания укажите идентификатор подписки и группу ресурсов, в которых вы намерены развернуть этот вложенный шаблон. Эти группы ресурсов могут находиться в разных подписках.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Если не указать идентификатор подписки или группу ресурсов, используются подписка и группа ресурсов из родительского шаблона. Все группы ресурсов необходимо создать до выполнения развертывания.

> [!NOTE]
> В рамках одного развертывания можно выполнять развертывание **в 800 группах ресурсов**. Как правило, это ограничение означает, что для развертывания используется одна группа ресурсов из родительского шаблона и не более 799 групп ресурсов из вложенных или связанных развертываний. Но если родительский шаблон содержит только вложенные или связанные шаблоны и сам не развертывает ресурсы, то вложенные или связанные развертывания могут включать до 800 групп ресурсов.

Следующий пример создает две учетные записи хранения. Первая учетная запись хранения развертывается в той группе ресурсов, которая указана в операции развертывания. Вторая учетная запись хранения развертывается в группе ресурсов, которая указана в параметрах `secondResourceGroup` и `secondSubscriptionID`:

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json":::

Если в качестве значения параметра `resourceGroup` задать имя несуществующей группы ресурсов, развертывание завершится сбоем.

Чтобы протестировать предыдущий шаблон и просмотреть результаты, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке**, используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в **двух подписках**, используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в двух группах ресурсов в **одной подписке**, используйте следующий код:

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

Чтобы развернуть две учетные записи хранения в **двух подписках**, используйте следующий код:

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

## <a name="cross-subscription-management-group-and-tenant"></a>Перекрестная подписка, Группа управления и клиент

При указании различных областей для подписки, группы управления и развертывания на уровне клиента используются вложенные развертывания, такие как пример для групп ресурсов. Свойства, используемые для указания области, могут отличаться. Эти сценарии описаны в статьях, посвященных уровням развертываний. Дополнительные сведения см. в разделе:

* [Создание групп ресурсов и ресурсов на уровне подписки](deploy-to-subscription.md)
* [Создание ресурсов на уровне группы управления](deploy-to-management-group.md)
* [Создание ресурсов на уровне клиента](deploy-to-tenant.md)

## <a name="how-functions-resolve-in-scopes"></a>Как функции разрешаются в областях

При развертывании в нескольких областях функции [resourceGroup ()](template-functions-resource.md#resourcegroup) и [Subscription ()](template-functions-resource.md#subscription) разрешаются по-разному в зависимости от способа указания шаблона. При указании ссылки на внешний шаблон функции всегда разрешаются в области этого шаблона. Если шаблон вложен в родительский шаблон, свойство `expressionEvaluationOptions` позволяет указать, где будут разрешаться функции: в группе ресурсов и подписке родительского шаблона или вложенного шаблона. Задайте для свойства значение `inner`, чтобы использовать область вложенного шаблона. Задайте для свойства значение `outer`, чтобы использовать область родительского шаблона.

В следующей таблице указано, где будут разрешаться функции: в группе ресурсов и подписке родительского шаблона или вложенного шаблона.

| Тип шаблона | Область | Решение |
| ------------- | ----- | ---------- |
| вложенный        | внешний (по умолчанию) | родительская группа ресурсов |
| вложенный        | Внутреннее | вложенная группа ресурсов |
| связанный        | Недоступно   | вложенная группа ресурсов |

В следующем [примере шаблона](https://github.com/Azure/azure-docs-json-samples/blob/master/azure-resource-manager/crossresourcegroupproperties.json) представлено следующее:

* вложенный шаблон с областью по умолчанию (внешней);
* вложенный шаблон с внутренней областью;
* связанный шаблон.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crossresourcegroupproperties.json":::

Чтобы протестировать предыдущий шаблон и просмотреть результаты, используйте PowerShell или Azure CLI.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

```azurepowershell-interactive
New-AzResourceGroup -Name parentGroup -Location southcentralus
New-AzResourceGroup -Name inlineGroup -Location southcentralus
New-AzResourceGroup -Name linkedGroup -Location southcentralus

New-AzResourceGroupDeployment `
  -ResourceGroupName parentGroup `
  -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Выходные данные из предыдущего примера:

```output
 Name             Type                       Value
 ===============  =========================  ==========
 parentRG         String                     Parent resource group is parentGroup
 defaultScopeRG   String                     Default scope resource group is parentGroup
 innerScopeRG     String                     Inner scope resource group is inlineGroup
 linkedRG         String                     Linked resource group is linkedgroup
```

# <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

```azurecli-interactive
az group create --name parentGroup --location southcentralus
az group create --name inlineGroup --location southcentralus
az group create --name linkedGroup --location southcentralus

az deployment group create \
  --name ExampleDeployment \
  --resource-group parentGroup \
  --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/crossresourcegroupproperties.json
```

Выходные данные из предыдущего примера:

```output
"outputs": {
  "defaultScopeRG": {
    "type": "String",
    "value": "Default scope resource group is parentGroup"
  },
  "innerScopeRG": {
    "type": "String",
    "value": "Inner scope resource group is inlineGroup"
  },
  "linkedRG": {
    "type": "String",
    "value": "Linked resource group is linkedGroup"
  },
  "parentRG": {
    "type": "String",
    "value": "Parent resource group is parentGroup"
  }
},
```

---



## <a name="next-steps"></a>Дальнейшие действия

* Сведения об определении параметров в шаблоне см. в статье [Описание структуры и синтаксиса шаблонов Azure Resource Manager](template-syntax.md).
* Советы по устранению распространенных ошибок развертывания см. в разделе [Устранение распространенных ошибок развертывания в Azure с помощью Azure Resource Manager](common-deployment-errors.md).
* Сведения о развертывании шаблона, которому нужен токен SAS, см. в статье [Развертывание частного шаблона с помощью маркера SAS](secure-template-with-sas-token.md).
