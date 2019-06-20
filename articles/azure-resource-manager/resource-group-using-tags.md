---
title: Добавление тегов к ресурсам Azure для их логической организации | Документация Майкрософт
description: Здесь описано, как применить теги, чтобы организовать ресурсы Azure для выставления счетов и управления.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 04/26/2019
ms.author: tomfitz
ms.openlocfilehash: 861e108efa6da3668f529e0324fd0de19fe84328
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67206509"
---
# <a name="use-tags-to-organize-your-azure-resources"></a>Использование тегов для организации ресурсов в Azure

[!INCLUDE [resource-manager-governance-tags](../../includes/resource-manager-governance-tags.md)]

Чтобы применить теги к ресурсам, у пользователя должен быть доступ на запись к этому типу ресурсов. Чтобы применить теги ко всем типам ресурсов, используйте роль [участника](../role-based-access-control/built-in-roles.md#contributor). Чтобы применить теги только к одному типу ресурсов, используйте роль участника для этого ресурса. Например, чтобы применить теги к виртуальным машинам, используйте [Участник виртуальных машин](../role-based-access-control/built-in-roles.md#virtual-machine-contributor).

[!INCLUDE [Handle personal data](../../includes/gdpr-intro-sentence.md)]

## <a name="policies"></a>Политики

Вы можете использовать службу [Политика Azure](../governance/policy/overview.md), чтобы принудительно применять правила и соглашения. Создавая политику, можно избежать сценария, в котором ресурсы развертываются в подписку, не соответствующую ожидаемым тегам вашей организации. Вместо ручного применения тегов или поиска ресурсов, которые не соответствуют требованиям, вы можете создать политику, которая автоматически применяет необходимые теги во время развертывания. В следующем разделе приведены примеры политик для тегов.

[!INCLUDE [Tag policies](../../includes/azure-policy-samples-general-tags.md)]

## <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

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

Чтобы просмотреть существующие теги для *ресурса с указанным идентификатором ресурса*, используйте:

```azurepowershell-interactive
(Get-AzResource -ResourceId /subscriptions/<subscription-id>/resourceGroups/<rg-name>/providers/Microsoft.Storage/storageAccounts/<storage-name>).Tags
```

Или чтобы просмотреть существующие теги для *ресурса с указанным именем и группой ресурсов*, используйте:

```azurepowershell-interactive
(Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup).Tags
```

Чтобы получить *группы ресурсов с определенным тегом*, используйте:

```azurepowershell-interactive
(Get-AzResourceGroup -Tag @{ Dept="Finance" }).ResourceGroupName
```

Чтобы получить *ресурсы с определенным тегом*, используйте:

```azurepowershell-interactive
(Get-AzResource -Tag @{ Dept="Finance"}).Name
```

Чтобы получить *ресурсы с определенным тегом*, используйте:

```azurepowershell-interactive
(Get-AzResource -TagName Dept).Name
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов.

Чтобы добавить теги в *группу ресурсов без тегов*, используйте этот командлет:

```azurepowershell-interactive
Set-AzResourceGroup -Name examplegroup -Tag @{ Dept="IT"; Environment="Test" }
```

Чтобы добавить теги в *группу ресурсов с существующими тегами*, извлеките их, добавьте новый тег и повторно примените теги:

```azurepowershell-interactive
$tags = (Get-AzResourceGroup -Name examplegroup).Tags
$tags.Add("Status", "Approved")
Set-AzResourceGroup -Tag $tags -Name examplegroup
```

Чтобы добавить теги в *ресурс без тегов*, используйте этот командлет:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
Set-AzResource -Tag @{ Dept="IT"; Environment="Test" } -ResourceId $r.ResourceId -Force
```

Чтобы добавить теги в *ресурс с существующими тегами*, используйте:

```azurepowershell-interactive
$r = Get-AzResource -ResourceName examplevnet -ResourceGroupName examplegroup
$r.Tags.Add("Status", "Approved")
Set-AzResource -Tag $r.Tags -ResourceId $r.ResourceId -Force
```

Чтобы добавить все теги из группы ресурсов к ресурсам, и *не сохранять существующие теги ресурсов*, используйте следующий сценарий:

```azurepowershell-interactive
$groups = Get-AzResourceGroup
foreach ($g in $groups)
{
    Get-AzResource -ResourceGroupName $g.ResourceGroupName | ForEach-Object {Set-AzResource -ResourceId $_.ResourceId -Tag $g.Tags -Force }
}
```

Чтобы добавить все теги из группы ресурсов к ресурсам, и *сохранить существующие теги ресурсов, которые не являются дубликатами*, используйте следующий сценарий:

```azurepowershell-interactive
$group = Get-AzResourceGroup "examplegroup"
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

## <a name="azure-cli"></a>Инфраструктура CLI Azure

Чтобы просмотреть существующие теги для *группы ресурсов*, используйте этот командлет:

```azurecli
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

```azurecli
az resource show -n examplevnet -g examplegroup --resource-type "Microsoft.Network/virtualNetworks" --query tags
```

При циклическом переборе коллекции ресурсов может потребоваться отобразить ресурс с помощью идентификатора ресурса. Полный пример показан далее в этой статье. Чтобы просмотреть существующие теги для *ресурса с указанным идентификатором ресурса*, используйте следующую команду:

```azurecli
az resource show --id <resource-id> --query tags
```

Чтобы получить группы ресурсов с определенным тегом, используйте команду `az group list`:

```azurecli
az group list --tag Dept=IT
```

Чтобы получить все ресурсы с определенным тегом и значением, используйте команду `az resource list`:

```azurecli
az resource list --tag Dept=Finance
```

Каждый раз, когда вы добавляете теги к ресурсу или группе ресурсов, вы перезаписываете существующие теги в этом ресурсе или группе. Поэтому необходимо использовать другой подход, исходя из того, имеются ли теги в ресурсе или в группе ресурсов.

Чтобы добавить теги в *группу ресурсов без тегов*, используйте этот командлет:

```azurecli
az group update -n examplegroup --set tags.Environment=Test tags.Dept=IT
```

Чтобы добавить теги в *ресурс без тегов*, используйте этот командлет:

```azurecli
az resource tag --tags Dept=IT Environment=Test -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить теги в ресурс, который уже содержит теги, извлеките существующие теги, переформатируйте это значение и еще раз добавьте существующие и новые теги:

```azurecli
jsonrtag=$(az resource show -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks" --query tags)
rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
az resource tag --tags $rt Project=Redesign -g examplegroup -n examplevnet --resource-type "Microsoft.Network/virtualNetworks"
```

Чтобы добавить все теги из группы ресурсов к ресурсам, и *не сохранять существующие теги ресурсов*, используйте следующий сценарий:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    az resource tag --tags $t --id $resid
  done
done
```

Чтобы добавить все теги из группы ресурсов к ресурсам, и *сохранить существующие теги ресурсов*, используйте следующий сценарий:

```azurecli
groups=$(az group list --query [].name --output tsv)
for rg in $groups
do
  jsontag=$(az group show -n $rg --query tags)
  t=$(echo $jsontag | tr -d '"{},' | sed 's/: /=/g')
  r=$(az resource list -g $rg --query [].id --output tsv)
  for resid in $r
  do
    jsonrtag=$(az resource show --id $resid --query tags)
    rt=$(echo $jsonrtag | tr -d '"{},' | sed 's/: /=/g')
    az resource tag --tags $t$rt --id $resid
  done
done
```

## <a name="templates"></a>Шаблоны

[!INCLUDE [resource-manager-tags-in-templates](../../includes/resource-manager-tags-in-templates.md)]

## <a name="portal"></a>Портал

[!INCLUDE [resource-manager-tag-resource](../../includes/resource-manager-tag-resources.md)]

## <a name="rest-api"></a>REST API

Портал Azure и PowerShell используют [интерфейс REST API диспетчера ресурсов Resource Manager](https://docs.microsoft.com/rest/api/resources/). Если вам нужно интегрировать теги в другую среду, их можно получить с помощью метода **GET** по идентификатору ресурса и обновить набор тегов с помощью вызова метода **PATCH**.

## <a name="tags-and-billing"></a>Теги и выставление счетов

С помощью тегов можно группировать данные о выставлении счетов. Например, если вы используете несколько виртуальных машин для различных организаций, то с помощью тегов сможете сгруппировать сведения об использовании по месту возникновения затрат. Кроме того, теги можно использовать для классификации затрат по среде выполнения (например, сведения о выставленных счетах за виртуальные машины, запущенные в рабочей среде).

Сведения о тегах можно получить с помощью [интерфейсов API использования ресурсов Azure и RateCard](../billing/billing-usage-rate-card-overview.md) или файла сведений об использовании с разделителями-запятыми (CSV-файла). Этот файл можно скачать в [Центре управления учетной записью Azure](https://account.azure.com/Subscriptions) или на портале Azure. Дополнительные сведения см. в статье [Скачивание или просмотр счета на оплату и данных о ежедневном использовании в Azure](../billing/billing-download-azure-invoice-daily-usage-date.md). При скачивании файла сведений об использовании из Центра управления учетной записью Azure выберите **Версия 2**. Для служб, поддерживающих теги выставления счетов, эти теги отображаются в столбце **Теги**.

Подробнее об операциях REST API см. в [справочнике по REST API для выставления счетов Azure](/rest/api/billing/).

## <a name="next-steps"></a>Дальнейшие действия

* Не все типы ресурсов поддерживают теги. Сведения о возможности применения тегов к типу ресурса см. в статье о [поддержке тегов ресурсами Azure](tag-support.md).
* Общие сведения об использовании портала см. в статье [Управление ресурсами Azure через портал](manage-resource-groups-portal.md).  
