---
title: Сведения о пружинных Azure Cosmos DBах для заметок о выпуске API SQL и ресурсов
description: Узнайте все о пружинных данных Azure Cosmos DB для API SQL, включая даты выпуска, даты выбытия и изменения, внесенные в каждую версию пакета SDK для Java для Azure Cosmos DB SQL.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f94ea37966c4942f72ae2d7b701e742891fa6cd2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854308"
---
# <a name="spring-data-azure-cosmos-db-for-core-sql-api-release-notes-and-resources"></a>Пружинные данные Azure Cosmos DB для API Core (SQL): заметки о выпуске и ресурсы
> [!div class="op_single_selector"]
> * [Пакет SDK версии 3 для .NET](sql-api-sdk-dotnet-standard.md)
> * [Пакет SDK для .NET версии 2](sql-api-sdk-dotnet.md)
> * [Пакет SDK для .NET Core версии 2](sql-api-sdk-dotnet-core.md)
> * [Пакет SDK для веб-канала изменений .NET версии 2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Пакет SDK для Java версии 4](sql-api-sdk-java-v4.md)
> * [Пакет SDK для Async Java версии 2](sql-api-sdk-async-java.md)
> * [Пакет SDK для Sync Java версии 2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Соединитель Spark](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Групповой исполнитель — .NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Исполнитель массовых операций — Java](sql-api-sdk-bulk-executor-java.md)

Пружинные данные Azure Cosmos DB для ядра (SQL) позволяют разработчикам использовать Azure Cosmos DB в пружинных приложениях. Пружинные данные Azure Cosmos DB предоставляют интерфейс пружинных данных для управления базами данных и коллекциями, работы с документами и выдача запросов. И синхронизация, и асинхронные (реактивные) API поддерживаются в одном и том же артефакте Maven. 

[Пружинная платформа](https://spring.io/projects/spring-framework) — это модель программирования и конфигурации, которая упрощает разработку приложений Java. Чтобы заключить в кавычки веб-сайт Организации, пружина упрощает «работу» приложений, используя внедрение зависимостей. Многие разработчики, например пружины, так как создание и тестирование приложений становятся более простыми. [Пружинная Загрузка](https://spring.io/projects/spring-boot) расширяет концепцию работы с учетом веб-приложений и разработки микрослужб. [Пружинные данные](https://spring.io/projects/spring-data) — это модель программирования для доступа к хранилищам данных, таким как Azure Cosmos DB, из контекста пружинного или пружинного приложения загрузки. 

Вы можете использовать пружинные данные Azure Cosmos DB в [облачных приложениях Azure для весны](https://azure.microsoft.com/services/spring-cloud/) .

> [!IMPORTANT]  
> Эти заметки о выпуске предназначены только для Azure Cosmos DB API SQL. 
>
> Следующие руководства поддерживают пружинные данные на других Azure Cosmos DB API:
> * [Пружинные данные для Apache Cassandra с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Пружинные данные MongoDB с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Пружинные данные Gremlin с Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Хотите быстро перейти к работе?
> 1. Установите [минимальную поддерживаемую среду выполнения Java (JDK 8)](/java/azure/jdk/?view=azure-java-stable), чтобы можно было использовать пакет SDK.
> 2. Создавайте пружинные данные Azure Cosmos DB приложении с помощью начального. [это просто](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)!
> 3. Обратитесь к руководству по основным [данным Azure Cosmos DB разработчикам](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) , которые проходят через базовые запросы Azure Cosmos DB.
>
> Вы можете быстро загрузить начальные стартовые приложения с [пружинным Initializr](https://start.spring.io/)!
>

## <a name="helpful-content"></a>Полезное содержимое

| Содержимое | Ссылка |
|---|---|
| **Скачивание пакета SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**Документация по API** | [Справочная документация по пружинным данным Azure Cosmos DB]() |
|**Участие в разработке пакета SDK** | [Репозиторий с пружинными данными Azure Cosmos DB на GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Начальная загрузка весны**| [Azure Cosmos DBная клиентская библиотека начальной загрузки для Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Пример приложения с пружинным TODO с Azure Cosmos DB**| [Сквозной интерфейс Java в службе приложений Linux (часть 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Руководством для разработчиков** | [Сведения о пружинных Azure Cosmos DBах для разработчиков](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Инструкции по использованию начального** | [Использование начального приложения Spring Boot с API SQL Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [Репозиторий GitHub для начальной загрузки Azure "Весна" Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Пример со службами приложений** | [Использование Spring и Cosmos DB со Службой приложений в Linux](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Пример приложения TODO](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>История выпусков

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Новые функции
* Обновление версии пружинной загрузки до 2.3.0 
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Новые функции
* Обновление Azure Cosmos DB версии на v 3.7.3
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Содержит исправления утечки памяти и NETTY обновления версий из пакета SDK для Cosmos v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Новые функции
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлен флаг Алловтелеметри для учета из Космосдбконфиг
* Фиксированное свойство TTL в контейнере

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Новые функции
* Добавлен новый API findAll по ключу раздела.
* Версия Azure-Cosmos обновлена до 3.7.0
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлено collectionName-> containerName
* Исправлена Ентитикласс & domainClass-> domainType
* Исправлена "коллекция возвращаемых сущностей, сохраненная репозиторием вместо входной сущности"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Новые функции
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправление для "коллекция возвращаемых сущностей, сохраненная из репозитория вместо входной сущности"

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Новые функции
* Версия Azure-Cosmos обновлена до версии v 3.6.0
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Новые функции
* Версия пакета SDK Cosmos DB обновлена до 3.5.0
* Добавлено поле аннотации для включения или отключения автоматического создания коллекции
* Улучшенная обработка исключений, доступная Космосклиентексцептион через Космосдбакцессексцептион
* Предоставлены requestCharge и activityId через Респонседиагностикс
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Устранение ошибки обновления 3.5.0 пакетов SDK "при Cosmos DB заголовок ответа HTTP превышает 8192 байт", "Консистенциполици. Дефаултконсистенцилевел () завершается сбоем с ограниченным Устареваниями и последовательным префиксом"
* Исправлено поведение API Финдбид, возвращено пустое не найдено, вместо создания исключения
* Исправлена ошибка, при которой сортировка не была применена на следующей странице при использовании Космоспажерекуест

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Новые функции
* Добавлено поле аннотации для включения или отключения автоматического создания коллекции
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлено поведение API Финдбид, возвращено пустое не найдено, вместо создания исключения

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Новые функции
* Полная поддержка реактивного репозитория Cosmos
* Cosmos DB поддержки метрик запросов и строк диагностики
* Cosmos DB обновление версии пакета SDK для 3.3.1
* Обновление версии каркасной платформы до 5.2.0. RELEASE
* Обновление версии Commons Attribution данных до 2.2.0. RELEASE
* Добавлены Финдбидандпартитионкэй, Делетебидандпартитионкэй API
* Удалена зависимость от Azure-даументдб
* Изменена фирменная символика DocumentDb на Cosmos
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлена ошибка "при сортировке возникает исключение, когда pageSize меньше общего количества элементов в репозитории"

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Новые функции
* Нерекомендуемые API-интерфейсы базы данных документов
* Добавлены API-интерфейсы Финдбидандпартитионкэй, Делетебидандпартитионкэй.
* Добавлена Оптимистическая блокировка на основе _etag
* Включенное выражение спел для имени коллекции документов
* Улучшения Обжектмаппер.
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Новые функции
* Добавлена зависимость SDK v3 Cosmos
* Добавлен реактивный репозиторий Cosmos
* Обновленная реализация Документдбтемплате для использования пакета SDK для Cosmos v3.
* Другие изменения конфигурации для поддержки реактивного репозитория Cosmos.
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Новые функции
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Удалить зависимость applicationInsights для
    * Потенциальный риск замусоривать зависимостей
    * Несовместимость с Java 11
    * Предотвращение потенциального влияния на производительность ЦП и/или памяти.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Новые функции
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Независимый порт удаляет зависимость applicationInsights для
    * Потенциальный риск замусоривать зависимостей
    * Несовместимость с Java 11
    * Предотвращение потенциального влияния на производительность ЦП и/или памяти.

### <a name="211-2019-03-07"></a>"-е" (2019-03-07)
#### <a name="new-features"></a>Новые функции
* Обновить главную версию до версии
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Новые функции
* Игнорировать все исключения из телеметрии
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Новые функции
* Обновление версии до 2.1.0 для устранения проблемы
#### <a name="key-bug-fixes"></a>Исправления основных ошибок

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Новые функции
* Добавлено ключевое слово, startsWith
* Файл сведений об обновлении
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправление "не удается вызвать Self href непосредственно для сущности"
* Исправление "findAll завершится ошибкой, если коллекция не создана"

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (Предварительная версия) (2018-08-23)
#### <a name="new-features"></a>Новые функции
* Переименование пакета из documentdb в cosmosdb,
* Добавить новую функцию ключевого слова метода запроса, 16 ключевых слов из поддерживаемых API SQL.
* Добавление новой функции запроса с разбиением на страницы и сортировкой.
* Упростите настройку "Весна-Data-cosmosdb".
* Добавьте API Делетеколлектион и deleteAll.

#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправление ошибок и устранение дефектов.

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

Дополнительные сведения о пружинной платформе см. на [домашней странице проекта](https://spring.io/projects/spring-framework).

Дополнительные сведения о пружинной загрузке см. на [домашней странице проекта](https://spring.io/projects/spring-boot).

Дополнительные сведения о пружинных данных см. на [домашней странице проекта](https://spring.io/projects/spring-data).