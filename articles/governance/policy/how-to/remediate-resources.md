---
title: Исправление несоответствующих ресурсов
description: В этом руководстве вы можете восстановить ресурсы, не соответствующие политикам Azure.
ms.date: 02/26/2020
ms.topic: how-to
ms.openlocfilehash: 71af5c81e0dce4d5c0a0461534f634db36bd66a7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79471393"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Исправление несоответствующих ресурсов с помощью службы "Политика Azure"

Ресурсы, не совместимые с **развертываемымИНестиили** или **изменяющие** политику, могут быть введены в состояние совместимого с требованиями с **помощью исправлений.** Исправление происходит путем инструктажа azure Policy для выполнения эффекта **deployIfNotExists** или **операций** тегов назначенной политики на существующих ресурсах, независимо от того, выполняется ли это назначение группе управления, подписке, группе ресурсов или отдельному ресурсу. В этой статье показаны шаги, необходимые для понимания и выполнения исправлений с помощью azure Policy.

## <a name="how-remediation-security-works"></a>Как работает безопасность исправлений

Когда Azure Policy запускает шаблон в определении политики **deployIfNotExists,** она делает это с помощью [управляемого identity.](../../../active-directory/managed-identities-azure-resources/overview.md)
Политика Azure создает управляемую идентификацию для каждого назначения, но должна иметь сведения о том, какие роли можно предоставить управляемому иждивещу. Если в управляемом удостоверении отсутствуют роли, эта ошибка отображается во время назначения политики или в инициативе. При использовании портала политика Azure автоматически предоставляет управляемой идентификации перечисленные роли после начала назначения. _Местоположение_ управляемого удостоверения не влияет на работу с политикой Azure.

![Управляемое удостоверение. Отсутствующая роль](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Если ресурс, измененный **deployIfNotExists** или **изменение,** выходит за рамки назначения политики или шаблон получает доступ к свойствам на ресурсах, выходящих за рамки назначения политики, управляемому инофайлу назначения должен быть [предоставлен вручную доступ](#manually-configure-the-managed-identity) или развертывание исправлений завершится неудачей.

## <a name="configure-policy-definition"></a>Настройка определения политики

Первым шагом является определение ролей, которые **развертываютIfNotExists,** и **изменение** потребностей в определении политики для успешного развертывания содержимого включенного шаблона. В свойстве **details** добавьте свойство **roleDefinitionIds**. Это свойство является массивом строк, соответствующих ролям в среде. Для полного примера смотрите [пример deployIfNotExists](../concepts/effects.md#deployifnotexists-example) или [примеры модификации.](../concepts/effects.md#modify-examples)

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Свойство **RoleDefinitionIds** использует полный идентификатор ресурса и не берет на себя короткую **рольНаглавке** роли. Чтобы получить идентификатор для роли "Участник" в среде, используйте следующий код:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Настройка управляемого удостоверения вручную

При создании назначения с помощью портала политика Azure генерирует управляемую идентификацию и предоставляет ему роли, определенные в **roleDefinitionIds.** В следующих ситуациях шаги по созданию управляемого удостоверения и присвоению ему разрешений должны выполняться вручную:

- при использовании пакета SDK (например, Azure PowerShell);
- при изменении ресурсов, находящихся за пределами области назначения, с помощью шаблона;
- при чтении ресурсов, находящихся за пределами области назначения, с помощью шаблона.

> [!NOTE]
> В настоящее время только пакеты SDK Azure PowerShell и .NET поддерживают эту возможность.

### <a name="create-managed-identity-with-powershell"></a>Создание управляемого удостоверения с помощью PowerShell

Для создания управляемого удостоверения во время назначения политики необходимо определить свойство **Location** и использовать свойство **AssignIdentity**. Следующий пример получает определение встроенной политики **Развернуть прозрачное шифрование базы данных SQL**, задает целевую группу ресурсов, а затем создает назначение.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Get the built-in "Deploy SQL DB transparent data encryption" policy definition
$policyDef = Get-AzPolicyDefinition -Id '/providers/Microsoft.Authorization/policyDefinitions/86a912f6-9a06-4e26-b447-11b16ba8659f'

# Get the reference to the resource group
$resourceGroup = Get-AzResourceGroup -Name 'MyResourceGroup'

# Create the assignment using the -Location and -AssignIdentity properties
$assignment = New-AzPolicyAssignment -Name 'sqlDbTDE' -DisplayName 'Deploy SQL DB transparent data encryption' -Scope $resourceGroup.ResourceId -PolicyDefinition $policyDef -Location 'westus' -AssignIdentity
```

Переменная `$assignment` теперь содержит идентификатор субъекта для управляемого удостоверения, а также стандартные значения, возвращаемые при создании назначения политики. Доступ может осуществляться через `$assignment.Identity.PrincipalId`.

### <a name="grant-defined-roles-with-powershell"></a>Предоставление определенных ролей с помощью PowerShell

Новое управляемое удостоверение должно выполнить репликацию через Azure Active Directory, прежде чем ему можно будет предоставить необходимые роли. После завершения репликации приведенный ниже пример выполняет итерацию определения политики в `$policyDef` для **roleDefinitionIds** и использует командлет [New-AzRoleAssignment](/powershell/module/az.resources/new-azroleassignment), чтобы предоставить роли новому управляемому удостоверению.

```azurepowershell-interactive
# Use the $policyDef to get to the roleDefinitionIds array
$roleDefinitionIds = $policyDef.Properties.policyRule.then.details.roleDefinitionIds

if ($roleDefinitionIds.Count -gt 0)
{
    $roleDefinitionIds | ForEach-Object {
        $roleDefId = $_.Split("/") | Select-Object -Last 1
        New-AzRoleAssignment -Scope $resourceGroup.ResourceId -ObjectId $assignment.Identity.PrincipalId -RoleDefinitionId $roleDefId
    }
}
```

### <a name="grant-defined-roles-through-portal"></a>Предоставление определенных ролей через портал

На портале управляемому удостоверению назначения можно присвоить определенные роли двумя способами: используя раздел **управление доступом (IAM)** либо изменив назначение политики или инициативы и выбрав **Сохранить**.

Чтобы добавить роль для управляемого удостоверения назначения, выполните следующие действия:

1. Запустите службу "Политика Azure" на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

1. Выберите **Назначения** на странице службы "Политики Azure" слева.

1. Найдите назначение, которое имеет управляемое удостоверение, и щелкните его имя.

1. Найдите свойство **Идентификатор назначения** на странице изменения. Идентификатор назначения будет выглядеть примерно так:

   ```output
   /subscriptions/{subscriptionId}/resourceGroups/PolicyTarget/providers/Microsoft.Authorization/policyAssignments/2802056bfc094dfb95d4d7a5
   ```

   Имя управляемого удостоверения — это последняя часть идентификатора ресурса назначения (в этом примере `2802056bfc094dfb95d4d7a5`). Скопируйте эту часть идентификатора ресурса назначения.

1. Перейдите к ресурсу или родительскому контейнеру ресурсов (группа ресурсов, подписка, группа управления), для которого нужно вручную добавить определение роли.

1. Щелкните ссылку **Управление доступом (IAM)** на странице ресурсов и выберите **+ Добавить назначение ролей** в верхней части страницы управления доступом.

1. Выберите нужную роль, соответствующую **roleDefinitionIds** определения политики.
   В поле **Назначение доступа к** оставьте значение по умолчанию "Пользователь, группа или приложение Azure AD". В поле **Выберите** вставьте или введите часть идентификатора ресурса назначения, обнаруженного ранее. Как только поиск завершается, нажмите на объект с тем же именем, чтобы выбрать идентификатор и нажмите **Сохранить**.

## <a name="create-a-remediation-task"></a>Создание задачи исправления

### <a name="create-a-remediation-task-through-portal"></a>Создание задачи по исправлению ситуации через портал

Во время оценки назначение политики с **развертываниямиIfNotExists** или **изменение** эффектов определяет, есть ли несовместимые ресурсы. При обнаружении несоответствующих ресурсов на странице **Исправление** предоставляются дополнительные сведения. Вместе со списком политик, имеющих несоответствующие ресурсы, отображается параметр активации **задачи исправления**. Эта опция создает развертывание из шаблона **deployIfNotExists** или **изменять** операции.

Чтобы создать **задачу исправления**, выполните следующие действия:

1. Запустите службу "Политика Azure" на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

   ![Поиск политики в разделе "Все службы"](../media/remediate-resources/search-policy.png)

1. Выберите **Исправление** на странице службы "Политика Azure" слева.

   ![Выберите исправление на странице Политики](../media/remediate-resources/select-remediation.png)

1. Все **развертываемыеifNotExists** и **изменяемые** назначения политики с помощью ресурсов, не соответствующих требованиям, включаются в **политики для исправления** вкладок и таблицы данных. Выберите политику с несоответствующими ресурсами. Откроется страница **Новая задача исправления**.

   > [!NOTE]
   > Альтернативным способом открытия страницы **задачи исправления** — найти и щелкнуть политику на странице **соответствия**, а затем нажать кнопку **Создать задачу исправления**.

1. На странице **Новая задача исправления** отфильтруйте ресурсы, которые нужно исправить, используя многоточие возле поля **Область**, чтобы выбрать дочерние ресурсы, которым была присвоена политика (в том числе отдельные объекты ресурса). Кроме того, используйте раскрывающийся список **Расположения**, чтобы дополнительно отфильтровать ресурсы. Будут исправлены только те ресурсы, которые перечислены в таблице.

   ![Исправление - выберите, какие ресурсы для исправления](../media/remediate-resources/select-resources.png)

1. Запустите задачу по исправлению после того, как ресурсы были отфильтрованы, щелкнув **Исправить**. Страница соответствия политики открывается для вкладки **задач восстановления,** чтобы показать состояние хода выполнения задач. Развертывание, созданное задачей восстановления, начинается сразу.

   ![Исправление - прогресс задач по восстановлению](../media/remediate-resources/task-progress.png)

1. Щелкните **задачу исправления** на странице политики соответствия, чтобы получить сведения о ходе выполнения. Фильтрация, используемая для задачи, отображается вместе со списком ресурсов, которые будут исправлены.

1. На странице **задачи исправления** щелкните ресурс правой кнопкой мыши, чтобы просмотреть развертывание задачи обновления или ресурс. В конце строки щелкните **Связанные события**, чтобы просмотреть сведения, такие как сообщение об ошибке.

   ![Исправление. Контекстное меню задачи ресурса](../media/remediate-resources/resource-task-context-menu.png)

Ресурсы, развернутые посредством **задачи исправления**, добавляются на вкладку **Развернутые ресурсы** на странице соответствия политике.

### <a name="create-a-remediation-task-through-azure-cli"></a>Создание задачи по исправлению ситуации через Azure CLI

Чтобы создать **задачу исправления** с Azure CLI, используйте `az policy remediation` команды. Замените `{subscriptionId}` идентификатор подписки и `{myAssignmentId}` **развертываниеIfNotExists** или **измените** идентификатор назначения политики.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Для других команд и приведений [az policy remediation](/cli/azure/policy/remediation) в исправления и примерах см.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Создание задачи по исправлению ситуации через Azure PowerShell

Чтобы создать **задачу восстановления** с Azure `Start-AzPolicyRemediation` PowerShell, используйте команды. Замените `{subscriptionId}` идентификатор подписки и `{myAssignmentId}` **развертываниеIfNotExists** или **измените** идентификатор назначения политики.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Для других рекультивации cmdlets и примеры, см [Az.PolicyInsights](/powershell/module/az.policyinsights/#policy_insights) модуль.

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [примеры на примерах политики Azure](../samples/index.md).
- Изучите статью о [структуре определения Политики Azure](../concepts/definition-structure.md).
- Изучите [сведения о действии политик](../concepts/effects.md).
- Понять, как [программно создавать политики.](programmatically-create.md)
- Узнайте, как [получить данные о соответствии.](get-compliance-data.md)
- Просмотрите, что представляет собой группа управления с [организацией ресурсов с группами управления Azure.](../../management-groups/overview.md)
