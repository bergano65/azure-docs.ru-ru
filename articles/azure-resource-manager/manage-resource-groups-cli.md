---
title: Управление группами Azure Resource Manager с помощью интерфейса командной строки Azure | Документация Майкрософт
description: Используйте Azure CLI для управления группами Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
ms.service: azure-resource-manager
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: c50a96b2598b89d5072a9441162d198163156c8d
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/20/2019
ms.locfileid: "67296268"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Управление группами ресурсов Azure Resource Manager с помощью Azure CLI

Узнайте, как использовать Azure CLI с помощью [Azure Resource Manager](resource-group-overview.md) для управления группами ресурсов Azure. Для управления ресурсами Azure, см. в разделе [управление ресурсами Azure с помощью Azure CLI](./manage-resources-cli.md).

Другие статьи об управлении группами ресурсов:

- [Управление группами ресурсов Azure с помощью портала Azure](./manage-resources-portal.md)
- [Управление группами ресурсов Azure с помощью Azure PowerShell](./manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Что такое группа ресурсов

Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. В группу ресурсов могут входить все ресурсы приложения или только те, которыми необходимо управлять совместно. Кроме того, пользователи могут выбрать оптимальный для своей организации способ распределения ресурсов в группах ресурсов. Как правило, ресурсы с общим жизненным циклом добавляют в одну и ту же группу ресурсов, чтобы их можно было легко развертывать, обновлять и удалять в виде группы.

В группе ресурсов хранятся метаданные о ресурсах. Исходя из этого, при указании расположения группы ресурсов вы определяете расположение метаданных. В целях обеспечения соответствия необходимо убедиться, что данные хранятся в определенном регионе.

В группе ресурсов хранятся метаданные о ресурсах. При указании расположения группы ресурсов, вы указываете, где хранятся эти метаданные.

## <a name="create-resource-groups"></a>Создание группы ресурсов

Следующий сценарий CLI создает группу ресурсов и затем отображает группы ресурсов.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Перечислить группы ресурсов

Следующий сценарий CLI выводит группы ресурсов в подписке.

```azurecli-interactive
az group list
```

Чтобы получить одну группу ресурсов:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Удаление группы ресурсов

Следующий сценарий CLI удаляет группу ресурсов:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Дополнительные сведения о как упорядочивает удаление ресурсов в Azure Resource Manager, см. в разделе [удаление группы ресурсов Azure Resource Manager](./resource-group-delete.md).

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующую группу ресурсов

См. в разделе [развертывание ресурсов в существующую группу ресурсов](./manage-resources-cli.md#deploy-resources-to-an-existing-resource-group).

## <a name="deploy-a-resource-group-and-resources"></a>Развернуть группу ресурсов и ресурсы

Можно создать группу ресурсов и развернуть ресурсы в группу с помощью шаблона Resource Manager. См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](./deploy-to-subscription.md#create-resource-group-and-deploy-resources).

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Эта функция также называется *отката в случае ошибки*. Дополнительные сведения см. в разделе [повторное развертывание при сбое развертывания](./resource-group-template-deploy-cli.md#redeploy-when-deployment-fails).

## <a name="move-to-another-resource-group-or-subscription"></a>Переместить в другую группу ресурсов или подписку

Ресурсы в группе можно переместить в другую группу ресурсов. Дополнительные сведения см. в разделе [перемещение ресурсов](./manage-resources-cli.md#move-resources).

## <a name="lock-resource-groups"></a>Блокировки группы ресурсов

Блокировка запрещает другим пользователям в организации случайно удалить или изменить критически важные ресурсы, такие как подписки Azure, группу ресурсов или ресурс. 

Следующий сценарий блокирует группу ресурсов, поэтому невозможно удалить группу ресурсов.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Следующий сценарий получает все блокировки для группы ресурсов:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock list --resource-group $resourceGroupName  
```

Следующий скрипт удаляет блокировку:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the lock name:" &&
read lockName &&
az lock delete --name $lockName --resource-group $resourceGroupName
```

Дополнительные сведения см. в статье [Блокировка ресурсов с помощью диспетчера ресурсов Azure](resource-group-lock-resources.md).

## <a name="tag-resource-groups"></a>Тег группы ресурсов

К ресурсам и их группам можно добавлять теги. Это позволяет логически их упорядочивать. Сведения см. в разделе [использование тегов для организации ресурсов в Azure](./resource-group-using-tags.md#azure-cli).

## <a name="export-resource-groups-to-templates"></a>Экспорт групп ресурсов в шаблонах

После успешной настройки группы ресурсов, может возникнуть необходимость просмотреть шаблон Resource Manager для группы ресурсов. Экспорт шаблона обеспечивает два преимущества:

- Автоматизируйте будущие развертывания решения, так как шаблон содержит полноценную инфраструктуру.
- Узнайте синтаксис шаблона путем поиска в в объект нотацию JavaScript (JSON) своего решения.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Сценарий отображает шаблон на консоли.  Скопируйте код JSON и сохраните его как файл.

Дополнительные сведения см. в разделе [экспорта одной или нескольких ресурсов в шаблон на портале Azure](./export-template-portal.md).

## <a name="manage-access-to-resource-groups"></a>Управление доступом к группам ресурсов

[Управление доступом на основе ролей (RBAC)](../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Дополнительные сведения см. в разделе [управление доступом с помощью RBAC и Azure CLI](../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать, Azure Resource Manager, см. в разделе [обзоре Azure Resource Manager](./resource-group-overview.md).
- Чтобы узнать синтаксис шаблона Resource Manager, см. в разделе [описание структуры и синтаксиса шаблонов Azure Resource Manager](./resource-group-authoring-templates.md).
- Чтобы научиться разрабатывать шаблоны, см. в разделе [пошаговые учебники](/azure/azure-resource-manager/).
- Чтобы просмотреть схемы шаблонов Azure Resource Manager, см. в разделе [Справочник по шаблонам](/azure/templates/).