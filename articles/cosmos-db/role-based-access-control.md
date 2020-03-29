---
title: Управление доступом на основе ролей в Azure Cosmos DB
description: Узнайте, как Azure Cosmos DB обеспечивает защиту базы данных при активной интеграции каталогов (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 0c7332a42751b35b6ad8ec3f88afb7bc78cc85e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75445099"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Управление доступом на основе ролей в Azure Cosmos DB

Azure Cosmos DB обеспечивает встроенный элемент управления доступом на основе ролей (RBAC) для общих сценариев управления в Azure Cosmos DB. Человек, у которого есть профиль в Active Directory Azure, может назначить эти роли RBAC пользователям, группам, руководителям служб или управляемым идентификаторам, чтобы предоставить или лишить доступа к ресурсам и операциям на ресурсах Azure Cosmos DB. Назначения ролей используются только для доступа к управлению самолетом, который включает доступ к учетным записям Azure Cosmos, базам данных, контейнерам и предложениям (пропускной способ).

## <a name="built-in-roles"></a>Встроенные роли

Ниже приведены встроенные роли, поддерживаемые Azure Cosmos DB:

|**Встроенные роли**  |**Описание**  |
|---------|---------|
|[Участник учетной записи DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Может управлять учетными записями Azure Cosmos DB|
|[Читатель учетной записи Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Позволяет считывать данные учетных записей Azure Cosmos DB.|
|[Оператор резервного копирования «Космос»](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Можно отправить запрос на восстановление базы данных Azure Cosmos или контейнера.|
|[Оператор Космос ДБ](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Может предоставлять учетные записи, базы данных и контейнеры Azure Cosmos, но не может получить доступ к ключам, необходимым для доступа к данным.|

> [!IMPORTANT]
> Поддержка RBAC в Azure Cosmos DB применяется только к управлению операциями. Операции плоскости данных обеспечиваются с помощью ключевых ключей или токенов ресурсов. Чтобы узнать больше, смотрите [Безопасный доступ к данным в Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Управление идентификацией и доступом (IAM)

Панель **управления доступом (IAM)** на портале Azure используется для настройки управления доступом на основе ролей на ресурсах Azure Cosmos. Роли применяются к пользователям, группам, директорам служб и управляемым идентификаторам в Active Directory. Встроенные роли или пользовательские роли можно использовать для отдельных лиц и групп. На следующем скриншоте показана интеграция Active Directory (RBAC) с использованием управления доступом (IAM) на портале Azure:

![Управление доступом (IAM) на портале Azure: демонстрация безопасности базы данных](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Пользовательские роли

Помимо встроенных ролей, пользователи могут также создавать [пользовательские роли](../role-based-access-control/custom-roles.md) в Azure и применять эти роли к принципам обслуживания во всех подписях в рамках арендатора Active Directory. Пользовательские роли предоставляют пользователям возможность создания определений ролей RBAC с пользовательским набором операций поставщика ресурсов. Чтобы узнать, какие операции доступны для создания пользовательских ролей для Azure Cosmos DB, см., [поставщик ресурсов Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)

## <a name="preventing-changes-from-cosmos-sdk"></a>Предотвращение изменений от Космос SDK

Поставщик ресурсов Cosmos может быть заблокирован, чтобы предотвратить любые изменения в ресурсах, включая учетную запись Cosmos, базы данных, контейнеры и пропускную емкость от любого клиента, соединяющего через ключи счета (т.е. приложения, соединяющие через Cosmos SDK). При наборе изменения в любом ресурсе должны быть от пользователя с надлежащей ролью RBAC и учетными данными. Эта возможность устанавливается `disableKeyBasedMetadataWriteAccess` с значением свойства в поставщике ресурсов Cosmos. Приведен пример шаблона управления ресурсами Azure с этим параметром свойств ниже.

```json
{
    {
      "type": "Microsoft.DocumentDB/databaseAccounts",
      "name": "[variables('accountName')]",
      "apiVersion": "2019-08-01",
      "location": "[parameters('location')]",
      "kind": "GlobalDocumentDB",
      "properties": {
        "consistencyPolicy": "[variables('consistencyPolicy')[parameters('defaultConsistencyLevel')]]",
        "locations": "[variables('locations')]",
        "databaseAccountOfferType": "Standard",
        "disableKeyBasedMetadataWriteAccess": true
        }
    }
}
```

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое элемент управления доступом на основе ролей (RBAC) для ресурсов Azure](../role-based-access-control/overview.md)
- [Пользовательские роли для ресурсов Azure](../role-based-access-control/custom-roles.md)
- [Операции поставщика ресурсов Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
