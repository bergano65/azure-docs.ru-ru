---
title: Управление ресурсами - Azure CLI
description: Для управления ресурсами используйте Azure CLI и менеджер ресурсов Azure. Показывает, как развертывать и удалять ресурсы.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: f00c1725201b0e49f80fec64e5d69b375ec7a233
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75485537"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Управление ресурсами Azure с помощью Azure CLI

Узнайте, как использовать Azure CLI с [помощью менеджера ресурсов Azure](overview.md) для управления ресурсами Azure. Для управления группами ресурсов см. [Управление группами ресурсов Azure с помощью Azure CLI.](manage-resource-groups-cli.md)

Другие статьи об управлении ресурсами:

- [Управление ресурсами Azure с помощью портала Azure](manage-resources-portal.md)
- [Управление ресурсами Azure с помощью Azure PowerShell](manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующей группе ресурсов

Можно развертывать ресурсы Azure непосредственно с помощью Azure CLI или развертывать шаблон управления ресурсами для создания ресурсов Azure.

### <a name="deploy-a-resource"></a>Развертывание ресурса

Следующий скрипт создает учетную запись для хранения данных.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account create --resource-group $resourceGroupName --name $storageAccountName --location $location --sku Standard_LRS --kind StorageV2 &&
az storage account show --resource-group $resourceGroupName --name $storageAccountName 
```

### <a name="deploy-a-template"></a>Развертывание шаблона

Следующий скрипт создает шаблон «Быстрый пуск» для создания учетной записи хранилища. Для получения дополнительной информации [см.](../templates/quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell)

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](../templates/deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Развертывание группы ресурсов и ресурсов

Можно создать группу ресурсов и развернуть ресурсы в группу. См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Развертывание ресурсов в нескольких подписок или групп ресурсов

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Для получения дополнительной информации [перечню развертывание ресурсов Azure для нескольких подписок или групп ресурсов.](../templates/cross-resource-group-deployment.md)

## <a name="delete-resources"></a>Удаление ресурсов

На следующем скрипте показано, как удалить учетную запись хранения.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Для получения дополнительной информации о том, как диспетчер [Azure Resource Manager resource group deletion](delete-resource-group.md)ресурсов Azure заказывает удаление ресурсов, см.

## <a name="move-resources"></a>Перемещение ресурсов

В следующем скрипте показано, как удалить учетную запись хранилища из одной группы ресурсов в другую группу ресурсов.

```azurecli-interactive
echo "Enter the source Resource Group name:" &&
read srcResourceGroupName &&
echo "Enter the destination Resource Group name:" &&
read destResourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
storageAccount=$(az resource show --resource-group $srcResourceGroupName --name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --query id --output tsv) &&
az resource move --destination-group $destResourceGroupName --ids $storageAccount
```

Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](move-resource-group-and-subscription.md).

## <a name="lock-resources"></a>Блокировка ресурсов

Блокировка предотвращает случайное исключение или изменение критически важных ресурсов, таких как подписка Azure, группа ресурсов или ресурс. 

Следующий скрипт блокирует учетную запись хранения, поэтому учетная запись не может быть удалена.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Следующий скрипт получает все блокировки для учетной записи хранилища:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Следующий скрипт удаляет блокировку учетной записи хранилища:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Для получения дополнительной информации смотрите [ресурсы блокировки с менеджером ресурсов Azure.](lock-resources.md)

## <a name="tag-resources"></a>Добавление тегов к ресурсам

Метки помогают логически организовать группу ресурсов и ресурсы. Для получения [информации см. «Использование тегов для организации ресурсов Azure.](tag-resources.md#azure-cli)

## <a name="manage-access-to-resources"></a>Управление доступом к ресурсам

[Управление доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Для получения дополнительной информации [см. Управление доступом с помощью RBAC и Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать менеджер ресурсов Azure, [пересмотрите обзор менеджера ресурсов Azure.](overview.md)
- Чтобы узнать синтаксис шаблона управления ресурсами, [см.](../templates/template-syntax.md)
- Чтобы узнать, как разрабатывать шаблоны, смотрите [пошаговые учебники](/azure/azure-resource-manager/).
- Для просмотра схем шаблонов шаблонов Azure Resource Manager [см.](/azure/templates/)
