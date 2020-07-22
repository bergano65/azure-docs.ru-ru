---
title: Развертывание ресурсов в нескольких группах ресурсов и подписках
description: Сведения о развертывании ресурсов в нескольких подписках и группах ресурсов Azure.
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: 34de1d9df53d61d849ffbb81a57b468020bc3b65
ms.sourcegitcommit: bcb962e74ee5302d0b9242b1ee006f769a94cfb8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86057387"
---
# <a name="deploy-azure-resources-across-subscriptions-or-resource-groups"></a>Развертывание ресурсов Azure в нескольких подписках или группах ресурсов

Resource Manager позволяет развертывать более одной группы ресурсов в одном развертывании. Вложенные шаблоны используются для указания групп ресурсов, отличающихся от основной группы ресурсов для операции развертывания. Эти группы ресурсов могут находиться в разных подписках.

> [!NOTE]
> В рамках одного развертывания можно выполнять развертывание **в 800 группах ресурсов**. Как правило, это ограничение означает, что для развертывания используется одна группа ресурсов из родительского шаблона и не более 799 групп ресурсов из вложенных или связанных развертываний. Но если родительский шаблон содержит только вложенные или связанные шаблоны и сам не развертывает ресурсы, то вложенные или связанные развертывания могут включать до 800 групп ресурсов.

## <a name="specify-subscription-and-resource-group"></a>Указание подписки и группы ресурсов

Чтобы выбрать группу ресурсов, которая отличается от указанной в родительском шаблоне, используйте [вложенный или связанный шаблон](linked-templates.md). В обозначении типа ресурса для развертывания укажите идентификатор подписки и группу ресурсов, в которых вы намерены развернуть этот вложенный шаблон.

:::code language="json" source="~/resourcemanager-templates/azure-resource-manager/crosssubscription.json" range="38-43" highlight="5-6":::

Если не указать идентификатор подписки или группу ресурсов, используются подписка и группа ресурсов из родительского шаблона. Все группы ресурсов необходимо создать до выполнения развертывания.

Учетная запись, которая используется для развертывания шаблона, должна иметь разрешения на развертывание в подписке с указанным идентификатором. Если указанная подписка существует в другом клиенте Azure Active Directory, необходимо [добавить гостевых пользователей из другого каталога](../../active-directory/b2b/what-is-b2b.md).

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

## <a name="use-functions"></a>Использование функций

Результат выполнения функций [resourceGroup()](template-functions-resource.md#resourcegroup) и [subscription()](template-functions-resource.md#subscription) зависит от способа, с помощью которого указан шаблон. При указании ссылки на внешний шаблон функции всегда разрешаются в области этого шаблона. Если шаблон вложен в родительский шаблон, свойство `expressionEvaluationOptions` позволяет указать, где будут разрешаться функции: в группе ресурсов и подписке родительского шаблона или вложенного шаблона. Задайте для свойства значение `inner`, чтобы использовать область вложенного шаблона. Задайте для свойства значение `outer`, чтобы использовать область родительского шаблона.

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
