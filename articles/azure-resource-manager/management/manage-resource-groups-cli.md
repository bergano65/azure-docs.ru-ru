---
title: Управление группами ресурсов - Azure CLI
description: Используйте Azure CLI для управления группами ресурсов через azure Resource Manager. Показывает, как создавать, перечислять и удалять группы ресурсов.
author: mumian
ms.topic: conceptual
ms.date: 02/11/2019
ms.author: jgao
ms.openlocfilehash: 7face572f545153ea92efbdb345bbaabda5dd126
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79248337"
---
# <a name="manage-azure-resource-manager-resource-groups-by-using-azure-cli"></a>Управление группами ресурсов Ресурсов Azure с помощью Azure CLI

Узнайте, как использовать Azure CLI с [помощью менеджера ресурсов Azure](overview.md) для управления группами ресурсов Azure. Для управления ресурсами Azure см. [Управление ресурсами Azure с помощью Azure CLI.](manage-resources-cli.md)

Другие статьи об управлении группами ресурсов:

- [Управление группами ресурсов Azure с помощью портала Azure](manage-resources-portal.md)
- [Управление группами ресурсов Azure с помощью Azure PowerShell](manage-resources-powershell.md)

## <a name="what-is-a-resource-group"></a>Что такое ресурсная группа

Группа ресурсов — это контейнер, содержащий связанные ресурсы для решения Azure. В группу ресурсов могут входить все ресурсы приложения или только те, которыми необходимо управлять совместно. Кроме того, пользователи могут выбрать оптимальный для своей организации способ распределения ресурсов в группах ресурсов. Как правило, ресурсы с общим жизненным циклом добавляют в одну и ту же группу ресурсов, чтобы их можно было легко развертывать, обновлять и удалять в виде группы.

В группе ресурсов хранятся метаданные о ресурсах. Исходя из этого, при указании расположения группы ресурсов вы определяете расположение метаданных. В целях обеспечения соответствия необходимо убедиться, что данные хранятся в определенном регионе.

В группе ресурсов хранятся метаданные о ресурсах. Указывая расположение группы ресурсов, вы определяете расположение метаданных.

## <a name="create-resource-groups"></a>Создание группы ресурсов

Следующий скрипт CLI создает группу ресурсов, а затем показывает группу ресурсов.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
echo "Enter the location (i.e. centralus):" &&
read location &&
az group create --name $resourceGroupName --location $location
```

## <a name="list-resource-groups"></a>Перечислить группы ресурсов

Следующий скрипт CLI перечисляет группы ресурсов по вашей подписке.

```azurecli-interactive
az group list
```

Чтобы получить одну группу ресурсов:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group show --name $resourceGroupName
```

## <a name="delete-resource-groups"></a>Удаление групп ресурсов

Следующий скрипт CLI удаляет группу ресурсов:

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group delete --name $resourceGroupName
```

Для получения дополнительной информации о том, как диспетчер [Azure Resource Manager resource group deletion](delete-resource-group.md)ресурсов Azure заказывает удаление ресурсов, см.

## <a name="deploy-resources-to-an-existing-resource-group"></a>Развертывание ресурсов в существующей группе ресурсов

Просмотрите [ресурсы развертывание в существующей группе ресурсов.](manage-resources-cli.md#deploy-resources-to-an-existing-resource-group)

## <a name="deploy-a-resource-group-and-resources"></a>Развертывание группы ресурсов и ресурсов

Можно создать группу ресурсов и развернуть ресурсы в группу с помощью шаблона «Менеджер ресурсов». См. дополнительные сведения в разделе [Создание группы ресурсов и развертывание ресурсов](../templates/deploy-to-subscription.md#resource-group-and-resources).

## <a name="redeploy-when-deployment-fails"></a>Повторное развертывание при сбое развертывания

Эта функция также известна как *откат по ошибке.* Для получения дополнительной [Redeploy when deployment fails](../templates/rollback-on-error.md)информации см.

## <a name="move-to-another-resource-group-or-subscription"></a>Перемещение в другую группу ресурсов или подписку

Можно переместить ресурсы в группе в другую группу ресурсов. Для получения дополнительной информации [см.](manage-resources-cli.md#move-resources)

## <a name="lock-resource-groups"></a>Блокировка групп ресурсов

Блокировка предотвращает случайное исключение или изменение критически важных ресурсов, таких как подписка Azure, группа ресурсов или ресурс. 

Следующий скрипт блокирует группу ресурсов, поэтому группа ресурсов не может быть удалена.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az lock create --name LockGroup --lock-type CanNotDelete --resource-group $resourceGroupName  
```

Следующий скрипт получает все блокировки для группы ресурсов:

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

Для получения дополнительной информации смотрите [ресурсы блокировки с менеджером ресурсов Azure.](lock-resources.md)

## <a name="tag-resource-groups"></a>Группы ресурсов тегов

К ресурсам и их группам можно добавлять теги. Это позволяет логически их упорядочивать. Для получения [информации см. «Использование тегов для организации ресурсов Azure.](tag-resources.md#azure-cli)

## <a name="export-resource-groups-to-templates"></a>Экспорт групп ресурсов в шаблоны

После успешной настройки группы ресурсов можно просмотреть шаблон «Менеджер ресурсов» для группы ресурсов. Экспорт шаблона обеспечивает два преимущества:

- Автоматизировать будущие развертывания решения, поскольку шаблон содержит всю инфраструктуру.
- Изучите синтаксис шаблонов, взглянув на нотацию объектов JavaScript (JSON), которая представляет ваше решение.

```azurecli-interactive
echo "Enter the Resource Group name:" &&
read resourceGroupName &&
az group export --name $resourceGroupName  
```

Скрипт отображает шаблон на консоли.  Скопируйте код JSON и сохраните его как файл.

Функция шаблона экспорта не поддерживает экспорт ресурсов Azure Data Factory. Чтобы узнать о том, как можно экспортировать ресурсы Data Factory, [см.](https://aka.ms/exportTemplateViaAdf)

Для экспорта ресурсов, созданных с помощью классической модели развертывания, необходимо [перенести их в модель развертывания диспетчера ресурсов.](https://aka.ms/migrateclassicresourcetoarm)

Для получения дополнительной информации на [портале Azure смотрите единый и многоресурсный экспорт шаблонов.](../templates/export-template-portal.md)

## <a name="manage-access-to-resource-groups"></a>Управление доступом к группам ресурсов

[Управление доступом на основе ролей (RBAC)](../../role-based-access-control/overview.md) — это способ управления доступом к ресурсам в Azure. Для получения дополнительной информации [см. Управление доступом с помощью RBAC и Azure CLI](../../role-based-access-control/role-assignments-cli.md).

## <a name="next-steps"></a>Дальнейшие действия

- Чтобы узнать менеджер ресурсов Azure, [пересмотрите обзор менеджера ресурсов Azure.](overview.md)
- Чтобы узнать синтаксис шаблона управления ресурсами, [см.](../templates/template-syntax.md)
- Чтобы узнать, как разрабатывать шаблоны, смотрите [пошаговые учебники](/azure/azure-resource-manager/).
- Для просмотра схем шаблонов шаблонов Azure Resource Manager [см.](/azure/templates/)