---
title: Создание настраиваемой роли Azure с помощью шаблона Azure Resource Manager — Azure RBAC
description: Узнайте, как создать настраиваемую роль Azure с помощью шаблонов Azure Resource Manager и управления доступом на основе ролей Azure (Azure RBAC).
services: role-based-access-control,azure-resource-manager
author: rolyon
manager: mtillman
ms.service: role-based-access-control
ms.topic: how-to
ms.custom: subject-armqs
ms.workload: identity
ms.date: 06/25/2020
ms.author: rolyon
ms.openlocfilehash: bcf1966ffc326291448cb611d99390fe0d652151
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "85398043"
---
# <a name="create-an-azure-custom-role-using-an-azure-resource-manager-template"></a>Создание настраиваемой роли Azure с помощью шаблона Azure Resource Manager

Если [встроенные роли Azure](built-in-roles.md) не отвечают конкретным потребностям Организации, можно создать собственные [пользовательские роли](custom-roles.md). В этой статье описывается создание пользовательской роли с помощью шаблона Azure Resource Manager.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

## <a name="prerequisites"></a>Предварительные требования

Чтобы создать настраиваемую роль, необходимо иметь следующее:

- разрешения на создание пользовательских ролей, такие как [Владелец](built-in-roles.md#owner) или [Администратор доступа пользователя](built-in-roles.md#user-access-administrator);

## <a name="create-a-custom-role"></a>Создание настраиваемой роли

Чтобы создать пользовательскую роль, необходимо указать имя роли, разрешения и место, где можно использовать эту роль. В этой статье вы создадите роль с именем "Custom Role-RG Reader" с разрешениями ресурсов, которые могут быть назначены в области действия подписки или ниже.

### <a name="review-the-template"></a>Изучение шаблона

Шаблон, используемый в этой статье, относится к [шаблонам](https://github.com/Azure/azure-quickstart-templates/tree/master/subscription-deployments/create-role-def)быстрого запуска Azure. Шаблон содержит четыре параметра и раздел ресурсов. Четыре параметра:

- Массив действий со значением по умолчанию ["Microsoft. Resources/Subscriptions/resourceGroups/Read"]
- Массив неизменений с пустым значением по умолчанию
- Имя роли со значением по умолчанию "Настраиваемая роль — RG Reader"
- Описание роли со значением по умолчанию "развертывание определения роли на уровне подписки"

В шаблоне определен следующий ресурс:

- [Microsoft. Authorization/roleDefinitions](/azure/templates/Microsoft.Authorization/roleDefinitions)

Областью, в которой можно назначить эту настраиваемую роль, назначается текущая подписка.

:::code language="json" source="~/quickstart-templates/subscription-deployments/create-role-def/azuredeploy.json":::

### <a name="deploy-the-template"></a>Развертывание шаблона

Выполните следующие действия, чтобы развернуть предыдущий шаблон.

1. Войдите на [портал Azure](https://portal.azure.com).

1. Откройте Azure Cloud Shell для PowerShell.

1. Скопируйте и вставьте следующий скрипт в Cloud Shell.

    ```azurepowershell
    $location = Read-Host -Prompt "Enter a location (i.e. centralus)"
    [string[]]$actions = Read-Host -Prompt "Enter actions as a comma-separated list (i.e. action1,action2)"
    $actions = $actions.Split(',')

    $templateUri = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json"

    New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    ```

1. Введите расположение для развертывания, например *centralus*.

1. Введите список действий для пользовательской роли в виде списка с разделителями-запятыми *, например Microsoft. Resources/Resources/Read, Microsoft. Resources/Subscriptions/resourceGroups/Read*.

1. При необходимости нажмите клавишу ВВОД, чтобы выполнить команду New-AzDeployment.

    Команда [New-аздеплоймент](/powershell/module/az.resources/new-azdeployment) развертывает шаблон для создания настраиваемой роли.

    Выходные данные должны иметь следующий вид.

    ```azurepowershell
    PS> New-AzDeployment -Location $location -TemplateUri $templateUri -actions $actions
    
    Id                      : /subscriptions/{subscriptionId}/providers/Microsoft.Resources/deployments/azuredeploy
    DeploymentName          : azuredeploy
    Location                : centralus
    ProvisioningState       : Succeeded
    Timestamp               : 6/25/2020 8:08:32 PM
    Mode                    : Incremental
    TemplateLink            :
                              Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/subscription-deployments/create-role-def/azuredeploy.json
                              ContentVersion : 1.0.0.0
    
    Parameters              :
                              Name               Type                       Value
                              =================  =========================  ==========
                              actions            Array                      [
                                "Microsoft.Resources/resources/read",
                                "Microsoft.Resources/subscriptions/resourceGroups/read"
                              ]
                              notActions         Array                      []
                              roleName           String                     Custom Role - RG Reader
                              roleDescription    String                     Subscription Level Deployment of a Role Definition
    
    Outputs                 :
    DeploymentDebugLogLevel :
    ```

## <a name="review-deployed-resources"></a>Просмотр развернутых ресурсов

Выполните следующие действия, чтобы убедиться, что пользовательская роль создана.

1. Выполните команду [Get-азроледефинитион](/powershell/module/az.resources/get-azroledefinition) , чтобы получить список настраиваемой роли.

    ```azurepowershell
    Get-AzRoleDefinition "Custom Role - RG Reader" | ConvertTo-Json
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже:

    ```azurepowershell
    {
      "Name": "Custom Role - RG Reader",
      "Id": "11111111-1111-1111-1111-111111111111",
      "IsCustom": true,
      "Description": "Subscription Level Deployment of a Role Definition",
      "Actions": [
        "Microsoft.Resources/resources/read",
        "Microsoft.Resources/subscriptions/resourceGroups/read"
      ],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/{subscriptionId}"
      ]
    }
    ```

1. В портал Azure откройте подписку.

1. В меню слева щелкните **Управление доступом (IAM)** .

1. Перейдите на вкладку **роли** .

1. Задайте для списка **типов** значение **кустомроле**.

1. Убедитесь, что в списке указана **пользовательская роль читателя RG** .

   ![Новая настраиваемая роль в портал Azure](./media/custom-roles-template/custom-role-template-portal.png)

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить настраиваемую роль, выполните следующие действия.

1. Выполните следующую команду, чтобы удалить настраиваемую роль.

    ```azurepowershell
    Get-AzRoleDefinition -Name "Custom Role - RG Reader" | Remove-AzRoleDefinition
    ```

1. Введите **Y** , чтобы подтвердить, что вы хотите удалить настраиваемую роль.

## <a name="next-steps"></a>Дальнейшие действия

- [Общие сведения о определениях ролей Azure](role-definitions.md)
- [Краткое руководство. Добавление назначения роли Azure с помощью шаблона Azure Resource Manager](quickstart-role-assignments-template.md)
- [Документация по шаблонам ARM](../azure-resource-manager/templates/index.yml)
