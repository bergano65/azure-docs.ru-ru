---
title: Теговые ресурсы, ресурсные группы и подписки для логической организации
description: Здесь описано, как применить теги, чтобы организовать ресурсы Azure для выставления счетов и управления.
ms.topic: conceptual
ms.date: 03/20/2020
ms.openlocfilehash: ffc97df0923e26c3abf0eed8e7810f3b1dc61ed2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80132202"
---
# <a name="use-tags-to-organize-your-azure-resources-and-management-hierarchy"></a>Используйте теги для организации ресурсов Azure и иерархии управления

Теги применяются к ресурсам Azure, группам ресурсов и подпискам, чтобы логически организовать их в таксономию. Каждый тег состоит из пары "имя — значение". Например, имя Environment и значение Production можно применить ко всем ресурсам в рабочей среде.

Рекомендации по реализации стратегии пометки смотрите [руководство по наименованию и пометки решений ресурса.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="required-access"></a>Необходимый доступ

Чтобы применить теги к ресурсу, необходимо иметь доступ к типу ресурсов **Microsoft.Resources/tags.** Роль **тега Позволяет** применять теги к объекту, не имея доступа к самой сущности.

Роль [вкладчика](../../role-based-access-control/built-in-roles.md#contributor) также предоставляет необходимый доступ для применения тегов к любому объекту. Чтобы применить теги только к одному типу ресурсов, используйте роль участника для этого ресурса. Например, чтобы применить теги к виртуальным машинам, используйте [Участник виртуальных машин](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="powershell"></a>PowerShell

### <a name="apply-tags"></a>Применить теги

Azure PowerShell предлагает две команды для применения тегов - [New-AzTag](/powershell/module/az.resources/new-aztag) и [Update-AzTag](/powershell/module/az.resources/update-aztag). Для использования этих команд необходимо иметь Azure PowerShell 3.6.1 или позже.

**New-AzTag** заменяет все теги на ресурсе, группе ресурсов или подписке. При вызове команды передайте идентификатор ресурса объекту, который вы хотите отметить.

В следующем примере применяется набор тегов к учетной записи хранилища:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
New-AzTag -ResourceId $resource.id -Tag $tags
```

Когда команда завершается, обратите внимание, что ресурс имеет два тега.

```output
Properties :
        Name    Value
        ======  =======
        Dept    Finance
        Status  Normal
```

Если вы снова запустите команду, но на этот раз с различными тегами, обратите внимание, что более ранние теги удаляются.

```azurepowershell-interactive
$tags = @{"Team"="Compliance"; "Environment"="Production"}
New-AzTag -ResourceId $resource.id -Tag $tags
```

```output
Properties :
        Name         Value
        ===========  ==========
        Environment  Production
        Team         Compliance
```

Чтобы добавить теги к ресурсу, у которого уже есть теги, используйте **Update-AzTag.** Установите параметр **операции** для **слияния.**

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

Обратите внимание, что два новых тега были добавлены в два существующих тега.

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Normal
        Dept         Finance
        Team         Compliance
        Environment  Production
```

Каждое имя тега может иметь только одно значение. Если вы предоставляете новое значение для тега, старое значение заменяется, даже если вы используете операцию слияния. Следующий пример изменяет тег «Статус» с «нормального» на «зеленый».

```azurepowershell-interactive
$tags = @{"Status"="Green"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Merge
```

```output
Properties :
        Name         Value
        ===========  ==========
        Status       Green
        Dept         Finance
        Team         Compliance
        Environment  Production
```

При установке параметра **операции** для **замены**существующие теги заменяются новым набором тегов.

```azurepowershell-interactive
$tags = @{"Project"="ECommerce"; "CostCenter"="00123"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $tags -Operation Replace
```

На ресурсе остаются только новые теги.

```output
Properties :
        Name        Value
        ==========  =========
        CostCenter  00123
        Team        Web
        Project     ECommerce
```

Те же команды также работают с группами ресурсов или подписками. Вы проходите в идентификаторе для группы ресурсов или подписки, пометкий.

Чтобы добавить новый набор тегов в группу ресурсов, используйте:

```azurepowershell-interactive
$tags = @{"Dept"="Finance"; "Status"="Normal"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
New-AzTag -ResourceId $resourceGroup.ResourceId -tag $tags
```

Для обновления тегов для группы ресурсов используйте:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Production"}
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Update-AzTag -ResourceId $resourceGroup.ResourceId -Tag $tags -Operation Merge
```

Чтобы добавить новый набор тегов в подписку, используйте:

```azurepowershell-interactive
$tags = @{"CostCenter"="00123"; "Environment"="Dev"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
New-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags
```

Для обновления тегов для подписки используйте:

```azurepowershell-interactive
$tags = @{"Team"="Web Apps"}
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Update-AzTag -ResourceId "/subscriptions/$subscription" -Tag $tags -Operation Merge
```

В группе ресурсов может быть несколько ресурсов с одинаковым именем. В этом случае можно настроить каждый ресурс со следующими командами:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Update-AzTag -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Operation Merge }
```

### <a name="list-tags"></a>Вывод списка тегов

Чтобы получить теги для ресурса, группы ресурсов или подписки, используйте команду [Get-AzTag](/powershell/module/az.resources/get-aztag) и передайте идентификатор ресурса для сущности.

Чтобы просмотреть теги для ресурса, используйте:

```azurepowershell-interactive
$resource = Get-AzResource -Name demoStorage -ResourceGroup demoGroup
Get-AzTag -ResourceId $resource.id
```

Чтобы просмотреть теги для группы ресурсов, используйте:

```azurepowershell-interactive
$resourceGroup = Get-AzResourceGroup -Name demoGroup
Get-AzTag -ResourceId $resourceGroup.ResourceId
```

Чтобы просмотреть теги для подписки, используйте:

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Get-AzTag -ResourceId "/subscriptions/$subscription"
```

### <a name="list-by-tag"></a>Список по тегу

Чтобы получить ресурсы с определенным именем и значением тега, используйте:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "CostCenter"="00123"}).Name
```

Чтобы получить ресурсы с определенным именем тегов с любым значением тегов, используйте:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Чтобы получить группы ресурсов с определенным именем и значением тега, используйте:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "CostCenter"="00123" }).ResourceGroupName
```

### <a name="remove-tags"></a>Удалить теги

Чтобы удалить определенные теги, используйте **Update-AzTag** и **установите -Операция** **по удалению.** Передайте теги, которые вы хотите удалить.

```azurepowershell-interactive
$removeTags = @{"Project"="ECommerce"; "Team"="Web"}
Update-AzTag -ResourceId $resource.id -Tag $removeTags -Operation Delete
```

Указанные теги удаляются.

```output
Properties :
        Name        Value
        ==========  =====
        CostCenter  00123
```

Чтобы удалить все теги, используйте команду [Удалить-AzTag.](/powershell/module/az.resources/remove-aztag)

```azurepowershell-interactive
$subscription = (Get-AzSubscription -SubscriptionName "Example Subscription").Id
Remove-AzTag -ResourceId "/subscriptions/$subscription"
```

## <a name="azure-cli"></a>Azure CLI

### <a name="apply-tags"></a>Применить теги

При добавлении тегов в группу ресурсов или ресурс можно перезаписать существующие теги или присвоить новые теги к существующим теговам.

Чтобы перезаписать теги на ресурсе, используйте:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы привнести тег и пополнить существующие теги на ресурсе, используйте:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы перезаписать существующие теги в группе ресурсов, используйте:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Чтобы привнести тег и пополнить существующие теги в группе ресурсов, используйте:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

В настоящее время Azure CLI не поддерживает применение тегов к подпискам.

### <a name="list-tags"></a>Вывод списка тегов

Чтобы просмотреть существующие теги для ресурса, используйте:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

Чтобы просмотреть существующие теги для группы ресурсов, используйте этот командлет:

```azurecli-interactive
az group show -n examplegroup --query tags
```

Этот скрипт вернет ответ в следующем формате:

```json
{
  "Dept"        : "IT",
  "Environment" : "Test"
}
```

### <a name="list-by-tag"></a>Список по тегу

Чтобы получить все ресурсы с определенным тегом и значением, используйте команду `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

Чтобы получить группы ресурсов с определенным тегом, используйте команду `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

### <a name="handling-spaces"></a>Обработка помещений

Если имена или значения тегов включают пробелы, необходимо сделать несколько дополнительных шагов. Следующий пример применяется все теги из группы ресурсов к ее ресурсам, когда теги могут содержать пробелы.

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '{}"' | sed 's/: /=/g' | sed "s/\"/'/g" | sed 's/, /,/g' | sed 's/ *$//g' | sed 's/^ *//g')
origIFS=$IFS
IFS=','
read -a tagarr <<< "$tags"
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags "${tagarr[@]}" --id $id
done
IFS=$origIFS
```

## <a name="templates"></a>Шаблоны

Можно отоживать ресурсы, группы ресурсов и подписки во время развертывания с помощью шаблона «Менеджер ресурсов».

### <a name="apply-values"></a>Применяйте значения

В следующем примере развертывается учетная запись хранилища с тремя тегами. Два из тегов `Environment`(и`Dept` ) установлены на буквальное значения. Один тег`LastDeployed`() установлен на параметр, который по умолчанию к текущей дате.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "utcShort": {
            "type": "string",
            "defaultValue": "[utcNow('d')]"
        },
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "Finance",
                "Environment": "Production",
                "LastDeployed": "[parameters('utcShort')]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-an-object"></a>Применить объект

Можно определить параметр объекта, который хранит несколько тегов, и применить этот объект к элементу тега. Этот подход обеспечивает большую гибкость, чем предыдущий пример, поскольку объект может иметь различные свойства. Каждое свойство в объекте становится отдельным тегом ресурса. В следующем примере содержится параметр с именем `tagValues`, который применяется к элементу тега.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        },
        "tagValues": {
            "type": "object",
            "defaultValue": {
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": "[parameters('tagValues')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-a-json-string"></a>Применить строку JSON

Для хранения большого количества значений в одном теге примените строку JSON, представляющую значения. Вся строка JSON хранится как один тег, который не может превышать 256 символов. В следующем примере приведен один тег с именем `CostCenter`, содержащий несколько значений из строки JSON.  

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "CostCenter": "{\"Dept\":\"Finance\",\"Environment\":\"Production\"}"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-from-resource-group"></a>Применить теги из группы ресурсов

Чтобы применить теги из группы ресурсов в ресурс, используйте функцию [resourceGroup.](../templates/template-functions-resource.md#resourcegroup) При получении значения тегов `tags[tag-name]` используйте `tags.tag-name` синтаксис вместо синтаксиса, потому что некоторые символы не разбираются правильно в обозначении точки.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
            "type": "string",
            "defaultValue": "[resourceGroup().location]"
        }
    },
    "resources": [
        {
            "apiVersion": "2019-04-01",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[parameters('location')]",
            "tags": {
                "Dept": "[resourceGroup().tags['Dept']]",
                "Environment": "[resourceGroup().tags['Environment']]"
            },
            "sku": {
                "name": "Standard_LRS"
            },
            "kind": "Storage",
            "properties": {}
        }
    ]
}
```

### <a name="apply-tags-to-resource-groups-or-subscriptions"></a>Применяйте теги к группам ресурсов или подпискам

Вы можете добавить теги в группу ресурсов или подписку, развернув тип **ресурса Microsoft.Resources/tags.** Теги применяются к целевой группе ресурсов или подписке для развертывания. Каждый раз, когда вы развертываете шаблон, вы заменяете любые теги, которые были применены ранее.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tagName": {
            "type": "string",
            "defaultValue": "TeamName"
        },
        "tagValue": {
            "type": "string",
            "defaultValue": "AppTeam1"
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": {
                    "[parameters('tagName')]": "[parameters('tagValue')]"
                }
            }
        }
    ]
}
```

Чтобы применить теги к группе ресурсов, используйте clI PowerShell или Azure. Развертывание в группу ресурсов, которую необходимо отметить.

```azurepowershell-interactive
New-AzResourceGroupDeployment -ResourceGroupName exampleGroup -TemplateFile https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment group create --resource-group exampleGroup --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Чтобы применить теги к подписке, используйте clI PowerShell или Azure. Развертывание подписки, которую вы хотите отметить.

```azurepowershell-interactive
New-AzSubscriptionDeployment -name tagresourcegroup -Location westus2 -TemplateUri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

```azurecli-interactive
az deployment sub create --name tagresourcegroup --location westus2 --template-uri https://raw.githubusercontent.com/Azure/azure-docs-json-samples/master/azure-resource-manager/tags.json
```

Следующий шаблон добавляет теги с объекта в группу ресурсов или подписку.

```json
"$schema": "https://schema.management.azure.com/schemas/2018-05-01/subscriptionDeploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "tags": {
            "type": "object",
            "defaultValue": {
                "TeamName": "AppTeam1",
                "Dept": "Finance",
                "Environment": "Production"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Resources/tags",
            "name": "default",
            "apiVersion": "2019-10-01",
            "dependsOn": [],
            "properties": {
                "tags": "[parameters('tags')]"
            }
        }
    ]
}
```

## <a name="portal"></a>Портал

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Для работы с тегами через API Azure REST используйте:

* [Теги - Создание или обновление в области (операция](/rest/api/resources/tags/createorupdateatscope) PUT)
* [Теги - Обновление в области](/rest/api/resources/tags/updateatscope) (PATCH операции)
* [Теги - Получить в области](/rest/api/resources/tags/getatscope) (GET операции)
* [Теги - Удалить в области](/rest/api/resources/tags/deleteatscope) (DELETE операции)

## <a name="inherit-tags"></a>Наследуют теги

Теги, применяемые к группе ресурсов или подписке, не наследуются ресурсами. Чтобы применить теги из группы подписки [Azure Policies - tags](tag-policies.md)или ресурса к ресурсам, см.

## <a name="tags-and-billing"></a>Теги и выставление счетов

С помощью тегов можно группировать данные о выставлении счетов. Например, если вы используете несколько виртуальных машин для различных организаций, то с помощью тегов сможете сгруппировать сведения об использовании по месту возникновения затрат. Кроме того, теги можно использовать для классификации затрат по среде выполнения (например, сведения о выставленных счетах за виртуальные машины, запущенные в рабочей среде).

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](../../billing/billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать в [Центре управления учетной записью Azure](https://account.azure.com/Subscriptions) или на портале Azure. Дополнительные сведения см. в статье [Скачивание или просмотр счета на оплату и данных о ежедневном использовании в Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md). При скачивании файла сведений об использовании из Центра управления учетной записью Azure выберите **Версия 2**. Для служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Теги**.

Подробнее об операциях REST API см. в [справочнике по REST API для выставления счетов Azure](/rest/api/billing/).

## <a name="limitations"></a>Ограничения

Действительны следующие ограничения для тегов.

* Не все типы ресурсов поддерживают теги. Сведения о возможности применения тегов к типу ресурса см. в статье о [поддержке тегов ресурсами Azure](tag-support.md).
* Группы управления в настоящее время не поддерживают теги.
* Каждый ресурс, группа ресурсов и подписка могут иметь максимум 50 пар имен/значений тегов. Если вам нужно применить больше тегов, чем максимально допустимое число, используйте строку JSON для значения тегов. Строка JSON может содержать много значений, применяемых к одному имени тега. Группа ресурсов или подписка может содержать много ресурсов, каждая из которых имеет 50 пар имен/значений.
* Имя тега ограничено 512 символами, а значение тега — 256 символами. Для учетных записей хранения имя тега ограничено 128 символами, а значение тега — 256 символами.
* Обобщенные VM не поддерживают теги.
* Теги нельзя применять к классическим ресурсам, например к облачным службам Microsoft Azure.
* Имена тегов не могут содержать следующие символы: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > В настоящее время зоны Azure DNS и службы Traffic Manger также не позволяют использовать пробелы в теге.

## <a name="next-steps"></a>Дальнейшие действия

* Не все типы ресурсов поддерживают теги. Сведения о возможности применения тегов к типу ресурса см. в статье о [поддержке тегов ресурсами Azure](tag-support.md).
* Рекомендации по реализации стратегии пометки смотрите [руководство по наименованию и пометки решений ресурса.](/azure/cloud-adoption-framework/decision-guides/resource-tagging/?toc=/azure/azure-resource-manager/management/toc.json)
