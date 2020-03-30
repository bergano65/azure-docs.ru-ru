---
title: Управление группами ресурсов - Azure PowerShell
description: Используйте Azure PowerShell для управления группами ресурсов через Azure Resource Manager. Показывает, как создавать, перечислять и удалять группы ресурсов.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 25e001927d5c06b4a7e5639faaa054ae18b12bb7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248363"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Управление группами ресурсов Ресурсов Azure с помощью Azure PowerShell

Узнайте, как использовать Azure PowerShell с [помощью менеджера ресурсов Azure](overview.md) для управления группами ресурсов Azure. Для управления ресурсами Azure см. [Управление ресурсами Azure с помощью Azure PowerShell.](manage-resources-powershell.md)

Другие статьи об управлении группами ресурсов:

- [Управление группами ресурсов Azure с помощью портала Azure](manage-resources-portal.md)
- [Управление группами ресурсов Azure с помощью Azure CLI](manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Что такое ресурсная группа

Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. В группу ресурсов могут входить все ресурсы приложения или только те, которыми необходимо управлять совместно. Кроме того, пользователи могут выбрать оптимальный для своей организации способ распределения ресурсов в группах ресурсов. Как правило, ресурсы с общим жизненным циклом добавляют в одну и ту же группу ресурсов, чтобы их можно было легко развертывать, обновлять и удалять в виде группы.

В группе ресурсов хранятся метаданные о ресурсах. Таким образом, указывая расположение группы ресурсов, вы определяете расположение метаданных. В целях обеспечения соответствия необходимо убедиться, что данные хранятся в определенном регионе.

В группе ресурсов хранятся метаданные о ресурсах. Указывая расположение группы ресурсов, вы определяете расположение метаданных.

## <a name="create-resource-groups"></a>Создание группы ресурсов

Следующий скрипт PowerShell создает группу ресурсов, а затем показывает группу ресурсов.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"

New-AzResourceGroup -Name $resourceGroupName -Location $location

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="list-resource-groups"></a>Перечислить группы ресурсов

Следующий сценарий PowerShell перечисляет группы ресурсов по вашей подписке.

```azurepowershell-interactive
Get-AzResourceGroup
```

Чтобы получить одну группу ресурсов:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceGroup -Name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Удаление групп ресурсов

Следующий скрипт PowerShell удаляет группу ресурсов:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Remove-AzResourceGroup -Name $resourceGroupName
```

Для получения дополнительной информации о том, как диспетчер [Azure Resource Manager resource group deletion](delete-resource-group.md)ресурсов Azure заказывает удаление ресурсов, см.

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующей группе ресурсов

Просмотрите [ресурсы развертывание в существующей группе ресурсов.](manage-resources-powershell.md#deploy-resources-to-an-existing-resource-group)

Для проверки развертывания группы ресурсов [см.](https://docs.microsoft.com/powershell/module/Az.Resources/Test-AzResourceGroupDeployment?view=azps-1.3.0)

## <a name="deploy-a-resource-group-and-resources"></a>Развертывание группы ресурсов и ресурсов

Можно создать группу ресурсов и развернуть ресурсы в группу с помощью шаблона «Менеджер ресурсов». См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Эта функция также известна как *откат по ошибке.* Для получения дополнительной [Redeploy when deployment fails](../templates/rollback-on-error.md)информации см.

## <a name="move-to-another-resource-group-or-subscription"></a>Перемещение в другую группу ресурсов или подписку

Можно переместить ресурсы в группе в другую группу ресурсов. Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

## <a name="lock-resource-groups"></a>Блокировка групп ресурсов

Блокировка предотвращает случайное исключение или изменение критически важных ресурсов, таких как подписка Azure, группа ресурсов или ресурс. 

Следующий скрипт блокирует группу ресурсов, поэтому группа ресурсов не может быть удалена.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

New-AzResourceLock -LockName LockGroup -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName 
```

Следующий скрипт получает все блокировки для группы ресурсов:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName 
```

Для получения дополнительной информации смотрите [ресурсы блокировки с менеджером ресурсов Azure.](lock-resources.md)

## <a name="tag-resource-groups"></a>Группы ресурсов тегов

К ресурсам и их группам можно добавлять теги. Это позволяет логически их упорядочивать. Для получения [информации см. «Использование тегов для организации ресурсов Azure.](tag-resources.md#powershell)

## <a name="export-resource-groups-to-templates"></a>Экспорт групп ресурсов в шаблоны

После настройки группы ресурсов можно просмотреть шаблон менеджера ресурсов для группы ресурсов. Экспорт шаблона обеспечивает два преимущества:

- Автоматизировать будущие развертывания решения, поскольку шаблон содержит полную инфраструктуру.
- Изучите синтаксис шаблонов, взглянув на нотацию объектов JavaScript (JSON), которая представляет ваше решение.

Для экспорта всех ресурсов в группе ресурсов воспользуйтесь cmdlet [Export-AzResourceGroup](/powershell/module/az.resources/Export-AzResourceGroup) и предоставьте название группы ресурсов.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Это сохраняет шаблон как локальный файл.

Вместо экспорта всех ресурсов в группе ресурсов можно выбрать, какие ресурсы экспортировать.

Чтобы экспортировать один ресурс, передайте идентификатор ресурса.

```azurepowershell-interactive
$resource = Get-AzResource `
  -ResourceGroupName <resource-group-name> `
  -ResourceName <resource-name> `
  -ResourceType <resource-type>
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource $resource.ResourceId
```

Чтобы экспортировать несколько ресурсов, передайте итоговые данные ресурса в массиве.

```azurepowershell-interactive
Export-AzResourceGroup `
  -ResourceGroupName <resource-group-name> `
  -Resource @($resource1.ResourceId, $resource2.ResourceId)
```

При экспорте шаблона можно указать, используются ли параметры в шаблоне. По умолчанию параметры для имен ресурсов включены, но они не имеют значения по умолчанию. Во время развертывания необходимо передать значение параметра.

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

Если при `-IncludeParameterDefaultValue` экспорте шаблона параметр шаблона используется при экспорте, то параметр шаблона включает значение по умолчанию, которое устанавливается для текущего значения. Вы можете использовать это значение по умолчанию или перезаписать значение по умолчанию, перейдя в другом значении.

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

Если при `-SkipResourceNameParameterization` экспорте шаблона параметр используется, параметры имен ресурсов не включены в шаблон. Вместо этого имя ресурса устанавливается непосредственно на ресурсе к его текущему значению. Вы не можете настроить имя во время развертывания.

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

Функция шаблона экспорта не поддерживает экспорт ресурсов Azure Data Factory. Чтобы узнать о том, как можно экспортировать ресурсы Data Factory, [см.](https://aka.ms/exportTemplateViaAdf)

Для экспорта ресурсов, созданных с помощью классической модели развертывания, необходимо [перенести их в модель развертывания диспетчера ресурсов.](https://aka.ms/migrateclassicresourcetoarm)

Для получения дополнительной информации на [портале Azure смотрите единый и многоресурсный экспорт шаблонов.](../templates/export-template-portal.md)

## <a name="manage-access-to-resource-groups"></a>Управление доступом к группам ресурсов

[Управление доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Для получения дополнительной информации смотрите [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать менеджер ресурсов Azure, [пересмотрите обзор менеджера ресурсов Azure.](overview.md)
- Чтобы узнать синтаксис шаблона управления ресурсами, [см.](../templates/template-syntax.md)
- Чтобы узнать, как разрабатывать шаблоны, смотрите [пошаговые учебники](/azure/azure-resource-manager/).
- Для просмотра схем шаблонов шаблонов Azure Resource Manager [см.](/azure/templates/)