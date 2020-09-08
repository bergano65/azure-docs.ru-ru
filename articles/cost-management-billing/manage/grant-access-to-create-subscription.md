---
title: Предоставление доступа для создания подписок Azure Enterprise
description: Дополнительные сведения о предоставлении пользователю или субъекту-службе возможности программно создавать подписки Azure Enterprise.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.reviewer: amberb
ms.topic: conceptual
ms.date: 08/26/2020
ms.author: banders
ms.openlocfilehash: b154d723e82d02ea864459ef65eb5c05c14ae336
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88943187"
---
# <a name="grant-access-to-create-azure-enterprise-subscriptions-preview"></a>Предоставление доступа к созданию подписок Azure Enterprise (предварительная версия)

Пользователь Azure на [Соглашение Enterprise](https://azure.microsoft.com/pricing/enterprise-agreement/) может предоставлять разрешение другому пользователю или субъекту-службе на создание подписок, оплаченных от имени своей учетной записи. В этой статье рассказывается об использовании [управления доступом на основе ролей Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) для совместного создания подписок и их аудита. Необходима роль владельца для учетной записи, к которой вы хотите предоставить общий доступ.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="grant-access"></a>Предоставление доступа

Чтобы [создать подписки в учетной записи регистрации](programmatically-create-subscription.md), у пользователей должна быть [роль владельца RBAC](../../role-based-access-control/built-in-roles.md#owner) для этой учетной записи. Вы можете предоставить пользователю или группе пользователей роль владельца RBAC для учетной записи регистрации, выполнив следующие действия.

1. Получите идентификатор объекта учетной записи регистрации, к которой вы хотите предоставить доступ.

    Чтобы предоставить другим пользователям роль владельца RBAC для учетной записи регистрации, необходимо быть владельцем учетной записи или владельцем RBAC учетной записи.

    # <a name="rest"></a>[REST](#tab/rest)

    Запросите вывод списка всех учетных записей регистрации, к которым у вас есть доступ.

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

    Используйте свойство `principalName`, чтобы указать учетную запись, к которой вы хотите предоставить доступ на уровне владельца RBAC. Скопируйте значение `name` этой учетной записи. Например, если вы хотите предоставить доступ на уровне владельца RBAC к учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге в качестве `enrollmentAccountObjectId`.

    # <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

    Используйте командлет [Get-AzEnrollmentAccount](/powershell/module/az.billing/get-azenrollmentaccount), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ. Выберите **Попробовать**, чтобы открыть [Azure Cloud Shell](https://shell.azure.com/). Чтобы вставить код, щелкните окна оболочки правой кнопкой мыши, а затем выберите **Вставить**.

    ```azurepowershell-interactive
    Get-AzEnrollmentAccount
    ```

    Платформа Azure выведет список учетных записей регистрации, к которым у вас есть доступ.

    ```azurepowershell
    ObjectId                               | PrincipalName
    747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | SignUpEngineering@contoso.com
    4cd2fcf6-xxxx-xxxx-xxxx-xxxxxxxxxxxx   | BillingPlatformTeam@contoso.com
    ```

    Используйте свойство `principalName`, чтобы указать учетную запись, к которой вы хотите предоставить доступ на уровне владельца RBAC. Скопируйте значение `ObjectId` этой учетной записи. Например, если вы хотите предоставить доступ на уровне владельца RBAC к учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Вставьте этот идентификатор объекта в любое место, чтобы его можно было использовать на следующем шаге в качестве `enrollmentAccountObjectId`.

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

    Используйте [команду Get-EnrollmentAccount](https://aka.ms/EASubCreationPublicPreviewCLI), чтобы вывести список всех учетных записей регистрации, к которым у вас есть доступ. Выберите **Попробовать**, чтобы открыть [Azure Cloud Shell](https://shell.azure.com/). Чтобы вставить код, щелкните окна оболочки правой кнопкой мыши, а затем выберите **Вставить**.

    ```azurecli-interactive
    az billing enrollment-account list
    ```

    Платформа Azure выведет список учетных записей регистрации, к которым у вас есть доступ.

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

    Используйте свойство `principalName`, чтобы указать учетную запись, к которой вы хотите предоставить доступ на уровне владельца RBAC. Скопируйте значение `name` этой учетной записи. Например, если вы хотите предоставить доступ на уровне владельца RBAC к учетной записи регистрации SignUpEngineering@contoso.com, скопируйте ```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```. Это идентификатор объекта учетной записи регистрации. Вставьте это значение в любое место, чтобы его можно было использовать на следующем шаге в качестве `enrollmentAccountObjectId`.

1. <a id="userObjectId"></a>Получите идентификатор объекта пользователя или группы, которым необходимо предоставить роль владельца RBAC.

    1. На портале Azure найдите **Azure Active Directory**.
    1. Если вы хотите предоставить доступ пользователю, в меню слева выберите **Пользователи**. Чтобы предоставить доступ группе, выберите **Группы**.
    1. Выберите пользователя или группу, которым требуется назначить роль владельца RBAC.
    1. Если вы выбрали пользователя, идентификатор объекта можно найти на странице профиля. Если вы выбрали группу, идентификатор объекта будет отображаться на странице "Обзор". Скопируйте идентификатор **ObjectID**, щелкнув значок справа от текстового поля. Вставьте его в любое место, чтобы его можно было использовать на следующем шаге в качестве `userObjectId`.

1. Предоставьте пользователю или группе роль владельца RBAC для учетной записи регистрации.

    Используя значения, собранные на первых двух шагах, предоставьте пользователю или группе роль владельца RBAC для учетной записи регистрации.

    # <a name="rest"></a>[REST](#tab/rest-2)

    Воспользуйтесь следующей командой, заменив ```<enrollmentAccountObjectId>``` на значение `name`, которое вы скопировали на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` идентификатором объекта, скопированным на втором шаге.

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

    Воспользуйтесь командой [New-AzRoleAssignment](../../role-based-access-control/role-assignments-powershell.md), заменив ```<enrollmentAccountObjectId>``` значением `ObjectId`, полученным на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` идентификатором объекта, полученным на втором шаге.

    ```azurepowershell-interactive
    New-AzRoleAssignment -RoleDefinitionName Owner -ObjectId <userObjectId> -Scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    # <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli-2)

    Воспользуйтесь командой [az role assignment create](../../role-based-access-control/role-assignments-cli.md), заменив ```<enrollmentAccountObjectId>``` значением `name`, полученным на первом шаге (```747ddfe5-xxxx-xxxx-xxxx-xxxxxxxxxxxx```). Замените ```<userObjectId>``` идентификатором объекта, полученным на втором шаге.

    ```azurecli-interactive
    az role assignment create --role Owner --assignee-object-id <userObjectId> --scope /providers/Microsoft.Billing/enrollmentAccounts/<enrollmentAccountObjectId>
    ```

    Когда пользователь становится владельцем RBAC учетной записи регистрации, он получает возможность [программно создавать подписки](programmatically-create-subscription.md) в рамках этой учетной записи. Для подписки, созданной делегированным пользователем, администратором службы по-прежнему остается исходный владелец учетной записи. Однако делегированный пользователь также является ее владельцем RBAC по умолчанию.

    ---

## <a name="audit-who-created-subscriptions-using-activity-logs"></a>Аудит пользователей, создающих подписки, с помощью журналов действий

Для отслеживания подписок, создаваемых с помощью данного API, используйте [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs). В настоящее время для отслеживания создания подписки невозможно использовать PowerShell, интерфейс командной строки или портал Azure.

1. В качестве администратора клиента Azure AD [повысьте права доступа](../../role-based-access-control/elevate-access-global-admin.md), а затем назначьте роль читателя пользователю-аудитору для области `/providers/microsoft.insights/eventtypes/management`. Такой доступ предоставляется для роли [Читатель](../../role-based-access-control/built-in-roles.md#reader), [Участник мониторинга](../../role-based-access-control/built-in-roles.md#monitoring-contributor) или [пользовательской роли](../../role-based-access-control/custom-roles.md).
1. В качестве пользователя-аудитора вызовите [API журнала действий клиента](/rest/api/monitor/tenantactivitylogs) для просмотра действий создания подписок. Пример.

    ```
    GET "/providers/Microsoft.Insights/eventtypes/management/values?api-version=2015-04-01&$filter=eventTimestamp ge '{greaterThanTimeStamp}' and eventTimestamp le '{lessThanTimestamp}' and eventChannels eq 'Operation' and resourceProvider eq 'Microsoft.Subscription'"
    ```

Для удобного вызова API из командной строки попробуйте использовать [ARMClient](https://github.com/projectkudu/ARMClient).

## <a name="next-steps"></a>Дальнейшие действия

* Когда пользователь или субъект-служба имеют разрешение на создание подписки, можно использовать его для [программного создания подписок Azure Enterprise](programmatically-create-subscription.md).
* Создание подписок с помощью .NET: [пример кода на сайте GitHub](https://github.com/Azure-Samples/create-azure-subscription-dotnet-core).
* Дополнительные сведения о Azure Resource Manager и его интерфейсах API см. в статье [Обзор Azure Resource Manager](../../azure-resource-manager/management/overview.md).
* Дополнительные сведения о переносе большого числа подписок с помощью групп управления см. в руководстве по [упорядочиванию ресурсов с помощью групп управления Azure](../../governance/management-groups/overview.md).
* Полное руководство по лучшим методикам управления подписками для крупных организаций приведено в разделе [Корпоративный каркас Azure: рекомендуемая система управления подписками](/azure/architecture/cloud-adoption-guide/subscription-governance).
