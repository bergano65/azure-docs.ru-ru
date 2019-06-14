---
title: Управление группами Azure Resource Manager с помощью Azure PowerShell | Документация Майкрософт
description: Используйте Azure PowerShell для управления группами Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 8ae86d8bc7914a7a9c41eee93bb16b2f774993b9
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550501"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-powershell"></a>Управление группами ресурсов Azure Resource Manager с помощью Azure PowerShell

Узнайте, как использовать Azure PowerShell с [Azure Resource Manager](resource-group-overview.md) для управления группами ресурсов Azure. Для управления ресурсами Azure, см. в разделе [управление ресурсами Azure с помощью Azure PowerShell](./manage-resources-powershell.md).

Другие статьи об управлении группами ресурсов:

- [Управление группами ресурсов Azure с помощью портала Azure](./manage-resources-portal.md)
- [Управление группами ресурсов Azure с помощью Azure CLI](./manage-resources-cli.md)

## <a name="what-is-a-resource-group"></a>Что такое группа ресурсов

Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. В группу ресурсов могут входить все ресурсы приложения или только те, которыми необходимо управлять совместно. Кроме того, пользователи могут выбрать оптимальный для своей организации способ распределения ресурсов в группах ресурсов. Как правило, ресурсы с общим жизненным циклом добавляют в одну и ту же группу ресурсов, чтобы их можно было легко развертывать, обновлять и удалять в виде группы.

В группе ресурсов хранятся метаданные о ресурсах. Исходя из этого, при указании расположения группы ресурсов вы определяете расположение метаданных. В целях обеспечения соответствия необходимо убедиться, что данные хранятся в определенном регионе.

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

После успешной настройки группы ресурсов, может возникнуть необходимость просмотреть шаблон Resource Manager для группы ресурсов. Экспорт шаблона обеспечивает два преимущества:

- Автоматизируйте будущие развертывания решения, так как шаблон содержит полноценную инфраструктуру.
- Узнайте синтаксис шаблона путем поиска в в объект нотацию JavaScript (JSON) своего решения.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"

Export-AzResourceGroup -ResourceGroupName $resourceGroupName
```

Дополнительные сведения см. в разделе [Экспорт группы ресурсов](./manage-resource-groups-portal.md#export-resource-groups-to-templates).

## <a name="manage-access-to-resource-groups"></a>Управление доступом к группам ресурсов

[Управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Дополнительные сведения см. в разделе [управление доступом с помощью Azure PowerShell и RBAC](../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, Azure Resource Manager, см. в разделе [обзоре Azure Resource Manager](./resource-group-overview.md).
- Чтобы узнать синтаксис шаблона Resource Manager, см. в разделе [описание структуры и синтаксиса шаблонов Azure Resource Manager](./resource-group-authoring-templates.md).
- Чтобы научиться разрабатывать шаблоны, см. в разделе [пошаговые учебники](/azure/azure-resource-manager/).
- Чтобы просмотреть схемы шаблонов Azure Resource Manager, см. в разделе [Справочник по шаблонам](/azure/templates/).