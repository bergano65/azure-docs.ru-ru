---
title: Исправление несоответствующих ресурсов
description: В этом руководстве описывается исправление ресурсов, которые не соответствуют политикам в политике Azure.
ms.date: 09/09/2019
ms.topic: conceptual
ms.openlocfilehash: 8f1d263286a7504e7a8234ebd944bbbee69c5303
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74267357"
---
# <a name="remediate-non-compliant-resources-with-azure-policy"></a>Исправление несоответствующих ресурсов с помощью службы "Политика Azure"

Ресурсы, которые не соответствуют политике **deployIfNotExists** или **Modify** , могут быть переведены в соответствующее состояние путем **исправления**. Исправление достигается путем указания политики Azure на выполнение действия **deployIfNotExists** **или тегов** назначенной политики для существующих ресурсов. В этой статье описаны шаги, необходимые для понимания и выполнения исправления с помощью политики Azure.

## <a name="how-remediation-security-works"></a>Как работает безопасность исправлений

Когда политика Azure запускает шаблон в определении политики **deployIfNotExists** , она использует [управляемое удостоверение](../../../active-directory/managed-identities-azure-resources/overview.md).
Политика Azure создает управляемое удостоверение для каждого назначения, но должно иметь подробные сведения о том, какие роли должны предоставлять управляемое удостоверение. Если в управляемом удостоверении отсутствуют роли, эта ошибка отображается во время назначения политики или в инициативе. При использовании портала политика Azure автоматически предоставит управляемому удостоверению указанные роли после начала назначения.

![Управляемое удостоверение. Отсутствующая роль](../media/remediate-resources/missing-role.png)

> [!IMPORTANT]
> Если ресурс, измененный с помощью **deployIfNotExists** или **Modify** , находится за пределами области назначения политики, или шаблон обращается к свойствам ресурсов за пределами области назначения политики, то управляемому удостоверению назначения должен быть [предоставлен доступ вручную](#manually-configure-the-managed-identity) или развертывание исправления завершится сбоем.

## <a name="configure-policy-definition"></a>Настройка определения политики

Первым шагом является определение ролей, которые **deployIfNotExists** и **изменяют** потребности в определении политики для успешного развертывания содержимого включенного шаблона. В свойстве **details** добавьте свойство **roleDefinitionIds**. Это свойство является массивом строк, соответствующих ролям в среде. Полный пример см. в [примере deployIfNotExists](../concepts/effects.md#deployifnotexists-example) или в [примерах изменения](../concepts/effects.md#modify-examples).

```json
"details": {
    ...
    "roleDefinitionIds": [
        "/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/roleDefinitions/{roleGUID}",
        "/providers/Microsoft.Authorization/roleDefinitions/{builtinroleGUID}"
    ]
}
```

Свойство **роледефинитионидс** использует полный идентификатор ресурса и не принимает короткую значение **roleName** роли. Чтобы получить идентификатор для роли "Участник" в среде, используйте следующий код:

```azurecli-interactive
az role definition list --name 'Contributor'
```

## <a name="manually-configure-the-managed-identity"></a>Настройка управляемого удостоверения вручную

При создании назначения с помощью портала политика Azure создает управляемое удостоверение и предоставляет ему роли, определенные в **роледефинитионидс**. В следующих ситуациях шаги по созданию управляемого удостоверения и присвоению ему разрешений должны выполняться вручную:

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
   В поле **Назначение доступа к** оставьте значение по умолчанию "Пользователь, группа или приложение Azure AD". В поле **Выберите** вставьте или введите часть идентификатора ресурса назначения, обнаруженного ранее. После завершения поиска щелкните объект с тем же именем, чтобы выбрать идентификатор, и нажмите кнопку **Сохранить**.

## <a name="create-a-remediation-task"></a>Создание задачи исправления

### <a name="create-a-remediation-task-through-portal"></a>Создание задачи исправления с помощью портала

Во время оценки назначение политики с **deployIfNotExists** или **Modify** Effects определяет, есть ли несоответствующие ресурсы. При обнаружении несоответствующих ресурсов на странице **Исправление** предоставляются дополнительные сведения. Вместе со списком политик, имеющих несоответствующие ресурсы, отображается параметр активации **задачи исправления**. Этот параметр создает развертывание из шаблона **deployIfNotExists** или операций **изменения** .

Чтобы создать **задачу исправления**, выполните следующие действия:

1. Запустите службу "Политика Azure" на портале Azure, щелкнув **Все службы**, а затем выполнив поиск и выбрав **Политика**.

   ![Поиск политики в разделе "Все службы"](../media/remediate-resources/search-policy.png)

1. Выберите **Исправление** на странице службы "Политика Azure" слева.

   ![Выбор исправления на странице политики](../media/remediate-resources/select-remediation.png)

1. Все **deployIfNotExists** и **изменения** назначений политик с несоответствующими ресурсами включены в **политики для исправления** вкладки и таблицы данных. Выберите политику с несоответствующими ресурсами. Откроется страница **Новая задача исправления**.

   > [!NOTE]
   > Альтернативным способом открытия страницы **задачи исправления** — найти и щелкнуть политику на странице **соответствия**, а затем нажать кнопку **Создать задачу исправления**.

1. На странице **Новая задача исправления** отфильтруйте ресурсы, которые нужно исправить, используя многоточие возле поля **Область**, чтобы выбрать дочерние ресурсы, которым была присвоена политика (в том числе отдельные объекты ресурса). Кроме того, используйте раскрывающийся список **Расположения**, чтобы дополнительно отфильтровать ресурсы. Будут исправлены только те ресурсы, которые перечислены в таблице.

   ![Исправление — выбор ресурсов для исправления](../media/remediate-resources/select-resources.png)

1. Запустите задачу по исправлению после того, как ресурсы были отфильтрованы, щелкнув **Исправить**. На странице соответствия политики откроется вкладка **Задачи исправления**, где отображается состояние хода выполнения задач.

   ![Исправление — ход выполнения задач по исправлению](../media/remediate-resources/task-progress.png)

1. Щелкните **задачу исправления** на странице политики соответствия, чтобы получить сведения о ходе выполнения. Фильтрация, используемая для задачи, отображается вместе со списком ресурсов, которые будут исправлены.

1. На странице **задачи исправления** щелкните ресурс правой кнопкой мыши, чтобы просмотреть развертывание задачи обновления или ресурс. В конце строки щелкните **Связанные события**, чтобы просмотреть сведения, такие как сообщение об ошибке.

   ![Исправление. Контекстное меню задачи ресурса](../media/remediate-resources/resource-task-context-menu.png)

Ресурсы, развернутые посредством **задачи исправления**, добавляются на вкладку **Развернутые ресурсы** на странице соответствия политике.

### <a name="create-a-remediation-task-through-azure-cli"></a>Создание задачи исправления с помощью Azure CLI

Чтобы создать **задачу исправления** с Azure CLI, используйте команды `az policy remediation`. Замените `{subscriptionId}` ИДЕНТИФИКАТОРом подписки и `{myAssignmentId}` с помощью **deployIfNotExists** или **измените** идентификатор назначения политики.

```azurecli-interactive
# Login first with az login if not using Cloud Shell

# Create a remediation for a specific assignment
az policy remediation create --name myRemediation --policy-assignment '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Другие команды и примеры исправления см. в разделе [AZ Policy исправление](/cli/azure/policy/remediation) команд.

### <a name="create-a-remediation-task-through-azure-powershell"></a>Создание задачи исправления с помощью Azure PowerShell

Чтобы создать **задачу исправления** с Azure PowerShell, используйте команды `Start-AzPolicyRemediation`. Замените `{subscriptionId}` ИДЕНТИФИКАТОРом подписки и `{myAssignmentId}` с помощью **deployIfNotExists** или **измените** идентификатор назначения политики.

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell

# Create a remediation for a specific assignment
Start-AzPolicyRemediation -Name 'myRemedation' -PolicyAssignmentId '/subscriptions/{subscriptionId}/providers/Microsoft.Authorization/policyAssignments/{myAssignmentId}'
```

Другие командлеты и примеры исправления см. в разделе [AZ. полициинсигхтс](/powershell/module/az.policyinsights/#policy_insights) Module.

## <a name="next-steps"></a>Дополнительная информация

- Просмотрите примеры в [примерах политики Azure](../samples/index.md).
- Изучите статью о [структуре определения Политики Azure](../concepts/definition-structure.md).
- См. дополнительные сведения о [действиях политик](../concepts/effects.md).
- Узнайте, как [программно создавать политики](programmatically-create.md).
- Узнайте, как [получить данные о соответствии](get-compliance-data.md).
- Дополнительные сведения о группе управления см. в статье [Упорядочивание ресурсов с помощью групп управления Azure](../../management-groups/overview.md).
