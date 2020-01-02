---
title: Ограничьте доступ пользователей к операциям с данными только с помощью Azure Cosmos DB
description: Узнайте, как ограничить доступ к операциям с данными только с помощью Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74980378"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Ограничение доступа пользователей только к операциям с данными

В Azure Cosmos DB есть два способа проверки подлинности взаимодействия со службой базы данных:
- Использование удостоверения Azure Active Directory при взаимодействии с портал Azure
- использование [ключей](secure-access-to-data.md#master-keys) Azure Cosmos DB или [маркеров ресурсов](secure-access-to-data.md#resource-tokens) при выдаче вызовов из API и пакетов SDK.

Каждый метод проверки подлинности предоставляет доступ к разным наборам операций с перекрытием: ![разделение операций на тип проверки подлинности](./media/how-to-restrict-user-data/operations.png)

В некоторых сценариях может потребоваться ограничить количество пользователей организации для выполнения операций с данными (то есть запросов CRUD и запросов). Обычно это относится к разработчикам, которым не требуется создавать или удалять ресурсы, или изменять подготовленную пропускную способность контейнеров, над которыми они работают.

Вы можете ограничить доступ, применив следующие шаги.
1. Создание настраиваемой роли Azure Active Directory для пользователей, которым требуется ограничить доступ. Настраиваемая роль Active Directory должна иметь детальный уровень доступа к операциям с помощью [детализированных действий](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB.
1. Запрещение выполнения операций, не связанных с данными, с ключами. Это достигается путем того, что эти операции ограничиваются только Azure Resource Manager вызовах.

В следующих разделах этой статьи показано, как выполнить эти действия.

> [!NOTE]
> Чтобы выполнить команды в следующих разделах, необходимо установить Azure PowerShell Module 3.0.0 или более поздней версии, а также [роль владельца Azure](../role-based-access-control/built-in-roles.md#owner) в подписке, которую вы пытаетесь изменить.

В сценариях PowerShell в следующих разделах Замените следующие заполнители значениями, характерными для вашей среды:
- `$MySubscriptionId` — идентификатор подписки, содержащий учетную запись Azure Cosmos, в которой необходимо ограничить разрешения. Например, `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName` — группа ресурсов, содержащая учетную запись Azure Cosmos. Например, `myresourcegroup`.
- `$MyAzureCosmosDBAccountName` — имя учетной записи Azure Cosmos. Например, `mycosmosdbsaccount`.
- `$MyUserName` — имя входа (username@domain) пользователя, для которого требуется ограничить доступ. Например, `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Выбор подписки Azure

Azure PowerShell командам требуется выполнить вход и выбрать подписку для выполнения команд:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Создание настраиваемой роли Azure Active Directory

Следующий скрипт создает назначение роли Azure Active Directory с доступом только для ключа для учетных записей Azure Cosmos. Эта роль основана на [пользовательских ролях для ресурсов Azure](../role-based-access-control/custom-roles.md) и [детализированных действиях для Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb). Эти роли и действия являются частью пространства имен `Microsoft.DocumentDB` Azure Active Directory.

1. Сначала создайте документ JSON с именем `AzureCosmosKeyOnlyAccess.json` со следующим содержимым:

    ```
    {
        "Name": "Azure Cosmos DB Key Only Access Custom Role",
        "Id": "00000000-0000-0000-0000-0000000000",
        "IsCustom": true,
        "Description": "This role restricts the user to read the account keys only.",
        "Actions":
        [
            "Microsoft.DocumentDB/databaseAccounts/listKeys/action"
        ],
        "NotActions": [],
        "DataActions": [],
        "NotDataActions": [],
        "AssignableScopes":
        [
            "/subscriptions/$MySubscriptionId"
        ]
    }
    ```

1. Выполните следующие команды, чтобы создать назначение роли и назначить его пользователю:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Запретить выполнение операций, не связанных с данными

Следующие команды позволяют удалить возможность использования ключей в следующих разделах:
- Создание, изменение и удаление ресурсов
- Обновите параметры контейнера (включая политики индексирования, пропускную способность и т. д.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения об [управлении доступом на основе ролей Cosmos DB](role-based-access-control.md)
- Получите общие сведения о [безопасном доступе к данным в Cosmos DB](secure-access-to-data.md)
