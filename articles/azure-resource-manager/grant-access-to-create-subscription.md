---
title: Предоставление доступа для создания подписок Azure Enterprise
description: Дополнительные сведения о предоставлении пользователю или субъекту-службе возможности программно создавать подписки Azure Enterprise.
author: jureid
manager: jureid
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: jureid
ms.openlocfilehash: 8f1f7837b6243f1af49d3b5eb0f3632c5e144f6c
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2019
ms.locfileid: "74149848"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)

Пользователь Azure на [Соглашение Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) может предоставлять разрешение другому пользователю или субъекту-службе на создание подписок, оплаченных от имени своей учетной записи. В этой статье рассказывается об использовании [управления доступом на основе ролей (RBAC)](../active-directory/role-based-access-control-configure.md) для совместного создания подписок и их аудита. Необходима роль владельца для учетной записи, к которой вы хотите предоставить общий доступ.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Предоставление доступа

Для [создания подписок под учетной записью регистрации](programmatically-create-subscription.md)пользователи должны иметь [роль владельца RBAC](../role-based-access-control/built-in-roles.md#owner) для этой учетной записи. Вы можете предоставить пользователю или группе пользователей роль владельца RBAC на учетной записи регистрации, выполнив следующие действия.

1. Получите идентификатор объекта учетной записи регистрации, к которой вы хотите предоставить доступ.

    Чтобы предоставить другим пользователям роль "владелец RBAC" на учетной записи регистрации, необходимо быть владельцем учетной записи или владельцем учетной записи RBAC.

    # <a name="resttabrest"></a>[REST](#tab/rest)

    Запрос на получение списка всех учетных записей регистрации, к которым у вас есть доступ:

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

    Используйте свойство `principalName`, чтобы указать учетную запись, доступ к которой необходимо предоставить владельцу RBAC. Скопируйте `name` этой учетной записи. Например, если вы хотите предоставить владельцу RBAC доступ к учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге как `enrollmentAccountObjectId`.

    # <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

    Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ. Выберите **попробовать** , чтобы открыть [Azure Cloud Shell](https://shell.azure.com/). Чтобы вставить код, щелкните правой кнопкой мыши окна оболочки и выберите команду **Вставить**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Azure реагирует на список учетных записей регистрации, к которым у вас есть доступ:

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Используйте свойство `principalName`, чтобы указать учетную запись, доступ к которой необходимо предоставить владельцу RBAC. Скопируйте `ObjectId` этой учетной записи. Например, если вы хотите предоставить владельцу RBAC доступ к учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Вставьте этот идентификатор объекта где-то, чтобы его можно было использовать на следующем шаге в качестве `enrollmentAccountObjectId`.

    # <a name="azure-clitabazure-cli"></a>[Интерфейс командной строки Azure](#tab/azure-cli)

    Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ. Выберите **попробовать** , чтобы открыть [Azure Cloud Shell](https://shell.azure.com/). Чтобы вставить код, щелкните правой кнопкой мыши окна оболочки и выберите команду **Вставить**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Azure реагирует на список учетных записей регистрации, к которым у вас есть доступ:

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

    Используйте свойство `principalName`, чтобы указать учетную запись, доступ к которой необходимо предоставить владельцу RBAC. Скопируйте `name` этой учетной записи. Например, если вы хотите предоставить владельцу RBAC доступ к учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге как `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Возвращает идентификатор объекта пользователя или группы, которым необходимо предоставить роль владельца RBAC.

    1. В портал Azure выполните поиск по **Azure Active Directory**.
    1. Если вы хотите предоставить пользователю доступ, щелкните **Пользователи** в меню слева. Если вы хотите предоставить доступ к группе, щелкните **группы**.
    1. Выберите пользователя или группу, которой требуется назначить роль владельца RBAC.
    1. Если вы выбрали пользователя, идентификатор объекта можно найти на странице профиля. Если вы выбрали группу, идентификатор объекта будет отображаться на странице Обзор. Скопируйте **ObjectID** , щелкнув значок справа от текстового поля. Вставьте это место, чтобы можно было использовать его на следующем шаге как `userObjectId`.

1. Предоставление пользователю или группе роли владельца RBAC в учетной записи регистрации

    Используя значения, собранные в первых двух шагах, предоставьте пользователю или группе роль владельца RBAC в учетной записи регистрации.

    # <a name="resttabrest-2"></a>[REST](#tab/rest-2)

    Выполните следующую команду, заменив ```<enrollmentAccountObjectId>``` на `name`, скопированный на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` ИДЕНТИФИКАТОРом объекта, скопированным на втором шаге.

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

    # <a name="powershelltabazure-powershell-2"></a>[PowerShell](#tab/azure-powershell-2)

    Выполните следующую команду [New-азролеассигнмент](../active-directory/role-based-access-control-manage-access-powershell.md) , заменив ```<enrollmentAccountObjectId>``` на `ObjectId`, собранные на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` ИДЕНТИФИКАТОРом объекта, собранным на втором шаге.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-clitabazure-cli-2"></a>[Интерфейс командной строки Azure](#tab/azure-cli-2)

    Выполните следующую команду [AZ Role назначений Create](../active-directory/role-based-access-control-manage-access-azure-cli.md) , заменив ```<enrollmentAccountObjectId>``` на `name`, скопированный на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` ИДЕНТИФИКАТОРом объекта, собранным на втором шаге.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    После того как пользователь станет владельцем учетной записи регистрации RBAC, он сможет [программно создавать подписки](programmatically-create-subscription.md) . Подписка, созданная делегированным пользователем, по-прежнему имеет исходного владельца учетной записи в качестве администратора службы, но по умолчанию она также имеет делегированного пользователя в качестве владельца RBAC.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Аудит пользователей, создающих подписки, с помощью журналов действий

Для отслеживания подписок, создаваемых с помощью данного API, используйте [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs). В настоящее время для отслеживания создания подписки невозможно использовать PowerShell, интерфейс командной строки или портал Azure.

1. В качестве администратора клиента Azure AD [повысьте права доступа](../active-directory/role-based-access-control-tenant-admin-access.md), а затем назначьте роль читателя пользователю-аудитору для области `/providers/microsoft.insights/eventtypes/management`.
1. В качестве пользователя-аудитора вызовите [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs) для просмотра действий создания подписок. Пример:

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Для удобного вызова API из командной строки попробуйте использовать [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Дополнительная информация

* Когда пользователь или субъект-служба имеют разрешение на создание подписки, можно использовать его для [программного создания подписок Azure Enterprise](programmatically-create-subscription.md).
* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Дополнительные сведения о Azure Resource Manager и его интерфейсах API см. в статье [Обзор Azure Resource Manager](resource-group-overview.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](management-groups-overview.md).
* Полное руководство по лучшим методикам управления подписками для крупных организаций приведено в разделе [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
