---
title: Привязывание Azure Cosmos DB к приложению Azure Spring Cloud | Документация Майкрософт
description: Узнайте как привязать Azure Cosmos DB к приложению Azure Spring Cloud
author: jpconnock
ms.service: spring-cloud
ms.topic: tutorial
ms.date: 10/06/2019
ms.author: jeconnoc
ms.openlocfilehash: 76a2b9f305f041a19b8d7ace8234a804825f6a0e
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73607134"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Руководство по Привязывание Azure Cosmos DB к приложению Azure Spring Cloud

С помощью Azure Spring Cloud можно автоматически привязать выбранные службы Azure к приложениям, а не вручную настраивать приложение Spring Boot. В этой статье показано, как привязать приложение к Azure Cosmos DB.

Предварительные требования:
* Развернутый экземпляр Azure Spring Cloud  Чтобы приступить к работе, ознакомьтесь с [этим руководством](spring-cloud-quickstart-launch-app-cli.md).
* Учетная запись Azure Cosmos DB с минимальным уровнем разрешений участника.

## <a name="bind-azure-cosmos-db"></a>Привязка Azure Cosmos DB

Azure Cosmos DB имеет пять различных типов API, поддерживающих привязку:

1. Создает базу данных Azure Cosmos DB. Дополнительные сведения о создании базы данных см. в [этой статье](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal). Запишите имя базы данных. Наша имеет имя `testdb`.

1. Добавьте одну из следующих зависимостей приложения Spring Cloud `pom.xml` в соответствии с типом API.
    
    #### <a name="api-type-core-sql"></a>Тип API: Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Тип API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Тип API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Тип API: Gremlin (граф)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Тип API: таблице Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Обновите текущую развернутую службу с помощью `az spring-cloud app update` или создайте новое развертывание для этого изменения с помощью `az spring-cloud app deployment create`.  Эти команды либо обновляют, либо создают приложение с новой зависимостью.

1. Перейдите к странице своей службы Azure Spring Cloud на портале Azure. Это то же приложение, которое вы обновили или развернули на предыдущем шаге. Найдите **панель мониторинга приложения** и выберите приложение для привязки к Cosmos DB. Затем выберите `Service binding` и нажмите кнопку `Create service binding`. Заполните форму, выбрав **тип привязки** `Azure Cosmos DB`, тип API, имя базы данных и учетную запись Azure Cosmos DB.

    > [!NOTE]
    > Если используется Cassandra, используйте пространство ключей для имени базы данных.

1. Перезапустите приложение, нажав кнопку **Перезапустить** на странице приложения.

1. Чтобы обеспечить правильную привязку службы, выберите имя привязки и проверьте сведения о ней. Поле `property` должно выглядеть следующим образом:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Дополнительная информация

В этом руководстве вы узнали, как привязать приложение Azure Spring Cloud к базе данных CosmosDB.  Чтобы узнать, как привязать приложение к Кэшу Azure для Redis, перейдите к следующему учебнику.

> [!div class="nextstepaction"]
> [Привязывание приложения Spring Cloud к Кэшу Azure для Redis](spring-cloud-tutorial-bind-redis.md).
