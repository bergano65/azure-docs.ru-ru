---
title: Как работать с группами управления - Azure Governance
description: Узнайте, как просматривать, сохранять, обновлять и удалять иерархию групп управления.
ms.date: 04/15/2020
ms.topic: conceptual
ms.openlocfilehash: 423d1837c3d5710e24abb94f5411200319e8a8aa
ms.sourcegitcommit: ea006cd8e62888271b2601d5ed4ec78fb40e8427
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81381681"
---
# <a name="manage-your-resources-with-management-groups"></a>Управление ресурсами с помощью групп управления

Если в вашей организации оформлено много подписок, возможно, потребуется повысить эффективность управления доступом, политиками и соответствием требованиям для этих подписок. Группы управления Azure обеспечивают высокий уровень охвата подписок. Подписки упорядочиваются в контейнеры, которые называются группами управления. К ним применяются условия системы управления. Все подписки в группе управления автоматически наследуют условия, применяемые к группе управления.

Группы управления обеспечивают корпоративное управление в больших масштабах независимо от типа подписки. Дополнительные сведения о группах управления см. в статье [Упорядочение ресурсов с помощью групп управления Azure](./overview.md).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

> [!IMPORTANT]
> Токены пользователей и кэш группы управления Azure Resource Manager длятся 30 минут, прежде чем они будут вынуждены обновиться. После выполнения любых действий, таких как перемещение группы управления или подписки, это может занять до 30 минут, чтобы показать. Чтобы увидеть обновления раньше, вам нужно обновить маркер, освежая браузер, ввод и выход, или запрашивая новый маркер.  

## <a name="change-the-name-of-a-management-group"></a>Изменение имени группы управления

Имя группы управления можно изменить с помощью портала, PowerShell или Azure CLI.

### <a name="change-the-name-in-the-portal"></a>Изменение имени на портале

1. Войти на [портал Azure](https://portal.azure.com).

1. Выберите все > **группы управления** **службами.**

1. Выберите группу управления, которую требуется переименовать.

1. Выберите **детали**.

1. Выберите параметр **Переименовать группу** в верхней части страницы.

   :::image type="content" source="./media/detail_action_small.png" alt-text="Вариант переименования группы на странице группы управления" border="false":::

1. Когда откроется меню, введите новое имя.

   :::image type="content" source="./media/rename_context.png" alt-text="Переименовать панель группы для переименования группы управления" border="false":::

1. Щелкните **Сохранить**.

### <a name="change-the-name-in-powershell"></a>Изменение имени в PowerShell

Для обновления имени дисплея используйте **Update-AzManagementGroup**. Например, для изменения имени групп управления с "Contoso IT" на "Contoso Group" выполняется следующая команда:

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

   - Для перемещения группы управления подпиской или управлением в другую группу управления [см. Перемещение групп управления и подписок в иерархии.](#moving-management-groups-and-subscriptions)

1. Вам нужно написать разрешения на группу управления ("Владелец", "Вкладчик" или "Автор группы управления"). Чтобы получить сведения об имеющихся разрешениях, выберите группу управления, а затем выберите **IAM**. Чтобы узнать больше о ролях RBAC, см.  
   [Управление доступом и разрешениями с помощью RBAC.](../../role-based-access-control/overview.md)

### <a name="delete-in-the-portal"></a>Удаление на портале

1. Войти на [портал Azure](https://portal.azure.com).

1. Выберите все > **группы управления** **службами.**

1. Выберите группу управления, которую требуется удалить.

1. Выберите **детали**.

1. Выберите **Удалить**

   :::image type="content" source="./media/delete.png" alt-text="Параметр "Удалить группу"" border="false":::

   > [!TIP]
   > Если значок неактивен, наведите указатель мыши на значок, чтобы узнать причину.

1. Откроется окно, в котором нужно подтвердить удаление группы управления.

   :::image type="content" source="./media/delete_confirm.png" alt-text="Окно подтверждения удаления группы" border="false":::

1. Выберите **Да**.

### <a name="delete-in-powershell"></a>Удаление в PowerShell

Используйте команду **Remove-AzManagementGroup** в PowerShell для удаления групп управления.

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

1. Войти на [портал Azure](https://portal.azure.com).

1. Выберите все > **группы управления** **службами.**

1. Страница иерархии группуправления загрузится. На этой странице вы можете изучить все группы управления и подписки у вас есть доступ к. При выборе имени группы открывается нижний уровень в иерархии. Навигация работает так же, как и в обозревателе файлов.

1. Чтобы просмотреть сведения о группе управления, перейдите по ссылке **(подробности)** рядом с заголовком группы управления. Если ссылка недоступна, у вас нет разрешения на просмотр этой группы управления.

   :::image type="content" source="./media/main.png" alt-text="Главной" border="false":::

### <a name="view-in-powershell"></a>Просмотр в PowerShell

Для получения всех групп используется команда Get-AzManagementGroup. Ознакомиться с модулями [Az.Resources](/powershell/module/az.resources/Get-AzManagementGroup) можно в полном списке команд ГРУППы управления GET PowerShell.  

```azurepowershell-interactive
Get-AzManagementGroup
```

Для получения сведений об одной группе управления используйте параметр -GroupName.

```azurepowershell-interactive
Get-AzManagementGroup -GroupName 'Contoso'
```

Чтобы вернуть конкретную группу управления и все уровни иерархии под ней, используйте **параметры -Expand** и **-Recurse.**  

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

Чтобы вернуть конкретную группу управления и все уровни иерархии под ней, используйте **параметры -Expand** и **-Recurse.**

```azurecli-interactive
az account management-group show --name 'Contoso' -e -r
```

## <a name="moving-management-groups-and-subscriptions"></a>Перемещение групп управления и подписок   

Одна из причин для создания группы управления — объединение подписок. Дочерним элементом группы управления могут быть только подписки и группы управления. Подписка, которая переходит в группу управления, наследует весь пользовательский доступ и политики из родительской группы управления

При перемещении группы управления или подписки в качестве ребенка другой группы управления три правила должны быть оценены как верные.

Чтобы выполнить перемещение, вам потребуется следующее: 

- Разрешения на запись сведений о группе управления и назначении роли в дочерней подписке или группе управления.
  - Пример встроенной роли: **Владелец**.
- Разрешения на запись сведений о группе управления в целевой родительской группе управления.
  - Встроенный пример для подражания: **Владелец,** **Участник,** **Участник группы управления**
- Разрешения на запись сведений о группе управления в нынешней родительской группе управления.
  - Встроенный пример для подражания: **Владелец,** **Участник,** **Участник группы управления**

**Исключение:** Если цель или существующая группа управления родительскими родителями являются группой управления Root, требования к разрешениям не применяются. Так как корневая группа управления по умолчанию считается родительской для всех новых групп управления и подписок, перемещение элементов в нее не требует дополнительных разрешений.

Если роль владельца подписки наследуется из нынешней родительской группы управления, выбор целевых объектов для перемещения будет ограничен. Вы можете переместить подписку только в другую группу управления, для которой у вас есть роль владельца. Вы не сможете переместить ее в группу управления, для которой являетесь участником, так как при этом потеряете роль владельца для подписки. Если вы непосредственно назначены на роль Владельца для подписки (не унаследованный от группы управления), вы можете переместить его в любую группу управления, где вы являетесь участником.

Чтобы узнать, какие разрешения у вас есть на портале Azure, выберите группу управления, а затем выберите **IAM.** Дополнительные сведения о ролях RBAC см. в статье [Начало работы с управлением доступом на основе ролей на портале Azure](../../role-based-access-control/overview.md).

## <a name="move-subscriptions"></a>Перемещение подписок 

### <a name="add-an-existing-subscription-to-a-management-group-in-the-portal"></a>Добавление существующей подписки в группу управления на портале

1. Войти на [портал Azure](https://portal.azure.com).

1. Выберите все > **группы управления** **службами.**

1. Выберите группу управления, которую вы планируете сделать родительской.

1. В верхней части страницы выберите пункт **Добавить подписку**.

1. Выберите подписку в списке с правильным идентификатором.

   :::image type="content" source="./media/add_context_sub.png" alt-text="Доступные подписки для добавления в группу управления" border="false":::

1. Щелкните "Сохранить".

### <a name="remove-a-subscription-from-a-management-group-in-the-portal"></a>Удалить подписку из группы управления на портале

1. Войти на [портал Azure](https://portal.azure.com).

1. Выберите все > **группы управления** **службами.**

1. Выберите группу управления, которая является родительской.  

1. Выберите многоточие в конце строки подписки в списке, которую требуется переместить.

   :::image type="content" source="./media/move_small.png" alt-text="Перемещение опциона на группу управления" border="false":::

1. Выберите **Переместить**.

1. В открывшемся меню выберите **родительскую группу управления**.

   :::image type="content" source="./media/move_small_context.png" alt-text="Перемещение панели для изменения родительской группы" border="false":::

1. Щелкните **Сохранить**.

### <a name="move-subscriptions-in-powershell"></a>Перемещение подписок в PowerShell

Для перемещения подписки в PowerShell используется команда New-AzManagementGroupSubscription.  

```azurepowershell-interactive
New-AzManagementGroupSubscription -GroupName 'Contoso' -SubscriptionId '12345678-1234-1234-1234-123456789012'
```

Для удаления ссылки между подпиской и группой управления и группой управления используйте команду Удалить-AzManagementGroupSubscription.

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

## <a name="move-management-groups"></a>Перемещение групп управления 

### <a name="move-management-groups-in-the-portal"></a>Перемещение групп управления на портале

1. Войти на [портал Azure](https://portal.azure.com).

1. Выберите все > **группы управления** **службами.**

1. Выберите группу управления, которую вы планируете сделать родительской.

1. В верхней части страницы выберите пункт **Добавить группу управления**.

1. В открывшемся меню выберите создать новую или использовать существующую группу управления.

   - Если вы выберете новую, то создадите новую группу управления.
   - Если вы выберете существующую, вы сможете выбрать доступную группу управления из раскрывающегося списка.  

   :::image type="content" source="./media/add_context_MG.png" alt-text="Перемещение группы управления в новую или существующую группу" border="false":::

1. Щелкните **Сохранить**.

### <a name="move-management-groups-in-powershell"></a>Перемещение групп управления в PowerShell

Используйте команду Update-AzManagementGroup в PowerShell для перемещения группы управления в другую группу.

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

[Журнал действий Azure](../../azure-monitor/platform/platform-logs-overview.md) поддерживает группы управления. Можно задать вопрос обо всех событиях, которые происходят с группой управления в том же центральном месте, что и другие ресурсы Azure. Например, вы можете просмотреть сведения обо всех изменениях в назначениях ролей или назначении политик, внесенные в пределах определенной группы управления.

:::image type="content" source="./media/al-mg.png" alt-text="Логи активности с группами управления" border="false":::

Если нужно создать запрос к группам управления вне портала Azure, целевая область для групп управления будет такой: **"/providers/Microsoft.Management/managementGroups/{идентификатор_вашей_группы_управления}"**.

## <a name="referencing-management-groups-from-other-resource-providers"></a>Ссылки на группы управления от других поставщиков ресурсов

При отсылке групп управления из действий другого поставщика ресурсов используйте следующий путь в качестве области. Этот путь используется при использовании API PowerShell, Azure CLI и REST.  

`/providers/Microsoft.Management/managementGroups/{yourMgID}`

Примером использования этого пути является назначение нового назначения ролей группе управления в PowerShell:

```azurepowershell-interactive
New-AzRoleAssignment -Scope "/providers/Microsoft.Management/managementGroups/Contoso"
```

Такой же путь области используется при извлечении определения политики в группе управления.

```http
GET https://management.azure.com/providers/Microsoft.Management/managementgroups/MyManagementGroup/providers/Microsoft.Authorization/policyDefinitions/ResourceNaming?api-version=2018-05-01
```

## <a name="next-steps"></a>Следующие шаги

Дополнительные сведения о группах управления:

- [Создание групп управления для организации ресурсов Azure](./create.md)
- [Изменение, удаление групп управления и управление ими](./manage.md)
- [Просмотр групп управления в модуле ресурсов Azure PowerShell](/powershell/module/az.resources#resources)
- [Просмотр групп управления в REST API](/rest/api/resources/managementgroups)
- [Просмотр групп управления в Azure CLI](/cli/azure/account/management-group)
