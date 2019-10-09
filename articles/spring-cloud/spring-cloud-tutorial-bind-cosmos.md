---
title: Привязка Azure Cosmos DB к облачному приложению Azure для весны | Документация Майкрософт
description: Узнайте, как привязать Azure Cosmos DB к облачному приложению Azure для весны
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 10/06/2019
ms.author: v-vasuke
ms.openlocfilehash: d051fd431180e9cb86f1df4642fb5e947103c007
ms.sourcegitcommit: d773b5743cb54b8cbcfa5c5e4d21d5b45a58b081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72038734"
---
# <a name="tutorial-bind-an-azure-cosmos-db-to-your-azure-spring-cloud-application"></a>Учебник. Привязка Azure Cosmos DB к облачному приложению Azure для весны

С помощью Azure Веснного облака можно автоматически привязать выбранные службы Azure к приложениям, а не вручную настраивать приложение с пружинной загрузкой. В этой статье показано, как привязать приложение к Azure Cosmos DB.

Предварительные требования:
* Развернутый облачный экземпляр Azure весны.  Чтобы приступить к работе, выполните инструкции нашего [руководства](spring-cloud-quickstart-launch-app-cli.md) .
* Учетная запись Azure Cosmos DB с минимальным уровнем разрешений участника.

## <a name="bind-azure-cosmos-db"></a>Привязка Azure Cosmos DB

Azure Cosmos DB имеет пять различных типов API, поддерживающих привязку:

1. Создает базу данных Azure Cosmos DB. Сведения о создании базы данных [см. в этой статье](https://docs.microsoft.com/azure/cosmos-db/create-cosmosdb-resources-portal) . Запишите имя базы данных. Наша именованная `testdb`.

1. Добавьте одну из следующих зависимостей в `pom.xml` облачного приложения весны в соответствии с типом API.
    
    #### <a name="api-type-core-sql"></a>Тип API: Core (SQL)

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-cosmosdb-spring-boot-starter</artifactId>
        <version>2.1.6</version>
    </dependency>
    ```
    
    #### <a name="api-type-mongodb"></a>Тип API: MongoDB

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-mongodb</artifactId>
    </dependency>
    ```

    #### <a name="api-type-cassandra"></a>Тип API: Cassandra

    ```xml
    <dependency>
        <groupId>org.springframework.boot</groupId>
        <artifactId>spring-boot-starter-data-cassandra</artifactId>
    </dependency>
    ```

    #### <a name="api-type-gremlin-graph"></a>Тип API: Gremlin (граф)

    ```xml
    <dependency>
        <groupId>com.microsoft.spring.data.gremlin</groupId>
        <artifactId>spring-data-gremlin</artifactId>
        <version>2.1.7</version>
    </dependency>
    ```

    #### <a name="api-type-azure-table"></a>Тип API: таблице Azure

    ```xml
    <dependency>
        <groupId>com.microsoft.azure</groupId>
        <artifactId>azure-storage-spring-boot-starter</artifactId>
        <version>2.0.5</version>
    </dependency>
    ```

1. Обновите текущее развертывание с помощью `az spring-cloud app update` или создайте новое развертывание для этого изменения с помощью `az spring-cloud app deployment create`.  Эти команды либо обновляют, либо создают приложение с новой зависимостью.

1. Перейдите на страницу облачной службы Azure "Весна" на портал Azure. Это то же приложение, которое вы обновили или развернули на предыдущем шаге. Найдите **панель мониторинга приложения** и выберите приложение для привязки к Cosmos DB. Затем выберите `Service binding` и нажмите кнопку `Create service binding`. Заполните форму, выберите **тип привязки** `Azure Cosmos DB`, тип API, имя базы данных и учетную запись Azure Cosmos DB.

    > [!NOTE]
    > Если используется Cassandra, используйте пространство ключей для имени базы данных.

1. Перезапустите приложение, нажав кнопку **перезапустить** на странице приложения.

1. Чтобы обеспечить правильную привязку службы, выберите имя привязки и проверьте сведения о ней. Поле `property` должно быть похоже на следующее:

    ```
    azure.cosmosdb.uri=https:/<some account>.documents.azure.com:443
    azure.cosmosdb.key=abc******
    azure.cosmosdb.database=testdb
    ```

## <a name="next-steps"></a>Следующие шаги

В этом руководстве вы узнали, как привязать облачное приложение Azure весны к CosmosDB.  Чтобы узнать, как привязать приложение к кэшу Redis для Azure, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Привязка облачного приложения Azure весны к кэшу Redis для Azure](spring-cloud-tutorial-bind-redis.md).
