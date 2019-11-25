---
title: Устранение неполадок RBAC для ресурсов Azure | Документация Майкрософт
description: Устранение неполадок с управлением доступом на основе ролей (RBAC) для ресурсов Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/22/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 2351e6a63723156cce646a6a1cdda837b18a8f91
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/24/2019
ms.locfileid: "74456828"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Устранение неполадок RBAC в Azure

В этой статье содержатся ответы на часто задаваемые вопросы об управлении доступом на основе ролей (RBAC) для ресурсов Azure. Вы узнаете, какие ситуации возможны при использовании ролей на портале Azure, а также сможете устранить неполадки, связанные с доступом.

## <a name="problems-with-rbac-role-assignments"></a>Проблемы с назначениями ролей в RBAC

- If you are unable to add a role assignment in the Azure portal on **Access control (IAM)** because the **Add** > **Add role assignment** option is disabled or because you get the permissions error "The client with object id does not have authorization to perform action", check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleAssignments/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator) at the scope you are trying to assign the role.
- If you get the error message "No more role assignments can be created (code: RoleAssignmentLimitExceeded)" when you try to assign a role, try to reduce the number of role assignments by assigning roles to groups instead. Azure поддерживает до **2000** назначений ролей на подписку. This role assignments limit is fixed and cannot be increased.

## <a name="problems-with-custom-roles"></a>Проблемы с пользовательскими ролями

- If you need steps for how to create a custom role, see the custom role tutorials using [Azure PowerShell](tutorial-custom-role-powershell.md) or [Azure CLI](tutorial-custom-role-cli.md).
- If you are unable to update an existing custom role, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Authorization/roleDefinition/write` permission such as [Owner](built-in-roles.md#owner) or [User Access Administrator](built-in-roles.md#user-access-administrator).
- If you are unable to delete a custom role and get the error message "There are existing role assignments referencing role (code: RoleDefinitionHasAssignments)", then there are role assignments still using the custom role. Удалите эти назначения и снова попытайтесь удалить пользовательскую роль.
- Если при попытке создать пользовательскую роль отображается сообщение об ошибке "Превышено ограничение на определение ролей. No more role definitions can be created (code: RoleDefinitionLimitExceeded)" when you try to create a new custom role, delete any custom roles that aren't being used. Azure supports up to **5000** custom roles in a tenant. (Специализированные облачные службы, такие как Azure для государственных организаций, Azure для Германии и Azure для Китая от 21Vianet поддерживают до 2000 пользовательских ролей.)
- If you get an error similar to "The client has permission to perform action 'Microsoft.Authorization/roleDefinitions/write' on scope '/subscriptions/{subscriptionid}', however the linked subscription was not found" when you try to update a custom role, check whether one or more [assignable scopes](role-definitions.md#assignablescopes) have been deleted in the tenant. Если область удалена, создайте запрос в службу поддержки, так как способа самостоятельного решения этой проблемы пока не существует.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Восстановление RBAC при перемещении подписок между клиентами

- If you need steps for how to transfer a subscription to a different Azure AD tenant, see [Transfer ownership of an Azure subscription to another account](../billing/billing-subscription-transfer.md).
- Когда вы переносите подписку в другой клиент Azure AD, все назначения ролей удаляются из исходного клиента Azure AD без возможности восстановления и не переносятся в целевой клиент Azure AD. Необходимо повторно создать назначения ролей в целевом клиенте. You also have to manually recreate managed identities for Azure resources. For more information, see [FAQs and known issues with managed identities](../active-directory/managed-identities-azure-resources/known-issues.md).
- If you are an Azure AD Global Administrator and you don't have access to a subscription after it was moved between tenants, use the **Access management for Azure resources** toggle to temporarily [elevate your access](elevate-access-global-admin.md) to get access to the subscription.

## <a name="issues-with-service-admins-or-co-admins"></a>Проблемы с администраторами или соадминистраторами служб

- If you are having issues with Service administrator or Co-administrators, see [Add or change Azure subscription administrators](../billing/billing-add-change-azure-subscription-administrator.md) and [Classic subscription administrator roles, Azure RBAC roles, and Azure AD administrator roles](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Access denied or permission errors

- If you get the permissions error "The client with object id does not have authorization to perform action over scope (code: AuthorizationFailed)" when you try to create a resource, check that you are currently signed in with a user that is assigned a role that has write permission to the resource at the selected scope. Например, для управления виртуальными машинами в группе ресурсов вам должна быть назначена роль [Участник виртуальных машин](built-in-roles.md#virtual-machine-contributor) для этой группы ресурсов (или вышестоящего уровня). Список разрешений для каждой встроенной роли см. в статье [Built-in roles for Azure resources](built-in-roles.md) (Встроенные роли для ресурсов Azure).
- If you get the permissions error "You don't have permission to create a support request" when you try to create or update a support ticket, check that you are currently signed in with a user that is assigned a role that has the `Microsoft.Support/supportTickets/write` permission, such as [Support Request Contributor](built-in-roles.md#support-request-contributor).

## <a name="role-assignments-with-unknown-security-principal"></a>Role assignments with Unknown security principal

If you assign a role to a security principal (user, group, service principal, or managed identity) and then you later delete that security principal without removing the role assignment, the security principal type for the role assignment will be listed as **Unknown**. The following screenshot shows an example in the Azure portal. The security principal name is listed as **Identity deleted** and **Identity no longer exists**. 

![Группа ресурсов веб-приложений](./media/troubleshooting/unknown-security-principal.png)

If you list this role assignment using Azure PowerShell, you will see an empty `DisplayName` and an `ObjectType` set to Unknown. For example, [Get-AzRoleAssignment](/powershell/module/az.resources/get-azroleassignment) returns a role assignment that is similar to the following:

```azurepowershell
RoleAssignmentId   : /subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222
Scope              : /subscriptions/11111111-1111-1111-1111-111111111111
DisplayName        :
SignInName         :
RoleDefinitionName : Storage Blob Data Contributor
RoleDefinitionId   : ba92f5b4-2d11-453d-a403-e96b0029c9fe
ObjectId           : 33333333-3333-3333-3333-333333333333
ObjectType         : Unknown
CanDelegate        : False
```

Similarly, if you list this role assignment using Azure CLI, you will see an empty `principalName`. For example, [az role assignment list](/cli/azure/role/assignment#az-role-assignment-list) returns a role assignment that is similar to the following:

```azurecli
{
    "canDelegate": null,
    "id": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleAssignments/22222222-2222-2222-2222-222222222222",
    "name": "22222222-2222-2222-2222-222222222222",
    "principalId": "33333333-3333-3333-3333-333333333333",
    "principalName": "",
    "roleDefinitionId": "/subscriptions/11111111-1111-1111-1111-111111111111/providers/Microsoft.Authorization/roleDefinitions/ba92f5b4-2d11-453d-a403-e96b0029c9fe",
    "roleDefinitionName": "Storage Blob Data Contributor",
    "scope": "/subscriptions/11111111-1111-1111-1111-111111111111",
    "type": "Microsoft.Authorization/roleAssignments"
}
```

It isn't a problem to leave these role assignments, but you can remove them using steps that are similar to other role assignments. For information about how to remove role assignments, see [Azure portal](role-assignments-portal.md#remove-role-assignments), [Azure PowerShell](role-assignments-powershell.md#remove-access), or [Azure CLI](role-assignments-cli.md#remove-access)

In PowerShell, if you try to remove the role assignments using the object ID and role definition name, and more than one role assignment matches your parameters, you will get the error message: "The provided information does not map to a role assignment". The following shows an example of the error message:

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor"

Remove-AzRoleAssignment : The provided information does not map to a role assignment.
At line:1 char:1
+ Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 ...
+ ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
+ CategoryInfo          : CloseError: (:) [Remove-AzRoleAssignment], KeyNotFoundException
+ FullyQualifiedErrorId : Microsoft.Azure.Commands.Resources.RemoveAzureRoleAssignmentCommand
```

If you get this error message, make sure you also specify the `-Scope` or `-ResourceGroupName` parameters.

```Example
PS C:\> Remove-AzRoleAssignment -ObjectId 33333333-3333-3333-3333-333333333333 -RoleDefinitionName "Storage Blob Data Contributor" - Scope /subscriptions/11111111-1111-1111-1111-111111111111
```

## <a name="rbac-changes-are-not-being-detected"></a>Не удается обнаружить изменения RBAC

Azure Resource Manager иногда кэширует конфигурации и данные для повышения производительности. При создании или удалении назначения ролей вступление изменений в силу может занять до 30 минут. Если вы используете портал Azure, Azure PowerShell или Azure CLI, можно принудительно обновить изменения назначения ролей, выйдя из учетной записи и повторно войдя в нее. Если вы вносите изменения в назначения ролей с помощью вызовов REST API, можно принудительно обновить изменения, обновив маркер доступа.

## <a name="web-app-features-that-require-write-access"></a>Компоненты веб-приложений, требующие доступа для записи

Если предоставить пользователю доступ только для чтения к одному веб-приложению, некоторые компоненты могут неожиданно отключиться. Перечисленные ниже возможности управления требуют доступа к веб-приложению с правом **записи** (роль участника или владельца), поэтому они недоступны при наличии прав только на чтение.

* Команды (такие как запуск, остановка и т. д.).
* Изменение параметров, таких как общие параметры конфигурации, параметры масштабирования, резервного копирования и мониторинга.
* Доступ к учетным данным для публикации и другим секретным сведениям, например к параметрам приложений и строкам подключения.
* Журналы потоковой передачи.
* Конфигурация журналов диагностики.
* Консоль (командная строка).
* Активные и недавние развертывания (для локального непрерывного развертывания Git).
* Приблизительные затраты.
* Веб-тесты
* Виртуальная сеть (видна читателю только в том случае, если ранее была настроена пользователем с доступом на запись).

Если у вас нет доступа ни к одной из этих плиток, попросите администратора предоставить вам доступ к веб-приложению с правами участника.

## <a name="web-app-resources-that-require-write-access"></a>Ресурсы веб-приложений, требующие доступа для записи

Работа с веб-приложениями осложняется наличием нескольких взаимосвязанных ресурсов. Вот типичная группа ресурсов, связанная с несколькими веб-сайтами:

![Группа ресурсов веб-приложений](./media/troubleshooting/website-resource-model.png)

В результате, если вы предоставите кому-либо доступ только к веб-приложению, многие функции в колонке веб-сайта на портале Azure будут отключены.

Для этих элементов требуется доступ на **запись** к **плану службы приложений**, который соответствует вашему веб-сайту:  

* просмотр ценовой категории веб-приложения (например, "Бесплатный" или "Стандартный");  
* параметры масштабирования (число экземпляров, размер виртуальной машины, настройки автоматического масштабирования);  
* квоты (квоты хранилища, пропускной способности, ресурсов ЦП).  

Элементы, требующие доступа на **запись** ко всей **группе ресурсов**, которая содержит веб-сайт:  

* SSL-сертификаты и привязки (SSL-сертификаты могут совместно использоваться сайтами, относящимися к одной группе ресурсов и находящимися в одном географическом расположении);  
* Правила генерации оповещений  
* параметры автоматического масштабирования;  
* компоненты Application Insights;  
* Веб-тесты  

## <a name="virtual-machine-features-that-require-write-access"></a>Компоненты виртуальных машин, требующие доступа для записи

Как и в случае с веб-приложениями, для некоторых функций в колонке виртуальной машины нужен доступ к виртуальной машине или другим ресурсам в группе ресурсов с правами на запись.

Виртуальные машины связаны с доменными именами, виртуальными сетями, учетными записями хранения и правилами оповещений.

Элементы, требующие доступа к **виртуальной** машине с правом **записи**:

* Конечные точки  
* IP-адреса  
* Диски  
* расширения.  

Элементы, требующие доступа на **запись** как к **виртуальной машине**, так и к **группе ресурсов**, в которой она находится (а также к доменному имени):  

* Группа доступности  
* набор балансировки нагрузки;  
* Правила генерации оповещений  

Если у вас нет доступа ни к одному из этих элементов, попросите администратора предоставить вам права участника для группы ресурсов.

## <a name="azure-functions-and-write-access"></a>Функции Azure и доступ для записи

Для некоторых компонентов решения [Функции Azure](../azure-functions/functions-overview.md) требуется доступ на запись. For example, if a user is assigned the [Reader](built-in-roles.md#reader) role, they will not be able to view the functions within a function app. На портале отобразится текст **(Нет доступа)** .

![Сообщение об отсутствии доступа в приложении-функции](./media/troubleshooting/functionapps-noaccess.png)

Пользователь с ролью читателя может щелкнуть вкладку **Функции платформы** и выбрать **Все параметры**, чтобы просмотреть некоторые параметры, связанные с приложением-функцией (так же, как для веб-приложения), но не может изменить эти параметры. To access these features, you will need the [Contributor](built-in-roles.md#contributor) role.

## <a name="next-steps"></a>Дальнейшие действия

- [Troubleshoot for guest users](role-assignments-external-users.md#troubleshoot)
- [Управление доступом к ресурсам Azure с помощью RBAC и портала Azure](role-assignments-portal.md)
- [Просмотр журналов действий для изменений RBAC в ресурсах Azure](change-history-report.md)

