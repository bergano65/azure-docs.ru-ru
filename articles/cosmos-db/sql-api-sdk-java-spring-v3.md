---
title: Заметки о выпуске и ресурсы для SQL API Azure Cosmos DB v3
description: Сведения о пружинных данных Azure Cosmos DB v3 для API SQL, включая даты выпуска, даты выбытия и изменения, внесенные в каждую версию пакета SDK для Java для Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/18/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f0cb3d5f9184bacef42a0258add6dd2461a71dd7
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91326666"
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

Пружинные данные Azure Cosmos DB версии 3 для Core (SQL) позволяют разработчикам использовать Azure Cosmos DB в пружинных приложениях. Пружинные данные Azure Cosmos DB предоставляют интерфейс пружинных данных для управления базами данных и коллекциями, работы с документами и выдача запросов. И синхронизация, и асинхронные (реактивные) API поддерживаются в одном и том же артефакте Maven. 

Пружинные данные Azure Cosmos DB зависит от платформы пружины данных. Группа Azure Cosmos DB SDK выпускает артефакты Maven для пружинных данных версий 2,2 и 2,3.

[Пружинная платформа](https://spring.io/projects/spring-framework) — это модель программирования и конфигурации, которая упрощает разработку приложений Java. Пружина упрощает "работу" приложений с помощью внедрения зависимостей. Многие разработчики, например пружины, так как делают создание и тестирование приложений более простыми. [Пружинная Загрузка](https://spring.io/projects/spring-boot) расширяет эту обработку связи с продвижением к разработке веб-приложений и микрослужб. [Пружинные данные](https://spring.io/projects/spring-data) — это модель программирования и платформа для доступа к хранилищам данных, таким как Azure Cosmos DB, из контекста пружинного или пружинного приложения загрузки. 

Вы можете использовать пружинные данные Azure Cosmos DB в [облачных приложениях Azure для весны](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Эти заметки о выпуске предназначены для предварительных Azure Cosmos DB данных версии 3. [Заметки о выпуске для версии 2](sql-api-sdk-java-spring-v2.md)можно найти здесь. 
>
> Пружинные данные Azure Cosmos DB поддерживают только API SQL.
>
> Сведения о пружинных данных в других Azure Cosmos DB API см. в этих статьях:
> * [Пружинные данные для Apache Cassandra с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Пружинные данные MongoDB с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Пружинные данные Gremlin с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>

## <a name="start-here"></a>Начало работы

# <a name="explore"></a>[Просматриваем](#tab/explore)

<img src="media/sql-api-sdk-java-spring-v3/up-arrow.png" alt="explore the tabs above" width="80"/>

#### <a name="these-tabs-contain-basic-spring-data-azure-cosmos-db-samples"></a>Эти вкладки содержат основные Azure Cosmos DBные образцы данных.

# <a name="pomxml"></a>[pom.xml](#tab/pom)

### <a name="configure-dependencies"></a>Настройка зависимостей

  ```xml
  <dependency>
      <groupId>com.azure</groupId>
      <artifactId>azure-spring-data-cosmos</artifactId>
      <version>latest</version>
  </dependency>
  ```

# <a name="connect"></a>[Подключить](#tab/connect)

### <a name="connect"></a>Подключение

Укажите учетную запись Azure Cosmos DB и сведения о контейнере. Пружинные данные Azure Cosmos DB автоматически создают клиент и подключаются к контейнеру.

[приложение. свойства](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started/blob/main/azure-spring-data-cosmos-java-getting-started/src/main/resources/application.properties):
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

---

## <a name="resources"></a>Ресурсы

* **Участие в пакете SDK**: [пружинные данные Azure Cosmos DB репозиторий на GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-spring-data-cosmos)

* **Учебник**. [Azure Cosmos DB "пружинные данные" в GitHub](https://github.com/Azure-Samples/azure-spring-data-cosmos-java-sql-api-getting-started) 

[!INCLUDE[Release notes](~/azure-sdk-for-java-cosmos-db/sdk/cosmos/azure-spring-data-cosmos/CHANGELOG.md)]

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Дальнейшие действия

См. дополнительные сведения об [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Узнайте больше о [пружинной платформе](https://spring.io/projects/spring-framework).

Дополнительные сведения о [пружинной загрузке](https://spring.io/projects/spring-boot).

Дополнительные сведения о [пружинных данных](https://spring.io/projects/spring-data).