---
title: Предоставить доступ к созданию подписок Azure Enterprise
description: Дополнительные сведения о предоставлении пользователю или субъекту-службе возможности программно создавать подписки Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: b77efd7e5cf7ff016605e0ba2e74cff9ea8dab89
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75478881"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)

Пользователь Azure на [Соглашение Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) может предоставлять разрешение другому пользователю или субъекту-службе на создание подписок, оплаченных от имени своей учетной записи. В этой статье рассказывается об использовании [управления доступом на основе ролей (RBAC)](../../active-directory/role-based-access-control-configure.md) для совместного создания подписок и их аудита. Необходима роль владельца для учетной записи, к которой вы хотите предоставить общий доступ.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Предоставление доступа

Для [создания подписок под учетной записью регистрации](programmatically-create-subscription.md)пользователи должны иметь [роль владельца RBAC](../../role-based-access-control/built-in-roles.md#owner) в этой учетной записи. Вы можете предоставить пользователю или группе пользователей роль владельца RBAC в учетной записи, выдыхая следующие действия:

1. Получите идентификатор объекта учетной записи, к который вы хотите предоставить доступ

    Чтобы предоставить другим роль владельца RBAC на учетной записи, вы должны быть либо владельцем учетной записи, либо владельцем учетной записи RBAC.

    # <a name="rest"></a>[Остальные](#tab/rest)

    Запрос на список всех учетных записей, к которых у вас есть доступ:

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

    Используйте `principalName` свойство для определения учетной записи, к которым вы хотите предоставить доступ владельцу RBAC. Копируйте `name` эту учетную запись. Например, если вы хотите предоставить владельцу RBAC доступ к учетной SignUpEngineering@contoso.com записи, вы копируете. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Это идентификатор объекта учетной записи. Вставьте это значение где-то так, что `enrollmentAccountObjectId`вы можете использовать его в следующем шаге, как .

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ. Выберите **возможность** открыть [облачную оболочку Azure.](https://shell.azure.com/) Чтобы вставить код, нажмите правой щелкните окна оболочки, и выберите **Пасту**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure отвечает списком учетных записей, к которых у вас есть доступ:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Используйте `principalName` свойство для определения учетной записи, к какой учетной записи вы хотите предоставить владельцу RBAC доступ. Копируйте `ObjectId` эту учетную запись. Например, если вы хотите предоставить владельцу RBAC доступ к учетной SignUpEngineering@contoso.com записи, вы копируете. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Вставьте этот идентификатор объекта где-то, `enrollmentAccountObjectId`так что вы можете использовать его на следующем шаге в качестве.

    # <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli)

    Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ. Выберите **возможность** открыть [облачную оболочку Azure.](https://shell.azure.com/) Чтобы вставить код, нажмите правой щелкните окна оболочки, и выберите **Пасту**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure отвечает списком учетных записей, к которых у вас есть доступ:

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

    ---

    Используйте `principalName` свойство для определения учетной записи, к которым вы хотите предоставить доступ владельцу RBAC. Копируйте `name` эту учетную запись. Например, если вы хотите предоставить владельцу RBAC доступ к учетной SignUpEngineering@contoso.com записи, вы копируете. ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Это идентификатор объекта учетной записи. Вставьте это значение где-то так, что `enrollmentAccountObjectId`вы можете использовать его в следующем шаге, как .

1. <a id="userObjectId"></a>Получите идентификатор объекта пользователя или группы, которую вы хотите дать роли владельца RBAC

    1. На портале Azure ищите на **канале Azure Active Directory**.
    1. Если вы хотите предоставить пользователю доступ, нажмите на **пользователей** в меню слева. Если вы хотите предоставить доступ к группе, нажмите **группы**.
    1. Выберите Пользователя или группу, которую вы хотите дать роли владельца RBAC.
    1. Если вы выбрали пользователя, вы найдете идентификатор объекта на странице профиля. При выборе группы идентификатор объекта будет находиться на странице «Обзор». Копируйте **ObjectID,** нажав значок справа от текстового поля. Вставьте это где-то так, что вы `userObjectId`можете использовать его в следующем шаге, как .

1. Предоставить пользователю или группе роль владельца RBAC в учетной записи

    Используя значения, собранные в первых двух шагах, предоставите пользователю или группе роль владельца RBAC в учетной записи.

    # <a name="rest"></a>[Остальные](#tab/rest-2)

    Выполнить следующую команду, ```<enrollmentAccountObjectId>``` `name` заменив с копироваль на первом этапе ().```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Заменить ```<userObjectId>``` идентификатор объекта, скопированный со второго шага.

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

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell-2)

    Выполнить следующую команду [New-AzRoleAssignment,](../../active-directory/role-based-access-control-manage-access-powershell.md) заменив ```<enrollmentAccountObjectId>``` `ObjectId` собранные на первом этапе ().```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Заменить ```<userObjectId>``` идентификатор объекта, собранный на втором этапе.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Лазурный CLI](#tab/azure-cli-2)

    Выполнить следующее [назначение роли az создать](../../active-directory/role-based-access-control-manage-access-azure-cli.md) команду, заменив ```<enrollmentAccountObjectId>``` с скопированным `name` вами на первом этапе ().```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx``` Заменить ```<userObjectId>``` идентификатор объекта, собранный на втором этапе.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Как только пользователь становится владельцем RBAC для вашей учетной записи, он может [программно создавать подписки](programmatically-create-subscription.md) под ним. Подписка, созданная делегированным пользователем, по-прежнему имеет исходного владельца учетной записи в качестве админа службы, но по умолчанию она также имеет делегированного пользователя в качестве владельца RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Аудит пользователей, создающих подписки, с помощью журналов действий

Для отслеживания подписок, создаваемых с помощью данного API, используйте [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs). В настоящее время для отслеживания создания подписки невозможно использовать PowerShell, интерфейс командной строки или портал Azure.

1. В качестве администратора клиента Azure AD [повысьте права доступа](../../active-directory/role-based-access-control-tenant-admin-access.md), а затем назначьте роль читателя пользователю-аудитору для области `/providers/microsoft.insights/eventtypes/management`.
1. В качестве пользователя-аудитора вызовите [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs) для просмотра действий создания подписок. Пример

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Для удобного вызова API из командной строки попробуйте использовать [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Дальнейшие действия

* Когда пользователь или субъект-служба имеют разрешение на создание подписки, можно использовать его для [программного создания подписок Azure Enterprise](programmatically-create-subscription.md).
* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Дополнительные сведения о Azure Resource Manager и его интерфейсах API см. в статье [Обзор Azure Resource Manager](overview.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](../../governance/management-groups/overview.md).
* Полное руководство по лучшим методикам управления подписками для крупных организаций приведено в разделе [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
