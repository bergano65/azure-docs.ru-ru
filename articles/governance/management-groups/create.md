---
title: Создание групп управления для упорядочения ресурсов Azure. Система управления Azure
description: Узнайте, как создавать группы управления Azure для управления множеством ресурсов с помощью портала, Azure PowerShell и Azure CLI.
author: rthorn17
manager: rithorn
ms.service: governance
ms.devlang: na
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/05/2019
ms.author: rithorn
ms.topic: conceptual
ms.openlocfilehash: f34efbbca1616c75c13ee3a8bf73bbee1e66dc92
ms.sourcegitcommit: 6794fb51b58d2a7eb6475c9456d55eb1267f8d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70241161"
---
# <a name="create-management-groups-for-resource-organization-and-management"></a>Создание групп управления для упорядочения ресурсов и управления ими

Группы управления — это контейнеры, которые помогают управлять доступом, политикой и соответствием требованиям в нескольких подписках. Создание таких контейнеров позволяет построить эффективную и экономную иерархию, которую можно использовать с [политикой Azure](../policy/overview.md) и [элементами управления доступом на основе ролей Azure](../../role-based-access-control/overview.md). Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](overview.md).

Создание первой группы управления в каталоге может занять до 15 минут. Существуют процессы, выполняемые в первый раз при настройке службы групп управления в Azure для вашего каталога. По завершении процесса вы получите уведомление.

## <a name="create-a-management-group"></a>Создание группы управления

Группу управления можно создать с помощью портала, PowerShell или Azure CLI. В настоящее время нельзя использовать шаблоны Resource Manager для создания групп управления.

### <a name="create-in-portal"></a>Создание на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. На главной странице выберите **Создать группу управления**.

   ![Страница для работы с группами управления](./media/main.png)

1. Заполните поле идентификатора группы управления.

   - **Идентификатор группы управления** — уникальный идентификатор каталога, используемый для отправки команд в этой группе управления. Идентификатор не редактируется после ее создания, так как с его помощью эта группа идентифицируется во всей системе Azure. [Корневая группа управления](index.md#root-management-group-for-each-directory) создается автоматически с идентификатором, который является идентификатором Azure Active Directory. Для всех остальных групп управления назначьте уникальный идентификатор.
   - Поле отображаемого имени — это имя, которое отображается во всех разделах портала Azure. Отдельное отображаемое имя можно указать в необязательном поле при создании группы управления и изменить в любой момент.  

   ![Область параметров для создания новой группы управления](./media/create_context_menu.png)  

1. Щелкните **Сохранить**.

### <a name="create-in-powershell"></a>Создание в PowerShell

Для PowerShell используйте командлет [New-азманажементграуп](/powershell/module/az.resources/new-azmanagementgroup) , чтобы создать новую группу управления.

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso'
```

**GroupName** — уникальный создаваемый идентификатор. Этот идентификатор используется другими командами для ссылки на эту группу и не может быть изменен позже.

Если требуется, чтобы Группа управления отображала другое имя в портал Azure, добавьте параметр **DisplayName** . Например, чтобы создать группу управления с GroupName Contoso и отображаемым именем группы Contoso, используйте следующий командлет:

```azurepowershell-interactive
New-AzManagementGroup -GroupName 'Contoso' -DisplayName 'Contoso Group'
```

В приведенных выше примерах новая группа управления создается в корневой группе управления. Чтобы указать другую группу управления в качестве родительской, используйте параметр **ParentID** .

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName Contoso
New-AzManagementGroup -GroupName 'ContosoSubGroup' -ParentId $parentGroup.id
```

### <a name="create-in-azure-cli"></a>Создание в Azure CLI

Для Azure CLI используйте команду [AZ Account Management-Group Create](/cli/azure/account/management-group?view=azure-cli-latest#az-account-management-group-create) , чтобы создать новую группу управления.

```azurecli-interactive
az account management-group create --name Contoso
```

**Имя** — это уникальный идентификатор, который создается. Этот идентификатор используется другими командами для ссылки на эту группу и не может быть изменен позже.

Если вы хотите, чтобы Группа управления отображала другое имя в портал Azure, добавьте параметр **отображения-Name** . Например, чтобы создать группу управления с GroupName Contoso и отображаемым именем группы Contoso, используйте следующую команду:

```azurecli-interactive
az account management-group create --name Contoso --display-name 'Contoso Group'
```

В приведенных выше примерах новая группа управления создается в корневой группе управления. Чтобы указать другую группу управления в качестве родительской, используйте параметр **Parent** и укажите имя родительской группы.

```azurecli-interactive
az account management-group create --name ContosoSubGroup --parent Contoso
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о группах управления:

- [Создание групп управления для организации ресурсов Azure](create.md)
- [Изменение, удаление групп управления и управление ими](manage.md)
- [Просмотр групп управления в модуле ресурсов Azure PowerShell](/powershell/module/az.resources#resources)
- [Просмотр групп управления в REST API](/rest/api/resources/managementgroups)
- [Просмотр групп управления в Azure CLI](/cli/azure/account/management-group)