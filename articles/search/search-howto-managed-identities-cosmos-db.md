---
title: Настройка подключения к учетной записи Cosmos DB с использованием управляемого удостоверения (предварительная версия)
titleSuffix: Azure Cognitive Search
description: Сведения о том, как настроить подключение индексатора к учетной записи Cosmos DB с использованием управляемого удостоверения (предварительная версия)
manager: luisca
author: markheff
ms.author: maheff
ms.devlang: rest-api
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 05/18/2020
ms.openlocfilehash: f65aa4b307108682fa6e190a229e9d82b6efdec0
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88553208"
---
# <a name="set-up-an-indexer-connection-to-a-cosmos-db-database-using-a-managed-identity-preview"></a>Настройка подключения индексатора к базе данных Cosmos DB с использованием управляемого удостоверения (предварительная версия)

> [!IMPORTANT] 
> Поддержка настройки подключения к источнику данных с помощью управляемого удостоверения в настоящее время доступна в общедоступной предварительной версии. Для предварительной версии функции соглашение об уровне обслуживания не предусмотрено. Мы не рекомендуем использовать ее в рабочей среде.

На этой странице описывается, как настроить подключение индексатора к базе данных Cosmos DB с использованием управляемого удостоверения вместо того, чтобы включать учетные данные в строку подключения к объекту источника данных.

Перед изучением этой функции желательно иметь представление о том, что такое индексатор и как настроить индексатор для источника данных. См. подробнее в следующих статьях:
* [Обзор индексатора](search-indexer-overview.md)
* [Индексатор Azure Cosmos DB](search-howto-index-cosmosdb.md)

## <a name="set-up-a-connection-using-a-managed-identity"></a>Настройка подключения с помощью управляемого удостоверения

### <a name="1---turn-on-system-assigned-managed-identity"></a>1\. Включение управляемого удостоверения, назначаемого системой

Если включено управляемое удостоверение, назначаемое системой, Azure самостоятельно создаст удостоверение для службы поиска, которое можно использовать для проверки подлинности в других службах Azure для того же клиента и той же подписки. Это удостоверение вы можете использовать в назначениях для управления доступом на основе ролей (RBAC), чтобы разрешить доступ к данным на время индексирования.

![Включение управляемого удостоверения, назначаемого системой](./media/search-managed-identities/turn-on-system-assigned-identity.png "Включение управляемого удостоверения, назначаемого системой")

Нажав **Сохранить**, вы увидите идентификатор объекта, назначенный вашей службе поиска.

![Идентификатор объекта](./media/search-managed-identities/system-assigned-identity-object-id.png "Идентификатор объекта.")
 
### <a name="2---add-a-role-assignment"></a>2\. Добавление назначения роли

На этом шаге вы предоставите службе "Когнитивный поиск Azure" разрешения на чтение данных из базы данных Cosmos DB.

1. На портале Azure перейдите к учетной записи Cosmos DB, которая содержит данные для индексирования.
2. Выберите **Управление доступом (IAM)**
3. Щелкните **Добавить**, а затем **Добавить назначение роли**.

    ![Добавление назначения роли](./media/search-managed-identities/add-role-assignment-cosmos-db.png "Добавление назначения роли")

4. Выберите **Роль читателя учетных записей Cosmos DB**.
5. В поле **Назначение доступа к** сохраните выбранное значение **Пользователь, группа или субъект-служба Azure AD**.
6. Найдите службу поиска, выберите ее и щелкните **Сохранить**.

    ![Назначение роли для чтения и доступа к данным](./media/search-managed-identities/add-role-assignment-cosmos-db-account-reader-role.png "Назначение роли для чтения и доступа к данным")

### <a name="3---create-the-data-source"></a>3\. Создание источника данных

[REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source), портал Azure и [пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.search.models.datasource?view=azure-dotnet) поддерживают строку подключения управляемого удостоверения. Ниже приведен пример создания источника данных для индексирования данных из Cosmos DB с помощью [REST API](https://docs.microsoft.com/rest/api/searchservice/create-data-source) и строки подключения управляемой идентификации. Формат строки подключения управляемого удостоверения одинаков для REST API, пакета SDK для .NET и портал Azure.

При использовании управляемых удостоверений для проверки подлинности **учетные данные** не будут содержать ключ учетной записи.

```
POST https://[service name].search.windows.net/datasources?api-version=2020-06-30
Content-Type: application/json
api-key: [Search service admin key]

{
    "name": "cosmos-db-datasource",
    "type": "cosmosdb",
    "credentials": {
        "connectionString": "Database=sql-test-db;ResourceId=/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/cosmos-db-resource-group/providers/Microsoft.DocumentDB/databaseAccounts/my-cosmos-db-account/;"
    },
    "container": { "name": "myCollection", "query": null },
    "dataChangeDetectionPolicy": {
        "@odata.type": "#Microsoft.Azure.Search.HighWaterMarkChangeDetectionPolicy",
        "highWaterMarkColumnName": "_ts"
    }
}
```    

Текст запроса содержит определение источника данных, который должен включать следующие поля.

| Поле   | Описание |
|---------|-------------|
| **name** | Обязательный элемент. Введите любое имя для представления вашего объекта источника данных. |
|**type**| Обязательный элемент. Этот параметр должен содержать значение `cosmosdb`. |
|**credentials** | Обязательный элемент. <br/><br/>При подключении с использованием управляемого удостоверения формат **учетных данных** должен быть следующим: *Database=[имя_базы_данных];ResourceId=[строка_ИД_ресурса];(ApiKind=[тип_API];)*<br/> <br/>Формат ResourceId: *ResourceId=/subscriptions/**идентификатор_подписки**/resourceGroups/**имя_группы_ресурсов**/providers/Microsoft.DocumentDB/databaseAccounts/**имя_учетной_записи_Cosmos_DB**/;*<br/><br/>Для коллекций SQL указание параметра ApiKind в строке подключения не требуется.<br/><br/>Для коллекций MongoDB добавьте **ApiKind=MongoDb** в строку подключения. <br/><br/>Для графов Gremlin и таблиц Cassandra зарегистрируйтесь для [предварительной версии индексатора](https://aka.ms/azure-cognitive-search/indexer-preview), чтобы получить доступ к предварительной версии и сведениям о том, как отформатировать учетные данные.<br/>|
| **container** | В данной вкладке содержатся следующие элементы. <br/>**name**. Обязательный элемент. Укажите идентификатор коллекции базы данных, которая будет индексироваться.<br/>**query**. Необязательный параметр. Можно указать запрос на сведение произвольного документа JSON в неструктурированную схему, индексируемую Когнитивным поиском Azure.<br/>Для API MongoDB, Gremlin и Cassandra запросы не поддерживаются. |
| **dataChangeDetectionPolicy** | Рекомендуемая |
|**dataDeletionDetectionPolicy** | Необязательно |

### <a name="4---create-the-index"></a>4\. Создание индекса

Индекс задает поля в документе, атрибуты, и другие компоненты, которые определяют процедуру поиска.

Создать индекс с полем `booktitle`, доступным для поиска, можно следующим образом.

```
POST https://[service name].search.windows.net/indexes?api-version=2020-06-30
Content-Type: application/json
api-key: [admin key]

{
    "name" : "my-target-index",
    "fields": [
    { "name": "id", "type": "Edm.String", "key": true, "searchable": false },
    { "name": "booktitle", "type": "Edm.String", "searchable": true, "filterable": false, "sortable": false, "facetable": false }
    ]
}
```

Дополнительные сведения о создании индексов см. в статье [Create Index](https://docs.microsoft.com/rest/api/searchservice/create-index) (Создание индекса).

### <a name="5---create-the-indexer"></a>5\. Создание индексатора

Индексатор соединяет источник данных с целевым индексом поиска и предоставляет расписание для автоматизации обновления данных.

Когда индекс и источник данных уже созданы, можно создать индексатор.

Пример определения индексатора.

```http
    POST https://[service name].search.windows.net/indexers?api-version=2020-06-30
    Content-Type: application/json
    api-key: [admin key]

    {
      "name" : "cosmos-db-indexer",
      "dataSourceName" : "cosmos-db-datasource",
      "targetIndexName" : "my-target-index",
      "schedule" : { "interval" : "PT2H" }
    }
```

Этот индексатор будет выполняться каждые два часа (интервал расписания имеет значение "PT2H"). Чтобы запускать индексатор каждые 30 минут, задайте интервал "PT30M". Самый короткий интервал, который можно задать, составляет 5 минут. Расписание является необязательным. Если оно не указано, то индексатор выполняется только один раз при его создании. Однако индексатор можно запустить по запросу в любое время.   

Дополнительные сведения об API создания индексатора см. в разделе [Создание индексатора](https://docs.microsoft.com/rest/api/searchservice/create-indexer).

Дополнительные сведения об определении расписаний индексаторов для Когнитивного поиска Azure см. [здесь](search-howto-schedule-indexers.md).

## <a name="see-also"></a>См. также раздел

Дополнительные сведения об индексаторах Cosmos DB:
* [Индексатор Azure Cosmos DB](search-howto-index-cosmosdb.md)
