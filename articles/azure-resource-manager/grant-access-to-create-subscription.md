---
title: Предоставление доступа к созданию подписок Azure Enterprise | Документация Майкрософт
description: Дополнительные сведения о предоставлении пользователю или субъекту-службе возможности программно создавать подписки Azure Enterprise.
services: azure-resource-manager
author: jureid
manager: jureid
editor: ''
ms.assetid: ''
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/09/2019
ms.author: jureid
ms.openlocfilehash: 742658e36da956c46bd932b59903e68786c65b93
ms.sourcegitcommit: 36c50860e75d86f0d0e2be9e3213ffa9a06f4150
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/16/2019
ms.locfileid: "65794561"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)

Пользователь Azure на [Соглашение Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) может предоставлять разрешение другому пользователю или субъекту-службе на создание подписок, оплаченных от имени своей учетной записи. В этой статье рассказывается об использовании [управления доступом на основе ролей (RBAC)](../active-directory/role-based-access-control-configure.md) для совместного создания подписок и их аудита. Необходима роль владельца для учетной записи, к которой вы хотите предоставить общий доступ.

Подробные сведения о создании подписки см. в статье [Программное создание подписок Azure Enterprise (предварительная версия)](programmatically-create-subscription.md).

## <a name="grant-subscription-creation-access-to-a-user-or-group"></a>Предоставлять доступ к подписке создания пользователя или группы

Для создания подписок в учетной записи регистрации, пользователи должны иметь [роль владельца RBAC](../role-based-access-control/built-in-roles.md#owner) для этой учетной записи. Можно предоставить пользователю или группе пользователей роль владельца RBAC для учетной записи регистрации, выполнив следующие действия:

### <a name="1-get-the-object-id-of-the-enrollment-account-you-want-to-grant-access-to"></a>1. Получить идентификатор объекта, которые вы хотите предоставить доступ к учетной записи регистрации

Предоставлять другим пользователям роль владельца RBAC для учетной записи регистрации, необходимо либо быть владельцем учетной записи или владельцем RBAC учетной записи.

### <a name="resttabrest"></a>[REST](#tab/rest)

Запрос, чтобы получить список всех учетных записей регистрации, которые у вас есть доступ к:

```json
GET https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts?api-version=2018-03-01-preview
```

Платформа Azure выводит список всех учетных записей регистрации, к которым у вас есть доступ.

```json
{
  "value": [
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "SignUpEngineering@contoso.com"
      }
    },
    {
      "id": "/providers/Microsoft.Billing/enrollmentAccounts/4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
      "type": "Microsoft.Billing/enrollmentAccounts",
      "properties": {
        "principalName": "BillingPlatformTeam@contoso.com"
      }
    }
  ]
}
```

Используйте `principalName` свойства идентификации, вы хотите предоставить доступ владельца RBAC для учетной записи. Копировать `name` этой учетной записи. Например, если вы хотите предоставить доступ владельца RBAC для SignUpEngineering@contoso.com учетной записи регистрации, вы будете копировать ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение, где-нибудь, чтобы его можно использовать на следующем шаге как `enrollmentAccountObjectId`.

### <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

Откройте [Azure Cloud Shell](https://shell.azure.com/) и выберите команду PowerShell.

Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurepowershell-interactive
Get-AzEnrollmentAccount
```

Платформа Azure выводит список учетных записей регистрации, которые у вас есть доступ к:

```azurepowershell
ObjectId                               | PrincipalName
747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
```

Используйте `principalName` свойство для идентификации учетной записи, которую необходимо предоставить право владельца RBAC. Копировать `ObjectId` этой учетной записи. Например, если вы хотите предоставить доступ владельца RBAC для SignUpEngineering@contoso.com учетной записи регистрации, вы будете копировать ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Вставьте этот идентификатор объекта, где-нибудь, чтобы его можно использовать на следующем шаге как `enrollmentAccountObjectId`.

### <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ.

```azurecli-interactive 
az billing enrollment-account list
```

Платформа Azure выводит список учетных записей регистрации, которые у вас есть доступ к:

```json
[
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "SignUpEngineering@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  },
  {
    "id": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "name": "4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "principalName": "BillingPlatformTeam@contoso.com",
    "type": "Microsoft.Billing/enrollmentAccounts",
  }
]

```

Используйте `principalName` свойства идентификации, вы хотите предоставить доступ владельца RBAC для учетной записи. Копировать `name` этой учетной записи. Например, если вы хотите предоставить доступ владельца RBAC для SignUpEngineering@contoso.com учетной записи регистрации, вы будете копировать ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение, где-нибудь, чтобы его можно использовать на следующем шаге как `enrollmentAccountObjectId`.

<a id="userObjectId"></a>

### <a name="2-get-object-id-of-the-user-or-group-you-want-to-give-the-rbac-owner-role-to"></a>2. Получить идентификатор объекта пользователя или группы, необходимо предоставить роль владельца RBAC для

1. На портале Azure, выполните поиск **Azure Active Directory**.
1. Если вы хотите предоставить доступ пользователю, щелкнуть **пользователей** в меню слева. Если вы хотите предоставить доступ к группе, нажмите кнопку **группы**.
1. Выберите пользователя или группы, необходимо предоставить роль владельца RBAC для.
1. Если выбран пользователь, вы найдете на странице профиля идентификатор объекта. Если вы выбрали группы, идентификатор объекта будет в странице "Обзор". Копировать **ObjectID** , щелкнув значок справа от текстового поля. Вставьте это где-нибудь, которые можно использовать на следующем шаге как `userObjectId`.

### <a name="3-grant-the-user-or-group-the-rbac-owner-role-on-the-enrollment-account"></a>3. Предоставьте пользователю или группе роль владельца RBAC для учетной записи регистрации

С помощью значения, которые собираются в первые два шага, предоставьте пользователю или группе роль владельца RBAC для учетной записи регистрации.

### <a name="resttabrest-2"></a>[REST](#tab/rest-2)

Выполните следующую команду, заменив ```<enrollmentAccountObjectId>``` с `name` вы скопировали на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` с Идентификатором объекта, который вы скопировали на втором шаге.

```json
PUT  https://management.azure.com/providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>/providers/Microsoft.Authorization/roleAssignments/<roleAssignmentGuid>?api-version=2015-07-01

{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>"
  }
}
```

После успешного назначения роли владельца на уровне учетной записи регистрации платформа Azure возвращает сведения о назначении роли.

```json
{
  "properties": {
    "roleDefinitionId": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
    "principalId": "<userObjectId>",
    "scope": "/providers/Microsoft.Billing/enrollmentAccounts/747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
    "createdOn": "2018-03-05T08:36:26.4014813Z",
    "updatedOn": "2018-03-05T08:36:26.4014813Z",
    "createdBy": "<assignerObjectId>",
    "updatedBy": "<assignerObjectId>"
  },
  "id": "/providers/Microsoft.Billing/enrollmentAccounts/providers/Microsoft.Authorization/roleDefinitions/<ownerRoleDefinitionId>",
  "type": "Microsoft.Authorization/roleAssignments",
  "name": "<roleAssignmentGuid>"
}
```

### <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Выполните следующую команду, [New AzRoleAssignment](../active-directory/role-based-access-control-manage-access-powershell.md) команду, заменив ```<enrollmentAccountObjectId>``` с `ObjectId` собранные на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` с объектом идентификатор, собранные на втором шаге.

```azurepowershell-interactive
New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

### <a name="azure-clitabazure-cli-2"></a>[Интерфейс командной строки Azure](#tab/azure-cli-2)

Выполните следующую команду, [Создание назначений ролей az](../active-directory/role-based-access-control-manage-access-azure-cli.md) команду, заменив ```<enrollmentAccountObjectId>``` с `name` вы скопировали на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` с объектом идентификатор, собранные на втором шаге.

```azurecli-interactive
az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
```

----

Когда пользователь становится владельцем RBAC учетной записи регистрации, они могут [программно создавать подписки](programmatically-create-subscription.md) под ним. Для подписки, созданной делегированным пользователем по-прежнему имеет исходный владелец учетной записи, администратором службы, но она также содержит делегированного пользователя как владельца RBAC по умолчанию.

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Аудит пользователей, создающих подписки, с помощью журналов действий

Для отслеживания подписок, создаваемых с помощью данного API, используйте [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs). В настоящее время для отслеживания создания подписки невозможно использовать PowerShell, интерфейс командной строки или портал Azure.

1. В качестве администратора клиента Azure AD [повысьте права доступа](../active-directory/role-based-access-control-tenant-admin-access.md), а затем назначьте роль читателя пользователю-аудитору для области `/providers/microsoft.insights/eventtypes/management`.
1. В качестве пользователя-аудитора вызовите [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs) для просмотра действий создания подписок. Пример:

```
GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'" 
```

> Для удобного вызова API из командной строки попробуйте использовать [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Дальнейшие действия

* Когда пользователь или субъект-служба имеют разрешение на создание подписки, можно использовать его для [программного создания подписок Azure Enterprise](programmatically-create-subscription.md).
* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Дополнительные сведения о Azure Resource Manager и его интерфейсах API см. в статье [Обзор Azure Resource Manager](resource-group-overview.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
* Полное руководство по лучшим методикам управления подписками для крупных организаций приведено в разделе [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
