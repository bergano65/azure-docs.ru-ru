---
title: Управление ресурсами - Azure PowerShell
description: Для управления ресурсами используйте менеджер ресурсов Azure PowerShell и Azure. Показывает, как развертывать и удалять ресурсы.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: de6f24ea66ef41b5ee4bfdda5948de9639f10a51
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485407"
---
# <a name="manage-azure-resources-by-using-azure-powershell"></a>Управление ресурсами Azure с помощью Azure PowerShell

Узнайте, как использовать Azure PowerShell с [помощью менеджера ресурсов Azure](overview.md) для управления ресурсами Azure. Для управления группами ресурсов [см. Управление группами ресурсов Azure с помощью Azure PowerShell.](manage-resource-groups-powershell.md)

Другие статьи об управлении ресурсами:

- [Управление ресурсами Azure с помощью портала Azure](manage-resources-portal.md)
- [Управление ресурсами Azure с помощью Azure CLI](manage-resources-cli.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующей группе ресурсов

Вы можете развертывать ресурсы Azure непосредственно с помощью Azure PowerShell или развертывать шаблон управления ресурсами для создания ресурсов Azure.

### <a name="deploy-a-resource"></a>Развертывание ресурса

Следующий скрипт создает учетную запись для хранения данных.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

# Create the storage account.
$storageAccount = New-AzStorageAccount -ResourceGroupName $resourceGroupName `
  -Name $storageAccountName `
  -Location $location `
  -SkuName "Standard_LRS"

# Retrieve the context.
$ctx = $storageAccount.Context
```

### <a name="deploy-a-template"></a>Развертывание шаблона

Следующий скрипт создает шаблон «Быстрый пуск» для создания учетной записи хранилища. Для получения дополнительной информации [см.](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$location = Read-Host -Prompt "Enter the location (i.e. centralus)"
$templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
New-AzResourceGroupDeployment -ResourceGroupName $resourceGroupName -TemplateUri $templateUri -Location $location
```

Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure PowerShell](../templates/deploy-powershell.md).

## <a name="deploy-a-resource-group-and-resources"></a>Развертывание группы ресурсов и ресурсов

Можно создать группу ресурсов и развернуть ресурсы в группу. См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Развертывание ресурсов в нескольких подписок или групп ресурсов

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Для получения дополнительной информации [перечню развертывание ресурсов Azure для нескольких подписок или групп ресурсов.](../templates/cross-resource-group-deployment.md)

## <a name="delete-resources"></a>Удаление ресурсов

На следующем скрипте показано, как удалить учетную запись хранения.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Remove-AzStorageAccount -ResourceGroupName $resourceGroupName -AccountName $storageAccountName
```

Для получения дополнительной информации о том, как диспетчер [Azure Resource Manager resource group deletion](delete-resource-group.md)ресурсов Azure заказывает удаление ресурсов, см.

## <a name="move-resources"></a>Перемещение ресурсов

В следующем скрипте показано, как удалить учетную запись хранилища из одной группы ресурсов в другую группу ресурсов.

```azurepowershell-interactive
$srcResourceGroupName = Read-Host -Prompt "Enter the source Resource Group name"
$destResourceGroupName = Read-Host -Prompt "Enter the destination Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$storageAccount = Get-AzResource -ResourceGroupName $srcResourceGroupName -ResourceName $storageAccountName
Move-AzResource -DestinationResourceGroupName $destResourceGroupName -ResourceId $storageAccount.ResourceId
```

Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Блокировка ресурсов

Блокировка предотвращает случайное исключение или изменение критически важных ресурсов, таких как подписка Azure, группа ресурсов или ресурс. 

Следующий скрипт блокирует учетную запись хранения, поэтому учетная запись не может быть удалена.

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

New-AzResourceLock -LockName LockStorage -LockLevel CanNotDelete -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts 
```

Следующий скрипт получает все блокировки для учетной записи хранилища:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts
```

Следующий скрипт удаляет блокировку учетной записи хранилища:

```azurepowershell-interactive
$resourceGroupName = Read-Host -Prompt "Enter the Resource Group name"
$storageAccountName = Read-Host -Prompt "Enter the storage account name"

$lockId = (Get-AzResourceLock -ResourceGroupName $resourceGroupName -ResourceName $storageAccountName -ResourceType Microsoft.Storage/storageAccounts).LockId
Remove-AzResourceLock -LockId $lockId
```

Для получения дополнительной информации смотрите [ресурсы блокировки с менеджером ресурсов Azure.](lock-resources.md)

## <a name="tag-resources"></a>Добавление тегов к ресурсам

Метки помогают логически организовать группу ресурсов и ресурсы. Для получения [информации см. «Использование тегов для организации ресурсов Azure.](tag-resources.md#powershell)

## <a name="manage-access-to-resources"></a>Управление доступом к ресурсам

[Управление доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Для получения дополнительной информации смотрите [Управление доступом с помощью RBAC и Azure PowerShell](../../role-based-access-control/role-assignments-powershell.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать менеджер ресурсов Azure, [пересмотрите обзор менеджера ресурсов Azure.](overview.md)
- Чтобы узнать синтаксис шаблона управления ресурсами, [см.](../templates/template-syntax.md)
- Чтобы узнать, как разрабатывать шаблоны, смотрите [пошаговые учебники](/azure/azure-resource-manager/).
- Для просмотра схем шаблонов шаблонов Azure Resource Manager [см.](/azure/templates/)
