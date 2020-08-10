---
title: 'Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK Java версии 4 для API-интерфейса SQL'
description: Сведения о пакете SDK версии 4 и API-интерфейсе SQL для Azure Cosmos DB, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK SQL Async Java для Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 0d1845c06c1f0373ffd4be43c104889a7327c3ac
ms.sourcegitcommit: bfeae16fa5db56c1ec1fe75e0597d8194522b396
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/10/2020
ms.locfileid: "88035779"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK Java версии 4 для API-интерфейса Core (SQL)
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

Пакет SDK Java версии 4 для Azure Cosmos DB для Core (SQL) объединяет асинхронный и синхронный интерфейсы API в один артефакт Maven. Пакет SDK версии 4 обеспечивает более высокую производительность, новые функции API и поддержку асинхронных операций на основе проекта Reactor и [библиотеки Netty](https://netty.io/). При использовании пакета SDK Java версии 4 для Azure Cosmos DB пользователи могут получить более высокую производительность по сравнению с [пакетом SDK Async Java версии 2 для Azure Cosmos DB](sql-api-sdk-async-java.md) и [пакетом SDK Sync Java версии 2 для Azure Cosmos DB](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Эти заметки о выпуске применимы только к пакету SDK Java для Azure Cosmos DB версии 4. Если сейчас вы используете более раннюю версию, чем версия 4, руководство [Перевод приложения на использование пакета средств разработки Java для Azure Cosmos DB версии 4](migrate-java-v4-sdk.md) поможет вам обновить его до версии 4.
>
> Ниже приведены шаги, позволяющие быстро приступить к работе.
> 1. Установите [минимальную поддерживаемую среду выполнения Java (JDK 8)](/java/azure/jdk/?view=azure-java-stable), чтобы можно было использовать пакет SDK.
> 2. Ознакомьтесь с [кратким руководством по пакету SDK Java версии 4 для Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), чтобы получить доступ к артефакту Maven и изучить основные запросы к Azure Cosmos DB.
> 3. Ознакомьтесь с [советами по повышению производительности](performance-tips-java-sdk-v4-sql.md) пакета SDK Java версии 4 для Azure Cosmos DB и руководствами по [устранению неполадок](troubleshoot-java-sdk-v4-sql.md), чтобы оптимизировать пакет SDK для приложения.
>
> [Семинары и лабораторные занятия по Azure Cosmos DB](https://aka.ms/cosmosworkshop) — еще один отличный ресурс для освоения пакета SDK Java версии 4 для Azure Cosmos DB.
>

## <a name="helpful-content"></a>Полезное содержимое

| Содержимое | Ссылка |
|---|---|
|**Скачивание пакета SDK**| [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Документация по API** | [Справочная документация по API Java](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Участие в разработке пакета SDK** | [Центральный репозиторий пакета SDK Azure для Java в GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos/azure-cosmos) | 
|**Начало работы** | [Краткое руководство. Создание приложения Java для управления данными API SQL для Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) <br> [Репозиторий GitHub с кодом краткого руководства](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Простые примеры кода** | [Azure Cosmos DB. Примеры Java для API SQL](sql-api-java-sdk-samples.md) <br> [Репозиторий GitHub с примером кода](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Консольное приложение с каналом изменений**| [Веб-канал изменений — пример пакета SDK для Java v4](create-sql-api-java-changefeed.md) <br> [Репозиторий GitHub с примером кода](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Пример веб-приложения**| [Создание веб-приложения с помощью пакета SDK для Java v4](sql-api-java-application.md) <br> [Репозиторий GitHub с примером кода](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Советы по улучшению производительности**| [Советы по повышению производительности при использовании пакета SDK для Java версии 4](performance-tips-java-sdk-v4-sql.md)| 
| **Устранение неполадок** | [Устранение неполадок с пакетом SDK для Java версии 4](troubleshoot-java-sdk-v4-sql.md) |
| **Переход на пакет SDK версии 4 с более ранней версии** | [Переход на пакет SDK для Java версии 4](migrate-java-v4-sdk.md) |
| **Минимальная поддерживаемая среда выполнения**|[JDK 8](/java/azure/jdk/?view=azure-java-stable); | 
| **Семинары и лабораторные занятия по Azure Cosmos DB** |[Домашняя страница семинаров по Cosmos DB](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>История выпусков

### <a name="440-beta1-unreleased"></a>4.4.0-Beta. 1 (не выпущено)

### <a name="430-2020-07-29"></a>4.3.0 (2020-07-29)
#### <a name="new-features"></a>Новые функции
* Обновлена версия библиотеки реактора-Core до `3.3.8.RELEASE` . 
* Обновлена версия библиотеки реактора-NETTY до `0.9.10.RELEASE` . 
* Версия библиотеки NETTY обновлена до версии `4.1.51.Final` . 
* Добавлены новые API перегрузки для `upsertItem` с `partitionKey` . 
* Добавлена поддержка открытой трассировки телеметрии. 
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлена проблема, при которой Сслексцептион возникает в случае отмены запросов в режиме ШЛЮЗа.
* Исправлена политика повторных попыток регулирования ресурсов при выполнении хранимых процедур.
* Исправлена проблема, когда пакет SDK зависает в режиме отладки на уровне журнала. 
* Исправлены периодические пиковые пики в задержке в режиме прямого подключения. 
* Исправлена проблема высокой длительности инициализации клиента. 
* Исправлена ошибка прокси-сервера HTTP при настройке клиента в режиме Direct и шлюза. 
* Исправленные потенциальные НПЕ в пользователях передают параметры null. 
* `requestLatency`В строку диагностики Добавлено тимеунит.
* Удалена повторяющаяся строка URI из строки диагностики. 
* Исправлена строка диагностики в правильном формате JSON для операций с точками.
* Исправлена проблема с `.single()` оператором, вызывающий срабатывание цепочки реактора в случае возникновения исключения "не найдено". 

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
#### <a name="new-features"></a>Новые функции
* Добавлен API с поддержкой регистрации скриптов в `CosmosStoredProcedureRequestOptions` .
* `DirectConnectionConfig`По умолчанию изменено `idleEndpointTimeout` на 1 ч, а по умолчанию — `connectTimeout` на 5S.
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлена проблема `GatewayConnectionConfig` `idleConnectionTimeout` переопределения `DirectConnectionConfig` `idleConnectionTimeout` .
* Исправлены `responseContinuationTokenLimitInKb` API-интерфейсы Get и Set в `CosmosQueryRequestOptions` .
* Исправлена проблема в веб-канале запросов и изменений при повторном создании коллекции с тем же именем.
* Исправлена проблема с первым созданием запроса Класскастексцептион.
* Исправлена проблема с последовательностью вызова запроса Нуллпоинтерексцептион.
* Исправлена проблема с обработкой отмененных запросов в режиме прямого подключения, вызывающих `onErrorDropped` вызов реактора. 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Новые функции
* Добавлена поддержка `GROUP BY` запроса.
* Увеличено значение по умолчанию Максконнектионсперендпоинт до 130 в Директконнектионконфиг.
* Увеличено значение по умолчанию Максрекуестсперконнектион до 30 в Директконнектионконфиг.
#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлены проблемы с сортировкой по запросу, возвращающие дублирующиеся результаты при возобновлении с помощью токена продолжения. 
* Исправлены проблемы с запросом значений, возвращающими значения NULL для вложенного объекта.
* Исправлено исключение пустого указателя в диспетчере запросов в Рнтбдклиентчаннелпул.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Новые функции
* `QueryRequestOptions` переименован в `CosmosQueryRequestOptions`.
* Обновлен `ChangeFeedProcessorBuilder` шаблон построителя.
* Добавлены `CosmosPermissionProperties` новые интерфейсы API имени контейнера и дочерних ресурсов.
* Добавлены дополнительные примеры & обогащенных документов `CosmosClientBuilder` . 
* Обновленные `CosmosDatabase`  &  `CosmosContainer` API с сраугхпутпропертиес для поддержки автомасштабирования и автопилотного развертывания. 
* `CosmosClientException` переименован в `CosmosException`. 
* Заменены `AccessCondition`  &  `AccessConditionType` `ifMatchETag()`  &  `ifNoneMatchETag()` API. 
* Объединяет все `Cosmos*AsyncResponse`  &  `CosmosResponse` типы в один `CosmosResponse` тип.
* `CosmosResponseDiagnostics` переименован в `CosmosDiagnostics`.  
* Упаковано `FeedResponseDiagnostics` в `CosmosDiagnostics` . 
* Удалена `jackson` зависимость от Azure-cosmos & полагается на Azure-Core. 
* Заменяется `CosmosKeyCredential` на `AzureKeyCredential` тип. 
* Добавлены `ProxyOptions` API в `GatewayConnectionConfig` . 
* Обновленный пакет SDK для использования `Instant` типа вместо `OffsetDateTime` . 
* Добавлен новый тип перечисления `OperationKind` . 
* `FeedOptions` переименован в `QueryRequestOptions`. 
* Добавлены `getETag()`  &  `getTimestamp()` API-интерфейсы в `Cosmos*Properties` типы. 
* Добавлены `userAgent` сведения в `CosmosException`  &  `CosmosDiagnostics` . 
* Обновлен символ новой строки в `Diagnostics` до системной новой строки. 
* Удалены `readAll*` API, используйте вместо этого запрос выбрать все API.
* Добавлен `ChangeFeedProcessor` API запаздывания оценки.   
* Добавлена поддержка подготовки пропускной способности автомасштабирования и автопилота для пакета SDK.  
* Заменяются `ConnectionPolicy` новыми конфигурациями соединения. Предоставляемые `DirectConnectionConfig`  &  `GatewayConnectionConfig` API-интерфейсы посредством `CosmosClientBuilder` для прямого & конфигурации подключения в режиме шлюза.
* Перемещено `JsonSerializable`  &  `Resource` в пакет реализации. 
* Добавлен `contentResponseOnWriteEnabled` API в космосклиентбуилдер, который отключает полное содержимое ответа при операциях записи.
* Предоставляемые `getETag()` API для типов ответов.
* Перемещено `CosmosAuthorizationTokenResolver` в реализацию. 
* Переименованный `preferredLocations`  &  `multipleWriteLocations` API в `preferredRegions`  &  `multipleWriteRegions` . 
* Обновлен `reactor-core` до версии 3.3.5. Release, `reactor-netty` до 0.9.7. Release & `netty` до 4.1.49. Final. 
* Добавлена поддержка `analyticalStoreTimeToLive` в пакете SDK.     
* `CosmosClientException`расширяет `AzureException` . 
* Удалены `maxItemCount`  &  `requestContinuationToken` API из `FeedOptions` вместо этого с помощью `byPage()` интерфейсов API из `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Появилось `CosmosPermissionProperties` в общедоступной области для `Permission` API-интерфейсов.
* Удаленный `SqlParameterList` тип & заменен на`List`
* Исправлено несколько утечек памяти в прямом TCP-клиенте. 
* Добавлена поддержка `DISTINCT` запросов. 
* Внешние зависимости удалены `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Перемещено `CosmosPagedFlux`  &  `CosmosPagedIterable` в `utils` пакет. 
* Обновлен NETTY до 4.1.45. Final & проект реактора до 3.3.3 версии.
* Обновлены общедоступные контракты RESTful для `Final` классов.
* Добавлена поддержка расширенной диагностики для операций с точками.
* Пакет обновлен до`com.azure.cosmos`
* Добавлен `models` пакет для контрактов Model/RESTful
* Добавлен `utils` пакет для `CosmosPagedFlux`  &  `CosmosPagedIterable` типов. 
* Обновлены общедоступные API для использования в `Duration` пакете SDK.
* Добавлены все контракты RESTful в `models` пакет.
* Аргумент `RetryOptions` переименован в `ThrottlingRetryOptions`.
* Добавлены `CosmosPagedFlux`  &  `CosmosPagedIterable` типы разбивки на страницы для API запросов. 
* Добавлена поддержка совместного использования Транспортклиент в нескольких экземплярах Космосклиентс с помощью нового API в`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Оптимизации запросов путем удаления двойной сериализации и десериализации. 
* Оптимизации заголовков ответов путем удаления ненужных копий. 
* Оптимизированная `ByteBuffer` сериализация и десериализация путем удаления промежуточных экземпляров строк.

#### <a name="key-bug-fixes"></a>Исправления основных ошибок
* Исправлено `toString()` исключение пустого указателя ConnectionPolicy.
* Исправлена проблема с анализом результатов запроса в случае упорядочения значений по запросам. 
* Устранены проблемы утечки сокетов с прямым клиентом TCP.
* Исправлена `orderByQuery` Ошибка токена продолжения.
* `ChangeFeedProcessor`Исправление ошибки для обработки разбиений секций &, когда раздел не найден.
* `ChangeFeedProcessor`Исправление ошибки при синхронизации обновлений аренды в разных потоках.
* Исправлено состояние гонки, вызывающее `ArrayIndexOutOfBound` исключение в сторереадер

## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).