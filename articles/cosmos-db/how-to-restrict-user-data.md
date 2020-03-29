---
title: Ограничьте доступ пользователей к операциям данных только с помощью Azure Cosmos DB
description: Узнайте, как ограничить доступ к операциям данных только с Помощью Azure Cosmos DB
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/9/2019
ms.author: tvoellm
ms.openlocfilehash: 03cad9e4c3752b5f35be785a6280bf18aaa14860
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74980378"
---
# <a name="restrict-user-access-to-data-operations-only"></a>Ограничение доступа пользователей только операциями с данными

В Azure Cosmos DB существует два способа аутентификации взаимодействия с службой базы данных:
- используя иноеудостоверения Active Directory Azure при взаимодействии с порталом Azure,
- использование [ключей](secure-access-to-data.md#master-keys) DB Azure Cosmos или [токенов ресурсов](secure-access-to-data.md#resource-tokens) при выдаче звонков от AIS и SDK.

Каждый метод проверки подлинности дает доступ к ![различным наборам операций, с некоторым совпадением: Разделение операций по типу аутентификации](./media/how-to-restrict-user-data/operations.png)

В некоторых сценариях может потребоваться ограничить некоторых пользователей организации выполнять операции данных (то есть только запросы и запросы CRUD). Обычно это относится к разработчикам, которым не нужно создавать или удалять ресурсы или изменять прокладку подготовленных контейнеров, над которыми они работают.

Вы можете ограничить доступ, применяя следующие действия:
1. Создание пользовательской роли активного каталога Azure для пользователей, к которым требуется ограничить доступ. Пользовательская роль Active Directory должна иметь тонкий уровень доступа к операциям с использованием [гранулированных действий](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)Azure Cosmos DB.
1. Отказ при выполнении операций, не связанных с данными, с помощью ключей. Этого можно достичь, ограничив эти операции только вызовами диспетчера ресурсов Azure.

В следующих разделах этой статьи показаны способы выполнения этих шагов.

> [!NOTE]
> Для выполнения команд в следующих разделах необходимо установить модуль Azure PowerShell 3.0.0 или позже, а также [роль владельца Azure](../role-based-access-control/built-in-roles.md#owner) в подписке, которую вы пытаетесь изменить.

В сценариях PowerShell в следующих разделах замените следующие заполнители значениями, специфичными для вашей среды:
- `$MySubscriptionId`- Идентификатор подписки, содержащий учетную запись Azure Cosmos, где требуется ограничить разрешения. Например: `e5c8766a-eeb0-40e8-af56-0eb142ebf78e`.
- `$MyResourceGroupName`- Группа ресурсов, содержащая учетную запись Azure Cosmos. Например: `myresourcegroup`.
- `$MyAzureCosmosDBAccountName`- Название вашей учетной записи Azure Cosmos. Например: `mycosmosdbsaccount`.
- `$MyUserName`- Логинusername@domain() пользователя, для которого вы хотите ограничить доступ. Например: `cosmosdbuser@contoso.com`.

## <a name="select-your-azure-subscription"></a>Выбор подписки Azure

Команды Azure PowerShell требуют входа и выбора подписки для выполнения команд:

```azurepowershell
Login-AzAccount
Select-AzSubscription $MySubscriptionId
```

## <a name="create-the-custom-azure-active-directory-role"></a>Создание пользовательской роли активного каталога Azure

Следующий скрипт создает назначение ролей Active Directory Azure с доступом «Только ключ» для учетных записей Azure Cosmos. Роль основана на [пользовательских ролях для ресурсов Azure](../role-based-access-control/custom-roles.md) и [гранулированных действиях для Azure Cosmos DB.](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) Эти роли и действия `Microsoft.DocumentDB` являются частью пространства имен Active Directory Azure.

1. Во-первых, создайте `AzureCosmosKeyOnlyAccess.json` документ JSON с названием со следующим содержанием:

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

1. Выполнить следующие команды, чтобы создать назначение роли и назначить его пользователю:

    ```azurepowershell
    New-AzRoleDefinition -InputFile "AzureCosmosKeyOnlyAccess.json"
    New-AzRoleAssignment -SignInName $MyUserName -RoleDefinitionName "Azure Cosmos DB Key Only Access Custom Role" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName -ResourceType "Microsoft.DocumentDb/databaseAccounts"
    ```

## <a name="disallow-the-execution-of-non-data-operations"></a>Запретить выполнение операций, не связанных с данными

Следующие команды удаляют возможность использования ключей к:
- создавать, изменять или удалять ресурсы
- настройки контейнеров (включая политики индексирования, пропускную емкость и т.д.).

```azurepowershell
$cdba = Get-AzResource -ResourceType "Microsoft.DocumentDb/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName $MyResourceGroupName -ResourceName $MyAzureCosmosDBAccountName
$cdba.Properties.disableKeyBasedMetadataWriteAccess="True"
$cdba | Set-AzResource -Force
```

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [элементах управления доступом на основе роли Cosmos DB](role-based-access-control.md)
- Получить обзор [безопасного доступа к данным в Cosmos DB](secure-access-to-data.md)
