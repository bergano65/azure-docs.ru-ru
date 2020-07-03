---
title: Управление доступом на основе ролей в Azure Cosmos DB
description: Узнайте, как Azure Cosmos DB обеспечивает защиту базы данных с помощью интеграции с Active Directory (RBAC).
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 10/31/2019
ms.author: mjbrown
ms.openlocfilehash: 4e028e7a5e7e7b8f747d7a1cfb36c553a8113544
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "82583737"
---
# <a name="role-based-access-control-in-azure-cosmos-db"></a>Управление доступом на основе ролей в Azure Cosmos DB

Azure Cosmos DB предоставляет встроенные возможности управления доступом на основе ролей (RBAC) для распространенных сценариев управления в Azure Cosmos DB. Пользователь, имеющий профиль в Azure Active Directory, может назначать эти роли RBAC пользователям, группам, субъектам-службам или управляемым удостоверениям для предоставления или запрета доступа к ресурсам и операциям в ресурсах Azure Cosmos DB. Назначения ролей предоставляются только для доступа к плоскости управления, включая доступ к учетным записям Azure Cosmos, базам данных, контейнерам и предложениям (пропускная способность).

## <a name="built-in-roles"></a>Встроенные роли

Ниже перечислены встроенные роли, поддерживаемые Azure Cosmos DB.

|**Встроенные роли**  |**Описание**  |
|---------|---------|
|[Участник учетной записи DocumentDB](../role-based-access-control/built-in-roles.md#documentdb-account-contributor)|Может управлять учетными записями Azure Cosmos DB|
|[Средство чтения учетных записей Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-account-reader-role)|Позволяет считывать данные учетных записей Azure Cosmos DB.|
|[Оператор резервного копирования Cosmos](../role-based-access-control/built-in-roles.md#cosmosbackupoperator)|Может отправлять запрос на восстановление для базы данных Azure Cosmos или контейнера.|
|[Оператор Cosmos DB](../role-based-access-control/built-in-roles.md#cosmos-db-operator)|Может подготавливать учетные записи, базы данных и контейнеры Azure Cosmos, но не может получить доступ к ключам, необходимым для доступа к данным.|

> [!IMPORTANT]
> Поддержка RBAC в Azure Cosmos DB применяется только для операций управления плоскостью. Операции с плоскостью данных защищаются с помощью главных ключей или маркеров ресурсов. Дополнительные сведения см. в разделе [безопасный доступ к данным в Azure Cosmos DB](secure-access-to-data.md)

## <a name="identity-and-access-management-iam"></a>Управление удостоверениями и доступом (IAM)

Панель **управления доступом (IAM)** в портал Azure используется для настройки управления доступом на основе ролей в ресурсах Azure Cosmos. Роли применяются к пользователям, группам, субъектам-службам и управляемым удостоверениям в Active Directory. Для частных пользователей и групп можно использовать встроенные роли или пользовательские роли. На следующем снимке экрана показана Active Directoryная интеграция (RBAC) с использованием управления доступом (IAM) в портал Azure.

![Управление доступом (IAM) на портале Azure: демонстрация безопасности базы данных](./media/role-based-access-control/database-security-identity-access-management-rbac.png)

## <a name="custom-roles"></a>Пользовательские роли

В дополнение к встроенным ролям пользователи также могут создавать [пользовательские роли](../role-based-access-control/custom-roles.md) в Azure и применять эти роли к субъектам-службам во всех подписках в их Active Directory клиенте. Пользовательские роли предоставляют пользователям способ создания определений ролей RBAC с настраиваемым набором операций поставщика ресурсов. Чтобы узнать, какие операции доступны для создания пользовательских ролей для Azure Cosmos DB см. [Azure Cosmos DB операции с поставщиками ресурсов](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb) .

## <a name="preventing-changes-from-cosmos-sdk"></a>Предотвращение изменений из пакета SDK для Cosmos

Поставщик ресурсов Cosmos можно заблокировать, чтобы предотвратить любые изменения в ресурсах, включая учетную запись Cosmos, базы данных, контейнеры и пропускную способность от любого клиента, подключающегося через ключи учетной записи (т. е. приложения, подключающиеся через пакет SDK Cosmos). Если этот параметр задан, изменения в любом ресурсе должны принадлежать пользователю с правильной ролью RBAC и учетными данными. Эта возможность устанавливается с помощью `disableKeyBasedMetadataWriteAccess` значения свойства в поставщике ресурсов Cosmos. Ниже приведен пример шаблона Azure Resource Manager с этим значением свойства.

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
Если вы экспортируете существующий шаблон диспетчер ресурсов и обновляете его с помощью этого свойства, он может полностью заменить функциональность шаблона. Поэтому, если все значения не включены, они будут сброшены по умолчанию. Другим способом отключения доступа на запись метаданных на основе ключа является использование Azure CLI, как показано в следующей команде:

```cli
az cosmosdb update  --name CosmosDBAccountName --resource-group ResourceGroupName  --disable-key-based-metadata-write-access true

```

## <a name="next-steps"></a>Дальнейшие действия

- [Что такое управление доступом на основе ролей (RBAC) для ресурсов Azure](../role-based-access-control/overview.md)
- [Пользовательские роли для ресурсов Azure](../role-based-access-control/custom-roles.md)
- [Операции поставщика ресурсов Azure Cosmos DB](../role-based-access-control/resource-provider-operations.md#microsoftdocumentdb)
