---
title: Заметки о выпуске и ресурсы для SQL API Azure Cosmos DB v3
description: Узнайте все о пружинных данных Azure Cosmos DB v3 для API SQL, включая даты выпуска, даты выбытия и изменения, внесенные в каждую версию пакета SDK для Java для Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 04a50d37606754ff4540d1056e378d46388e2592
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2020
ms.locfileid: "88590724"
---
# <a name="spring-data-azure-cosmos-db-v3-for-core-sql-api-release-notes-and-resources"></a>Пружинные данные Azure Cosmos DB v3 для API Core (SQL): заметки о выпуске и ресурсы
> [!div class="op_single_selector"]
> * [Пакет SDK версии 3 для .NET](sql-api-sdk-dotnet-standard.md)
> * [Пакет SDK для .NET версии 2](sql-api-sdk-dotnet.md)
> * [Пакет SDK для .NET Core версии 2](sql-api-sdk-dotnet-core.md)
> * [Пакет SDK для веб-канала изменений .NET версии 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Пакет SDK для Java версии 4](sql-api-sdk-java-v4.md)
> * [Пакет SDK для Async Java версии 2](sql-api-sdk-async-java.md)
> * [Пакет SDK для Sync Java версии 2](sql-api-sdk-java.md)
> * [Пружинные данные v2](sql-api-sdk-java-spring-v2.md)
> * [Пружинные данные v3](sql-api-sdk-java-spring-v3.md)
> * [Соединитель Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Групповой исполнитель — .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Исполнитель массовых операций — Java](sql-api-sdk-bulk-executor-java.md)

Пружинные данные Azure Cosmos DB v3 для Core (SQL) позволяют разработчикам использовать Azure Cosmos DB в пружинных приложениях. Пружинные данные Azure Cosmos DB предоставляют интерфейс пружинных данных для управления базами данных и коллекциями, работы с документами и выдача запросов. И синхронизация, и асинхронные (реактивные) API поддерживаются в одном и том же артефакте Maven. 

Пружинные данные Azure Cosmos DB зависят от платформы пружины данных. Группа пакетов SDK Azure Cosmos DB выпускает артефакты Maven для пружинных данных v 2.2 и v 2.3.

[Пружинная платформа](https://spring.io/projects/spring-framework) — это модель программирования и конфигурации, которая упрощает разработку приложений Java. Чтобы заключить в кавычки веб-сайт Организации, пружина упрощает «работу» приложений, используя внедрение зависимостей. Многие разработчики, например пружины, так как создание и тестирование приложений становятся более простыми. [Пружинная Загрузка](https://spring.io/projects/spring-boot) расширяет концепцию работы с учетом веб-приложений и разработки микрослужб. [Пружинные данные](https://spring.io/projects/spring-data) — это модель программирования и платформа для доступа к хранилищам данных, таким как Azure Cosmos DB из контекста пружинного или пружинного приложения загрузки. 

Вы можете использовать пружинные данные Azure Cosmos DB в [облачных приложениях Azure для весны](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Эти заметки о выпуске предназначены для 3 Azure Cosmos DB данных. Заметки о выпуске v2 можно найти [здесь](sql-api-sdk-java-spring-v2.md). 
>
> Пружинные данные Azure Cosmos DB поддерживают только API SQL.
>
> Следующие руководства поддерживают пружинные данные на других Azure Cosmos DB API:
> * [Пружинные данные для Apache Cassandra с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Пружинные данные MongoDB с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Пружинные данные Gremlin с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Начало работы

# <a name="explore"></a>[Обзор](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

### <a name="navigate-the-tabs-above-for-basic-spring-data-azure-cosmos-db-samples"></a>Перейдите на вкладки выше, чтобы получить основные Azure Cosmos DB образцы данных.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Настройка зависимостей

Доступны две пружинные данные Azure Cosmos DB 3 дополнительные артефакты Maven v3.

Артефакт, который зависит от "пружинных данных" v 2.2:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-2-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

Артефакт, зависящий от Data Framework v 2.3:
```xml
<dependency>
    <groupId>com.azure</groupId>
    <artifactId>azure-spring-data-2-3-cosmos</artifactId>
    <version>latest</version>
</dependency>
```

# <a name="connect"></a>[Подключить](#tab/connect)

### <a name="connect"></a>Подключение

Укажите учетную запись Azure Cosmos DB и сведения о контейнере. Пружинные данные Azure Cosmos DB автоматически создают клиент и подключаются к контейнеру.

[приложение. свойства](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-2-cosmos-java-getting-started/src/main/resources/application.properties):
```
cosmos.uri=${ACCOUNT_HOST}
cosmos.key=${ACCOUNT_KEY}
cosmos.secondaryKey=${SECONDARY_ACCOUNT_KEY}

dynamic.collection.name=spel-property-collection
# Populate query metrics
cosmos.queryMetricsEnabled=true
```

# <a name="doc-ops"></a>[Операции с документами](#tab/docs)

### <a name="document-operations"></a>Операции с документом

[Создайте](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Create)]

[Удалить](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Delete)]

# <a name="query"></a>[Запрос](#tab/queries)

### <a name="query"></a>Запрос

[Запрос](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java): [!code-java[](~/spring-data-azure-cosmos-db-sql-tutorial/azure-spring-data-2-3-cosmos-java-getting-started/src/main/java/com/azure/spring/data/cosmostutorial/SampleApplication.java?name=Query)]

---

## <a name="helpful-content"></a>Полезное содержимое

| Содержимое | Платформа данных пружины v 2.2 | Пружина Data Framework v 2.3 |
|---|---|
| **Скачивание пакета SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-2-cosmos) | [Maven](https://mvnrepository.com/artifact/com.azure/azure-spring-data-2-3-cosmos) |
|**Участие в разработке пакета SDK** | [Репозиторий с пружинными данными Azure Cosmos DB на GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-2-cosmos) | [Репозиторий с пружинными данными Azure Cosmos DB на GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-2-3-cosmos) | 
|**Руководство**| [Учебник Azure Cosmos DB данных пружины на GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-2-cosmos-java-getting-started) | [Учебник Azure Cosmos DB данных пружины на GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/tree/main/azure-spring-data-2-3-cosmos-java-getting-started) |

## <a name="release-history"></a>История выпусков

### <a name="300-beta1-2020-08-17"></a>3.0.0-Beta. 1 (2020-08-17)
#### <a name="new-features"></a>новые функции;
* Идентификатор группы изменен на `com.azure` .
* Идентификатор артефакта изменен на `azure-spring-data-2-3-cosmos` .
* Обновлена зависимость пакета SDK для Azure-Cosmos `4.3.2-beta.2` .
* Поддержка сущностей аудита — автоматическое управление полями createdBy, createdDate, Ластмодифиедби и lastModifiedDate с заметками.
* `@GeneratedValue` Поддержка заметок для автоматического создания идентификаторов полей ИДЕНТИФИКАТОРов `String` типа.
* Поддержка конфигурации с несколькими базами данных для одной учетной записи Cosmos с несколькими базами данных и несколькими учетными записями Cosmos с несколькими базами данных.
* Поддержка `@Version` аннотации для любого строкового поля.
* Обновленные API синхронизации возвращают типы в `Iterable` типы, а не `List` .
* Предоставляется `CosmosClientBuilder` из пакета SDK Cosmos как пружинный Bean в `@Configuration` класс.
* Обновлены `CosmosConfig` , чтобы содержать метрики запросов и реализацию процессора диагностики ответов.
* Поддержка возвращаемых `Optional` типов данных для одиночных результирующих запросов.
#### <a name="renames"></a>Переименовывает
* `CosmosDbFactory` в `CosmosFactory` .
* `CosmosDBConfig` в `CosmosConfig` .
* `CosmosDBAccessException` в `CosmosAccessException` .
* `Document` Аннотация к `Container` заметке.
* `DocumentIndexingPolicy` Аннотация к `CosmosIndexingPolicy` заметке.
* `DocumentQuery` в `CosmosQuery` .
* Флаг приложения. Properties `populateQueryMetrics` `queryMetricsEnabled` .
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Планирование задачи ведения журнала диагностики для `Parallel` потоков, чтобы избежать блокировки потоков ввода-вывода NETTY.
* Исправлена Оптимистическая блокировка при операции удаления.
* Исправлена проблема с экранированием запросов в `IN` предложении.
* Исправлена проблема с разрешением `long` типа данных для `@Id` .
* Исправлена проблема, допускающая `boolean` , `long` , `int` , `double` в качестве типов данных для `@PartitionKey` аннотации.
* Исправлены `IgnoreCase`  &  `AllIgnoreCase` Ключевые слова для игнорирования запросов вариантов.
* При автоматическом создании контейнеров значение единицы запроса по умолчанию (4000) удалено.

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Дополнительные сведения о пружинной платформе см. на [домашней странице проекта](https://spring.io/projects/spring-framework).

Дополнительные сведения о пружинной загрузке см. на [домашней странице проекта](https://spring.io/projects/spring-boot).

Дополнительные сведения о пружинных данных см. на [домашней странице проекта](https://spring.io/projects/spring-data).