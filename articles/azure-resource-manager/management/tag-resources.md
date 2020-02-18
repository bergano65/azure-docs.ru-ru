---
title: Теги ресурсов для логической организации
description: Здесь описано, как применить теги, чтобы организовать ресурсы Azure для выставления счетов и управления.
ms.topic: conceptual
ms.date: 01/03/2020
ms.openlocfilehash: c7f8d8672e205fa677bff33c8ed173c1105b26c6
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77166604"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Использование тегов для организации ресурсов в Azure

Вы примените теги к ресурсам Azure, чтобы логически упорядочить их в таксономии. Каждый тег состоит из пары "имя — значение". Например, имя Environment и значение Production можно применить ко всем ресурсам в рабочей среде.

Применив теги, вы сможете получить все ресурсы в подписке с именем и значением нужного тега. Теги позволяют извлекать связанные ресурсы из разных групп ресурсов. Такой подход может быть полезным, когда требуется организовать ресурсы для выставления счетов или управления.

Ваша таксономия должна учитывать стратегию добавления тегов метаданных самообслуживания в дополнение к стратегии автоматического добавления тегов, чтобы снизить нагрузку на пользователей и повысить точность.

[!INCLUDE [Handle personal data](../../../includes/gdpr-intro-sentence.md)]

## <a name="limitations"></a>Ограничения

Действительны следующие ограничения для тегов.

* Не все типы ресурсов поддерживают теги. Сведения о возможности применения тегов к типу ресурса см. в статье о [поддержке тегов ресурсами Azure](tag-support.md).
* Каждый ресурс или группа ресурсов может иметь не более 50 пар "имя-значение" для тегов. Если необходимо применить больше тегов, чем максимально допустимое число, используйте строку JSON для значения тега. Строка JSON может содержать много значений, применяемых к одному имени тега. Группа ресурсов может содержать множество ресурсов, каждая из которых содержит 50 пар "имя-значение" для тегов.
* Имя тега ограничено 512 символами, а значение тега — 256 символами. Для учетных записей хранения имя тега ограничено 128 символами, а значение тега — 256 символами.
* Обобщенные виртуальные машины не поддерживают теги.
* Теги, примененные к группе ресурсов, не наследуются ресурсами в этой группе.
* Теги нельзя применять к классическим ресурсам, например к облачным службам Microsoft Azure.
* Имена тегов не могут содержать следующие символы: `<`, `>`, `%`, `&`, `\`, `?`, `/`

   > [!NOTE]
   > В настоящее время Azure DNS зоны и службы диспетчера трафика не позволяют использовать пробелы в теге. 

## <a name="required-access"></a>Требуемый доступ

Чтобы применить теги к ресурсам, у пользователя должен быть доступ на запись к этому типу ресурсов. Чтобы применить теги ко всем типам ресурсов, используйте роль [участника](../../role-based-access-control/built-in-roles.md#contributor). Чтобы применить теги только к одному типу ресурсов, используйте роль участника для этого ресурса. Например, чтобы применить теги к виртуальным машинам, используйте [Участник виртуальных машин](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

## <a name="policies"></a>Политики

Вы можете использовать службу [Политика Azure](../../governance/policy/overview.md), чтобы принудительно применять правила и соглашения. Создавая политику, можно избежать сценария, в котором ресурсы развертываются в подписку, не соответствующую ожидаемым тегам вашей организации. Вместо ручного применения тегов или поиска ресурсов, которые не соответствуют требованиям, вы можете создать политику, которая автоматически применяет необходимые теги во время развертывания. Теги также можно применить к существующим ресурсам с новым действием [изменения](../../governance/policy/concepts/effects.md#modify) и [задачей исправления](../../governance/policy/how-to/remediate-resources.md). В следующем разделе приведены примеры политик для тегов.

[!INCLUDE [Tag policies](../../../includes/azure-policy-samples-policies-tags.md)]

## <a name="powershell"></a>PowerShell

Чтобы просмотреть существующие теги для *группы ресурсов*, используйте этот командлет:

```azurepowershell-interactive
(Get-AzResourceGroup -Name examplegroup).Tags
```

Этот скрипт вернет ответ в следующем формате:

```powershell
Name                           Value
----                           -----
Dept                           IT
Environment                    Test
```

Чтобы просмотреть существующие теги для *ресурса с указанным именем и группой ресурсов*, используйте:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Если у вас есть идентификатор ресурса для ресурса, можно передать этот идентификатор ресурса для получения тегов.

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Чтобы получить *группы ресурсов с указанными именем и значением тега*, используйте:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ "Dept"="Finance" }).ResourceGroupName
```

Чтобы получить *ресурсы с указанными именем и значением тега*, используйте:

```azurepowershell-interactive
(Get-AzResource -Tag @{ "Dept"="Finance"}).Name
```

Чтобы получить *ресурсы с определенным тегом*, используйте:

```azurepowershell-interactive
(Get-AzResource -TagName "Dept").Name
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов.

Чтобы добавить теги в *группу ресурсов без тегов*, используйте этот командлет:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ "Dept"="IT"; "Environment"="Test" }
```

Чтобы добавить теги в *группу ресурсов с существующими тегами*, извлеките их, добавьте новый тег и повторно примените теги:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Чтобы добавить теги в *ресурс без тегов*, используйте этот командлет:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $resource.ResourceId -Force
```

В группе ресурсов может быть несколько ресурсов с одним и тем же именем. В этом случае можно задать каждый ресурс с помощью следующих команд:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName sqlDatabase1 -ResourceGroupName examplegroup
$resource | ForEach-Object { Set-AzResource -Tag @{ "Dept"="IT"; "Environment"="Test" } -ResourceId $_.ResourceId -Force }
```

Чтобы добавить теги в *ресурс с существующими тегами*, используйте:

```azurepowershell-interactive
$resource = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$resource.Tags.Add("Status", "Approved")
Set-AzResource -Tag $resource.Tags -ResourceId $resource.ResourceId -Force
```

Чтобы применить все теги из группы ресурсов к его ресурсам и *не сохранить существующие теги в ресурсах*, используйте следующий скрипт:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
Get-AzResource -ResourceGroupName $group.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $group.Tags -Force }
```

Чтобы применить все теги из группы ресурсов к своим ресурсам и *сохранить существующие теги на ресурсах, которые не являются повторяющимися*, используйте следующий скрипт:

```azurepowershell-interactive
$group = Get-AzResourceGroup -Name examplegroup
if ($null -ne $group.Tags) {
    $resources = Get-AzResource -ResourceGroupName $group.ResourceGroupName
    foreach ($r in $resources)
    {
        $resourcetags = (Get-AzResource -ResourceId $r.ResourceId).Tags
        if ($resourcetags)
        {
            foreach ($key in $group.Tags.Keys)
            {
                if (-not($resourcetags.ContainsKey($key)))
                {
                    $resourcetags.Add($key, $group.Tags[$key])
                }
            }
            Set-AzResource -Tag $resourcetags -ResourceId $r.ResourceId -Force
        }
        else
        {
            Set-AzResource -Tag $group.Tags -ResourceId $r.ResourceId -Force
        }
    }
}
```

Чтобы удалить все теги, передайте пустую хэш-таблицу:

```azurepowershell-interactive
Set-AzResourceGroup -Tag @{} -Name examplegroup
```

## <a name="azure-cli"></a>Azure CLI

Чтобы просмотреть существующие теги для *группы ресурсов*, используйте этот командлет:

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

Чтобы просмотреть существующие теги для *ресурса с указанным именем, типом и группой ресурсов*, используйте следующую команду:

```azurecli-interactive
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

При циклическом переборе коллекции ресурсов может потребоваться отобразить ресурс с помощью идентификатора ресурса. Полный пример показан далее в этой статье. Чтобы просмотреть существующие теги для *ресурса с указанным идентификатором ресурса*, используйте следующую команду:

```azurecli-interactive
az resource show --id <resource-id> --query tags
```

Чтобы получить группы ресурсов с определенным тегом, используйте команду `az group list`:

```azurecli-interactive
az group list --tag Dept=IT
```

Чтобы получить все ресурсы с определенным тегом и значением, используйте команду `az resource list`:

```azurecli-interactive
az resource list --tag Dept=Finance
```

При добавлении тегов в группу ресурсов или ресурс можно либо перезаписать существующие теги, либо добавить новые теги в существующие теги.

Чтобы перезаписать существующие теги в группе ресурсов, используйте:

```azurecli-interactive
az group update -n examplegroup --tags 'Environment=Test' 'Dept=IT'
```

Чтобы добавить тег к существующим тегам в группе ресурсов, используйте:

```azurecli-interactive
az group update -n examplegroup --set tags.'Status'='Approved'
```

Чтобы перезаписать теги для ресурса, используйте:

```azurecli-interactive
az resource tag --tags 'Dept=IT' 'Environment=Test' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить тег к существующим тегам для ресурса, используйте:

```azurecli-interactive
az resource update --set tags.'Status'='Approved' -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы применить все теги из группы ресурсов к его ресурсам и *не сохранить существующие теги в ресурсах*, используйте следующий скрипт:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')
resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  az resource tag --tags $tags --id $id
done
```

Чтобы применить все теги из группы ресурсов к ресурсам и *сохранить существующие теги для ресурсов*, используйте следующий скрипт:

```azurecli-interactive
jsontags=$(az group show --name examplegroup --query tags -o json)
tags=$(echo $jsontags | tr -d '"{},' | sed 's/: /=/g')

resourceids=$(az resource list -g examplegroup --query [].id --output tsv)
for id in $resourceids
do
  resourcejsontags=$(az resource show --id $id --query tags -o json)
  resourcetags=$(echo $resourcejsontags | tr -d '"{},' | sed 's/: /=/g')
  az resource tag --tags $tags$resourcetags --id $id
done
```

Если имена или значения тегов содержат пробелы, необходимо выполнить несколько дополнительных действий. В следующем примере все теги из группы ресурсов применяются к его ресурсам, если теги могут содержать пробелы.

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

Чтобы пометить ресурс во время развертывания, добавьте элемент `tags` в развертываемый ресурс. и укажите имя и значение тега.

### <a name="apply-a-literal-value-to-the-tag-name"></a>Применение литерального значения к имени тега

В следующем примере показана учетная запись хранения с двумя тегами (`Dept` и `Environment`), которым присвоены литеральные значения.

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
                "Dept": "Finance",
                "Environment": "Production"
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

Чтобы задать тег для значения DateTime, используйте [функцию UtcNow](../templates/template-functions-string.md#utcnow).

### <a name="apply-an-object-to-the-tag-element"></a>Применение объекта к элементу тега

Можно определить параметр объекта, который хранит несколько тегов, и применить этот объект к элементу тега. Каждое свойство в объекте становится отдельным тегом ресурса. В следующем примере содержится параметр с именем `tagValues`, который применяется к элементу тега.

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

### <a name="apply-a-json-string-to-the-tag-name"></a>Применение строки JSON к имени тега

Для хранения большого количества значений в одном теге примените строку JSON, представляющую значения. Вся строка JSON хранится в виде одного тега, длина которого не должна превышать 256 символов. В следующем примере приведен один тег с именем `CostCenter`, содержащий несколько значений из строки JSON.  

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

### <a name="apply-tags-from-resource-group"></a>Применение тегов из группы ресурсов

Чтобы применить теги из группы ресурсов к ресурсу, используйте функцию [resourceGroup](../templates/template-functions-resource.md#resourcegroup) . При получении значения тега используйте синтаксис `tags[tag-name]` вместо синтаксиса `tags.tag-name`, так как некоторые символы не анализируются должным образом в нотации с точкой.

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

## <a name="portal"></a>Портал

[!INCLUDE [resource-manager-tag-resource](../../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Портал Azure и PowerShell используют [интерфейс REST API диспетчера ресурсов Resource Manager](/rest/api/resources/). Если вам нужно интегрировать теги в другую среду, их можно получить с помощью метода **GET** по идентификатору ресурса и обновить набор тегов с помощью вызова метода **PATCH**.

## <a name="tags-and-billing"></a>Теги и выставление счетов

С помощью тегов можно группировать данные о выставлении счетов. Например, если вы используете несколько виртуальных машин для различных организаций, то с помощью тегов сможете сгруппировать сведения об использовании по месту возникновения затрат. Кроме того, теги можно использовать для классификации затрат по среде выполнения (например, сведения о выставленных счетах за виртуальные машины, запущенные в рабочей среде).

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](../../billing/billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать в [Центре управления учетной записью Azure](https://account.azure.com/Subscriptions) или на портале Azure. Дополнительные сведения см. в статье [Скачивание или просмотр счета на оплату и данных о ежедневном использовании в Azure](../../billing/billing-download-azure-invoice-daily-usage-date.md). При скачивании файла сведений об использовании из Центра управления учетной записью Azure выберите **Версия 2**. Для служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Теги**.

Подробнее об операциях REST API см. в [справочнике по REST API для выставления счетов Azure](/rest/api/billing/).

## <a name="next-steps"></a>Дальнейшие действия

* Не все типы ресурсов поддерживают теги. Сведения о возможности применения тегов к типу ресурса см. в статье о [поддержке тегов ресурсами Azure](tag-support.md).
* Общие сведения об использовании портала см. в статье [Управление ресурсами Azure через портал](manage-resource-groups-portal.md).  
