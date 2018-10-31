---
title: Создание групп управления для организации ресурсов Azure
description: Узнайте, как создавать группы управления Azure для управления множеством ресурсов.
author: rthorn17
manager: rithorn
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/10/2018
ms.author: rithorn
ms.openlocfilehash: 6a45ffa13ead40b72fd1a0a3c2696a6e6829a4d5
ms.sourcegitcommit: 5c00e98c0d825f7005cb0f07d62052aff0bc0ca8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2018
ms.locfileid: "49956414"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Создание групп управления для упорядочения ресурсов и управления ими

Группы управления — это контейнеры, которые помогают управлять доступом, политикой и соответствием требованиям в нескольких подписках. Создание таких контейнеров позволяет построить эффективную и экономную иерархию, которую можно использовать с [политикой Azure](../policy/overview.md) и [элементами управления доступом на основе ролей Azure](../../role-based-access-control/overview.md). Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](overview.md).

Создание первой группы управления в каталоге может занять до 15 минут. Существуют процессы, выполняемые в первый раз при настройке службы групп управления в Azure для вашего каталога. По завершении процесса вы получите уведомление.

## <a name="create-a-management-group"></a>Создание группы управления

Группу управления можно создать с помощью портала, PowerShell или Azure CLI. В настоящее время нельзя использовать шаблоны Resource Manager для создания групп управления.

### <a name="create-in-portal"></a>Создание на портале

1. Войдите на [портал Azure](http://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. На главной странице выберите **Создать группу управления**.

   ![Главная группа](./media/main.png)

1. Заполните поле идентификатора группы управления.

   - **Идентификатор группы управления** — уникальный идентификатор каталога, используемый для отправки команд в этой группе управления. Идентификатор не редактируется после ее создания, так как он используется во всей системе Azure для идентификации этой группы.
   - Поле отображаемого имени — это имя, которое отображается во всех разделах портала Azure. Отдельное отображаемое имя можно указать в необязательном поле при создании группы управления и изменить в любой момент.  

   ![Создание](./media/create_context_menu.png)  

1. Щелкните **Сохранить**.

### <a name="create-in-powershell"></a>Создание в PowerShell

В PowerShell используется командлет New-AzureRmManagementGroup.

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso'
```

**GroupName** — уникальный создаваемый идентификатор. Этот идентификатор используется в других командах для ссылки на эту группу, и впоследствии его нельзя изменить.

Если нужно, чтобы для группы управления на портале отображалось другое имя, следует добавить параметр **DisplayName** со строкой. Например, если нужно создать группу управления с именем (GroupName) Contoso и отображаемым именем (DisplayName) "Группа Contoso", необходимо использовать следующий командлет:

```azurepowershell-interactive
New-AzureRmManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group' -ParentId 'ContosoTenant'
```

Используйте параметр **ParentId**, чтобы создать эту группу управления под другим управлением.

### <a name="create-in-azure-cli"></a>Создание в Azure CLI

В Azure CLI используется команда az account management-group create.

```azurecli-interactive
az account management-group create --name 'Contoso'
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о группах управления:

- [Упорядочение ресурсов с помощью групп управления Azure](overview.md)
- [Изменение, удаление групп управления и управление ими](manage.md)
- [Страница для установки модуля Azure PowerShell](https://www.powershellgallery.com/packages/AzureRM.ManagementGroups)
- [Просмотр спецификации REST API](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/managementgroups/resource-manager/Microsoft.Management/preview)
- [Установка расширения Azure CLI](/cli/azure/extension?view=azure-cli-latest#az-extension-list-available)
