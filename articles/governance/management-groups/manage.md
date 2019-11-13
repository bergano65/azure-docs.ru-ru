---
title: Как работать с группами управления — Управление Azure
description: Узнайте, как просматривать, сохранять, обновлять и удалять иерархию групп управления.
ms.date: 05/22/2019
ms.topic: conceptual
ms.openlocfilehash: 90f4bacf462ed5f2590f51d15b6b660057c51738
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73960240"
---
# <a name="manage-your-resources-with-management-groups"></a>Управление ресурсами с помощью групп управления

Если в вашей организации оформлено много подписок, возможно, потребуется повысить эффективность управления доступом, политиками и соответствием требованиям для этих подписок. Группы управления Azure обеспечивают высокий уровень охвата подписок. Подписки упорядочиваются в контейнеры, которые называются группами управления. К ним применяются условия системы управления. Все подписки в группе управления автоматически наследуют условия, применяемые к группе управления.

Группы управления обеспечивают корпоративное управление в больших масштабах независимо от типа подписки.  Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

## <a name="change-the-name-of-a-management-group"></a>Изменение имени группы управления

Имя группы управления можно изменить с помощью портала, PowerShell или Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Изменение имени на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую требуется переименовать.

1. Выберите **сведения**.

1. Выберите параметр **Переименовать группу** в верхней части страницы.

   ![Параметр "переименовать группу" на странице группы управления](./media/detail_action_small.png)

1. Когда откроется меню, введите новое имя.

   ![Переименование области группы для переименования группы управления](./media/rename_context.png)

1. Щелкните **Сохранить**.

### <a name="change-the-name-in-powershell"></a>Изменение имени в PowerShell

Чтобы обновить отображаемое имя, используйте **Update-азманажементграуп**. Например, чтобы изменить отображаемое имя группы управления с "Contoso IT" на "группа Contoso", выполните следующую команду:

```azurepowershell-interactive
Update-AzManagementGroup -GroupName 'ContosoIt' -DisplayName 'Contoso Group'
```

### <a name="change-the-name-in-azure-cli"></a>Изменение имени в Azure CLI

Для Azure CLI используется команда обновления.

```azurecli-interactive
az account management-group update --name 'Contoso' --display-name 'Contoso Group'
```

## <a name="delete-a-management-group"></a>Удаление группы управления

Чтобы удалить группу управления, необходимо выполнить следующие требования:

1. В группе управления нет дочерних групп управления или подписок.

   - Сведения о том, как переместить подписку из группы управления, см. в [этом разделе](#move-subscriptions-in-the-hierarchy).

   - Сведения о перемещении группы управления в другую группу управления см. в [этом разделе](#move-management-groups-in-the-hierarchy).

1. У вас есть разрешения на запись в группу управления ("владелец", "участник" или "участник группы управления"). Чтобы получить сведения об имеющихся разрешениях, выберите группу управления, а затем выберите **IAM**. Дополнительные сведения о ролях RBAC см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../../role-based-access-control/overview.md).  

### <a name="delete-in-the-portal"></a>Удаление на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую требуется удалить.

1. Выберите **сведения**.

1. Выберите **Удалить**.

    > [!TIP]
    > Если значок неактивен, наведите указатель мыши на значок, чтобы узнать причину.

   ![Параметр "Удалить группу"](./media/delete.png)

1. Откроется окно, в котором нужно подтвердить удаление группы управления.

   ![Окно подтверждения удаления группы](./media/delete_confirm.png)

1. Выберите **Да**.

### <a name="delete-in-powershell"></a>Удаление в PowerShell

Удалите группы управления с помощью команды **Remove-азманажементграуп** в PowerShell.

```azurepowershell-interactive
Remove-AzManagementGroup -GroupName 'Contoso'
```

### <a name="delete-in-azure-cli"></a>Удаление в Azure CLI

В Azure CLI используется команда az account management-group delete.

```azurecli-interactive
az account management-group delete --name 'Contoso'
```

## <a name="view-management-groups"></a>Просмотр групп управления

Вы можете просматривать все группы управления, в которых у вас есть прямые или наследуемые роли RBAC.  

### <a name="view-in-the-portal"></a>Просмотр на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Будет загружена страница иерархия группы управления. На этой странице можно просмотреть все группы управления и подписки, к которым у вас есть доступ. При выборе имени группы открывается нижний уровень в иерархии. Навигация работает так же, как и в обозревателе файлов.

1. Чтобы просмотреть сведения о группе управления, перейдите по ссылке **(подробности)** рядом с заголовком группы управления. Если ссылка недоступна, у вас нет разрешения на просмотр этой группы управления.

   ![Главная](./media/main.png)

### <a name="view-in-powershell"></a>Просмотр в PowerShell

Чтобы получить все группы, используйте команду Get-Азманажементграуп.  Полный список команд PowerShell для группы управления см. в разделе [AZ. Resources](/powershell/module/az.resources/Get-AzManagementGroup) modules.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Для получения сведений об одной группе управления используйте параметр -GroupName.

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Чтобы получить определенную группу управления и все уровни иерархии под ней, используйте параметры **-expand** и **-** Recurse.  

```azurepowershell-interactive
PS C:\> $response = Get-AzManagementGroup -GroupName TestGroupParent -Expand -Recurse
PS C:\> $response

Id                : /providers/Microsoft.Management/managementGroups/TestGroupParent
Type              : /providers/Microsoft.Management/managementGroups
Name              : TestGroupParent
TenantId          : 00000000-0000-0000-0000-000000000000
DisplayName       : TestGroupParent
UpdatedTime       : 2/1/2018 11:15:46 AM
UpdatedBy         : 00000000-0000-0000-0000-000000000000
ParentId          : /providers/Microsoft.Management/managementGroups/00000000-0000-0000-0000-000000000000
ParentName        : 00000000-0000-0000-0000-000000000000
ParentDisplayName : 00000000-0000-0000-0000-000000000000
Children          : {TestGroup1DisplayName, TestGroup2DisplayName}

PS C:\> $response.Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestGroup1
Name        : TestGroup1
DisplayName : TestGroup1DisplayName
Children    : {TestRecurseChild}

PS C:\> $response.Children[0].Children[0]

Type        : /managementGroup
Id          : /providers/Microsoft.Management/managementGroups/TestRecurseChild
Name        : TestRecurseChild
DisplayName : TestRecurseChild
Children    :
```

### <a name="view-in-azure-cli"></a>Просмотр в Azure CLI

Для извлечения всех групп используется команда вывода списка.  

```azurecli-interactive
az account management-group list
```

Для получения сведений об одной группе управления используется команда отображения.

```azurecli-interactive
az account management-group show --name 'Contoso'
```

Чтобы получить определенную группу управления и все уровни иерархии под ней, используйте параметры **-expand** и **-** Recurse.

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="move-subscriptions-in-the-hierarchy"></a>Перемещение подписок в иерархии

Одна из причин для создания группы управления — объединение подписок. Дочерним элементом группы управления могут быть только подписки и группы управления. При перемещении в группу управления подписка наследует все права доступа и политики пользователя из родительской группы управления.

Чтобы переместить подписку, необходимо иметь все перечисленные ниже разрешения RBAC.

- Роль "Владелец" в дочерней подписке.
- Роль "владелец", "участник" или "участник группы управления" в целевой родительской группе управления.
- Роль "владелец", "участник" или "участник группы управления" в существующей родительской группе управления.

Если целевой или существующей родительской группой управления является корневая группа управления, требования к разрешениям не применяются. Так как корневая группа управления является назначением по умолчанию для всех новых групп управления и подписок, для перемещения элемента не требуются разрешения.

Если роль владельца подписки наследуется от текущей группы управления, целевые объекты перемещения будут ограничены. Вы можете переместить подписку только в другую группу управления, в которой есть роль владельца. Вы не можете переместить его в группу управления, в которой вы являетесь участником, так как вы потеряли владение подпиской. Если вы непосредственно назначаете роль владельца для подписки (не наследуется от группы управления), ее можно переместить в любую группу управления, в которой вы являетесь участником.

Чтобы узнать, какие разрешения имеются в портал Azure, выберите группу управления, а затем щелкните **IAM**. Дополнительные сведения о ролях RBAC см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../../role-based-access-control/overview.md).

### <a name="move-subscriptions-in-the-portal"></a>Перемещение подписок на портале

#### <a name="add-an-existing-subscription-to-a-management-group"></a>Добавление имеющейся подписки в группу управления

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую вы планируете сделать родительской.

1. В верхней части страницы выберите пункт **Добавить подписку**.

1. Выберите подписку в списке с правильным идентификатором.

   ![Доступные подписки для добавления в группу управления](./media/add_context_sub.png)

1. Щелкните "Сохранить".

#### <a name="remove-a-subscription-from-a-management-group"></a>Удаление подписки из группы управления

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которая является родительской.  

1. Выберите многоточие в конце строки подписки в списке, которую требуется переместить.

   ![Переместить параметр в группе управления](./media/move_small.png)

1. Выберите **Переместить**.

1. В открывшемся меню выберите **родительскую группу управления**.

   ![Переместить панель, чтобы изменить родительскую группу](./media/move_small_context.png)

1. Щелкните **Сохранить**.

### <a name="move-subscriptions-in-powershell"></a>Перемещение подписок в PowerShell

Чтобы переместить подписку в PowerShell, используйте команду New-Азманажементграупсубскриптион.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Чтобы удалить связь между и подпиской и группой управления, используйте команду Remove-Азманажементграупсубскриптион.

```azurepowershell-interactive
Remove-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

### <a name="move-subscriptions-in-azure-cli"></a>Перемещение подписок в Azure CLI

Чтобы переместить подписку в CLI, используйте команду добавления.

```azurecli-interactive
az account management-group subscription add --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

Чтобы удалить подписку из группы управления, используйте команду удаления.  

```azurecli-interactive
az account management-group subscription remove --name 'Contoso' --subscription '12345678-1234-1234-1234-123456789012'
```

## <a name="move-management-groups-in-the-hierarchy"></a>Перемещение групп управления в иерархии  

При перемещении родительской группы управления ее иерархия перемещается вместе с ней. Сведения о доступе, необходимом для перемещения групп управления, см. в разделе [доступ к группе управления](overview.md#management-group-access).

### <a name="move-management-groups-in-the-portal"></a>Перемещение групп управления на портале

1. Войдите на [портал Azure](https://portal.azure.com).

1. Выберите **Все службы** > **Группы управления**.

1. Выберите группу управления, которую вы планируете сделать родительской.

1. В верхней части страницы выберите пункт **Добавить группу управления**.

1. В открывшемся меню выберите создать новую или использовать существующую группу управления.

   - Если вы выберете новую, то создадите новую группу управления.
   - Если вы выберете существующую, вы сможете выбрать доступную группу управления из раскрывающегося списка.  

   ![Перемещение группы управления в новую или существующую группу](./media/add_context_MG.png)

1. Щелкните **Сохранить**.

### <a name="move-management-groups-in-powershell"></a>Перемещение групп управления в PowerShell

Используйте команду Update-Азманажементграуп в PowerShell, чтобы переместить группу управления в другую группу.

```azurepowershell-interactive
$parentGroup = Get-AzManagementGroup -GroupName ContosoIT
Update-AzManagementGroup -GroupName 'Contoso' -ParentId $parentGroup.id
```  

### <a name="move-management-groups-in-azure-cli"></a>Перемещение групп управления в Azure CLI

Чтобы переместить группу управления с помощью Azure CLI, используйте команду обновления.

```azurecli-interactive
az account management-group update --name 'Contoso' --parent ContosoIT
```

## <a name="audit-management-groups-using-activity-logs"></a>Аудит групп управления с помощью журналов действий

[Журнал действий Azure](../../azure-monitor/platform/activity-logs-overview.md) поддерживает группы управления. Вы можете запросить все события, происходящие в группе управления, в том же центральном расположении, что и другие ресурсы Azure.  Например, вы можете просмотреть сведения обо всех изменениях в назначениях ролей или назначении политик, внесенные в пределах определенной группы управления.

![Журналы действий с группами управления](media/al-mg.png)

Если нужно создать запрос к группам управления вне портала Azure, целевая область для групп управления будет такой: **"/providers/Microsoft.Management/managementGroups/{идентификатор_вашей_группы_управления}"** .

## <a name="referencing-management-groups-from-other-resource-providers"></a>Ссылки на группы управления из других поставщиков ресурсов

При обращении к группам управления из действий поставщика других ресурсов используйте следующий путь в качестве области. Этот путь используется при использовании PowerShell, Azure CLI и API-интерфейсов RESTFUL.  

>"/Провидерс/Микрософт.манажемент/манажементграупс/{йоурмгид}"

Пример использования этого пути: назначение нового назначения роли группе управления в PowerShell

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

При извлечении определения политики в группе управления используется тот же путь к области.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Дополнительная информация

Дополнительные сведения о группах управления:

- [Создание групп управления для организации ресурсов Azure](create.md)
- [Изменение, удаление групп управления и управление ими](manage.md)
- [Просмотр групп управления в модуле ресурсов Azure PowerShell](/powershell/module/az.resources#resources)
- [Просмотр групп управления в REST API](/rest/api/resources/managementgroups)
- [Просмотр групп управления в Azure CLI](/cli/azure/account/management-group)