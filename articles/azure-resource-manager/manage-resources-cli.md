---
title: Управлять ресурсами Azure с помощью интерфейса командной строки Azure | Документация Майкрософт
description: Используйте Azure CLI и Azure Resource Manager для управления ресурсами.
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
ms.openlocfilehash: 076c57f5415a4f6f19252fb5a3546e5e9a8a23f4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60550252"
---
# <a name="manage-azure-resources-by-using-azure-cli"></a>Управлять ресурсами Azure с помощью Azure CLI

Узнайте, как использовать Azure CLI с помощью [Azure Resource Manager](resource-group-overview.md) для управления ресурсами Azure. Для управления группами ресурсов, см. в разделе [групп ресурсов Azure, управлять ими с помощью Azure CLI](./manage-resource-groups-cli.md).

Другие статьи об управлении ресурсами:

- [Управлять ресурсами Azure с помощью портала Azure](./manage-resources-portal.md)
- [Управлять ресурсами Azure с помощью Azure PowerShell](./manage-resources-powershell.md)

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующую группу ресурсов

Развертывание ресурсов Azure с помощью Azure PowerShell или развернуть шаблон Resource Manager для создания ресурсов Azure.

### <a name="deploy-a-resource"></a>Развертывание ресурса

Следующий скрипт создает учетную запись хранения.

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

Следующий скрипт создает развертывание шаблона быстрого запуска для создания учетной записи хранения. Дополнительные сведения см. в разделе [ Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md?tabs=PowerShell).

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group deployment create --resource-group $resourceGroupName --template-uri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json"
```

Дополнительные сведения см. в статье [Развертывание ресурсов с использованием шаблонов Resource Manager и Azure CLI](./resource-group-template-deploy-cli.md).

## <a name="deploy-a-resource-group-and-resources"></a>Развернуть группу ресурсов и ресурсы

Можно создать группу ресурсов и развернуть ресурсы в группе. См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="deploy-resources-to-multiple-subscriptions-or-resource-groups"></a>Развертывание ресурсов в нескольких подписках или группах ресурсов

Обычно развертывание всех ресурсов в шаблоне выполняется в отдельную группу ресурсов. Тем не менее возможны ситуации, когда необходимо развернуть набор ресурсов одновременно, но при этом разместить их в отдельных подписках или группах ресурсов. Дополнительные сведения см. в разделе [развертывание ресурсов Azure в нескольких подписках или группах ресурсов](./resource-manager-cross-resource-group-deployment.md).

## <a name="delete-resources"></a>Удаление ресурсов.

Следующий сценарий показывает, как удалить учетную запись хранения.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az storage account delete --resource-group $resourceGroupName --name $storageAccountName 
```

Дополнительные сведения о как упорядочивает удаление ресурсов в Azure Resource Manager, см. в разделе [удаление группы ресурсов Azure Resource Manager](./resource-group-delete.md).

## <a name="move-resources"></a>Перемещение ресурсов

Следующий сценарий показывает, как удалить учетную запись хранения из одной группы ресурсов в другую группу ресурсов.

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

Дополнительные сведения см. в статье [Перемещение ресурсов в новую группу ресурсов или подписку](resource-group-move-resources.md).

## <a name="lock-resources"></a>Блокировка ресурсов

Блокировка запрещает другим пользователям в организации случайно удалить или изменить критически важные ресурсы, такие как подписки Azure, группу ресурсов или ресурс. 

Следующий сценарий блокирует учетную запись хранения, чтобы учетная запись не может быть удалена.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock create --name LockSite --lock-type CanNotDelete --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts 
```

Следующий сценарий получает все блокировки для учетной записи хранения:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
az lock list --resource-group $resourceGroupName --resource-name $storageAccountName --resource-type Microsoft.Storage/storageAccounts --parent ""
```

Следующий скрипт удаляет блокировку учетной записи хранения:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the storage account name:" &&
read storageAccountName &&
lockId=$(az lock show --name LockSite --resource-group $resourceGroupName --resource-type Microsoft.Storage/storageAccounts --resource-name $storageAccountName --output tsv --query id)&&
az lock delete --ids $lockId
```

Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="tag-resources"></a>Добавление тегов к ресурсам

Добавление тегов позволяет логически Упорядочить группу ресурсов и ресурсы. Сведения см. в разделе [использование тегов для организации ресурсов в Azure](./resource-group-using-tags.md#azure-cli).

## <a name="manage-access-to-resources"></a>Управление доступом к ресурсам

[Управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Дополнительные сведения см. в разделе [управление доступом с помощью RBAC и Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, Azure Resource Manager, см. в разделе [обзоре Azure Resource Manager](./resource-group-overview.md).
- Чтобы узнать синтаксис шаблона Resource Manager, см. в разделе [описание структуры и синтаксиса шаблонов Azure Resource Manager](./resource-group-authoring-templates.md).
- Чтобы научиться разрабатывать шаблоны, см. в разделе [пошаговые учебники](/azure/azure-resource-manager/).
- Чтобы просмотреть схемы шаблонов Azure Resource Manager, см. в разделе [Справочник по шаблонам](/azure/templates/).
