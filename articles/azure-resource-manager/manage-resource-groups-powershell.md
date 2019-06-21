---
title: Управление группами Azure Resource Manager с помощью Azure PowerShell | Документация Майкрософт
description: Используйте Azure PowerShell для управления группами Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 3d6a102b794ca9c43e1dd18f923f6ce224596499
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296258"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Управление группами ресурсов Azure Resource Manager с помощью Azure PowerShell

Узнайте, как использовать Azure PowerShell с [Azure Resource Manager](resource-group-overview.md) для управления группами ресурсов Azure. Для управления ресурсами Azure, см. в разделе [управление ресурсами Azure с помощью Azure PowerShell](./manage-resources-powershell.md).

Другие статьи об управлении группами ресурсов:

- [Управление группами ресурсов Azure с помощью портала Azure](./manage-resources-portal.md)
- [Управление группами ресурсов Azure с помощью Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Что такое группа ресурсов

Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. В группу ресурсов могут входить все ресурсы приложения или только те, которыми необходимо управлять совместно. Кроме того, пользователи могут выбрать оптимальный для своей организации способ распределения ресурсов в группах ресурсов. Как правило, ресурсы с общим жизненным циклом добавляют в одну и ту же группу ресурсов, чтобы их можно было легко развертывать, обновлять и удалять в виде группы.

В группе ресурсов хранятся метаданные о ресурсах. Таким образом, указывая расположение группы ресурсов, вы определяете расположение метаданных. В целях обеспечения соответствия необходимо убедиться, что данные хранятся в определенном регионе.

В группе ресурсов хранятся метаданные о ресурсах. При указании расположения группы ресурсов, вы указываете, где хранятся эти метаданные.

## <a name="create-resource-groups"></a>Создание группы ресурсов

Следующий сценарий PowerShell создает группу ресурсов и затем отображает группы ресурсов.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Перечислить группы ресурсов

Следующий сценарий PowerShell список групп ресурсов в подписке.

```azurepowershell-interactive
Get-AzResourceGroup
```

Чтобы получить одну группу ресурсов:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Удаление группы ресурсов

Следующий сценарий PowerShell удаляет группу ресурсов:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Дополнительные сведения о как упорядочивает удаление ресурсов в Azure Resource Manager, см. в разделе [удаление группы ресурсов Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующую группу ресурсов

См. в разделе [развертывание ресурсов в существующую группу ресурсов](./manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group).

Чтобы проверить развертывание группы ресурсов, см. в разделе [AzResourceGroupDeployment теста](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0).

## <a name="deploy-a-resource-group-and-resources"></a>Развернуть группу ресурсов и ресурсы

Можно создать группу ресурсов и развернуть ресурсы в группу с помощью шаблона Resource Manager. См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Эта функция также называется *отката в случае ошибки*. Дополнительные сведения см. в разделе [повторное развертывание при сбое развертывания](./resource-group-template-deploy.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Переместить в другую группу ресурсов или подписку

Ресурсы в группе можно переместить в другую группу ресурсов. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](./resource-group-move-resources.md#move-resources).

## <a name="lock-resource-groups"></a>Блокировки группы ресурсов

Блокировка запрещает другим пользователям в организации случайно удалить или изменить критически важные ресурсы, такие как подписки Azure, группу ресурсов или ресурс. 

Следующий сценарий блокирует группу ресурсов, поэтому невозможно удалить группу ресурсов.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Следующий сценарий получает все блокировки для группы ресурсов:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Тег группы ресурсов

К ресурсам и их группам можно добавлять теги. Это позволяет логически их упорядочивать. Сведения см. в разделе [использование тегов для организации ресурсов в Azure](./resource-group-using-tags.md#powershell).

## <a name="export-resource-groups-to-templates"></a>Экспорт групп ресурсов в шаблонах

После настройки группы ресурсов, можно просмотреть шаблон Resource Manager для группы ресурсов. Экспорт шаблона обеспечивает два преимущества:

- Автоматизируйте будущие развертывания решения, так как шаблон содержит полноценную инфраструктуру.
- Узнайте синтаксис шаблона путем поиска в в объект нотацию JavaScript (JSON) своего решения.

Чтобы экспортировать все ресурсы в группе ресурсов, используйте [AzResourceGroup экспорта](/powershell/module/az.resources/Export-AzResourceGroup) командлет и укажите имя группы ресурсов.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Она сохраняет шаблон в виде локального файла.

Вместо экспорта все ресурсы в группе ресурсов, вы можете выбрать, какие ресурсы для экспорта.

Чтобы экспортировать один ресурс, передайте идентификатор этого ресурса.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Чтобы экспортировать более одного ресурса, передайте идентификаторы ресурсов в массив.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

При экспорте шаблона, можно указать, используются ли параметры в шаблоне. По умолчанию включаются параметры для имен ресурсов, но они не имеет значения по умолчанию. Значение этого параметра необходимо передать во время развертывания.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": null,
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": null,
    "type": "String"
  }
}
```

В ресурсе параметр используется для имени.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "[parameters('serverfarms_demoHostPlan_name')]",
    ...
  }
]
```

Если вы используете `-IncludeParameterDefaultValue` параметр при экспорте шаблона, включает в себя параметр шаблона по умолчанию ей присваивается текущее значение. Можно использовать это значение по умолчанию или заменить значение по умолчанию, передав другое значение.

```json
"parameters": {
  "serverfarms_demoHostPlan_name": {
    "defaultValue": "demoHostPlan",
    "type": "String"
  },
  "sites_webSite3bwt23ktvdo36_name": {
    "defaultValue": "webSite3bwt23ktvdo36",
    "type": "String"
  }
}
```

Если вы используете `-SkipResourceNameParameterization` параметр при экспорте шаблона, параметры для имен ресурсов не включается в шаблон. Вместо этого непосредственно на ресурсе, чтобы его текущее значение задано имя ресурса. Имя не может настроить во время развертывания.

```json
"resources": [
  {
    "type": "Microsoft.Web/serverfarms",
    "apiVersion": "2016-09-01",
    "name": "demoHostPlan",
    ...
  }
]
```

Дополнительные сведения см. в разделе [экспорта одной или нескольких ресурсов в шаблон на портале Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Управление доступом к группам ресурсов

[Управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Дополнительные сведения см. в разделе [управление доступом с помощью Azure PowerShell и RBAC](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, Azure Resource Manager, см. в разделе [обзоре Azure Resource Manager](./resource-group-overview.md).
- Чтобы узнать синтаксис шаблона Resource Manager, см. в разделе [описание структуры и синтаксиса шаблонов Azure Resource Manager](./resource-group-authoring-templates.md).
- Чтобы научиться разрабатывать шаблоны, см. в разделе [пошаговые учебники](/azure/azure-resource-manager/).
- Чтобы просмотреть схемы шаблонов Azure Resource Manager, см. в разделе [Справочник по шаблонам](/azure/templates/).