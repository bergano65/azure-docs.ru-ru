---
title: Azure Cosmos DB — API-интерфейс, ресурсы и пакет SDK Async Java SQL
description: Сведения о пакете SDK и API-интерфейсе SQL Async Java, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK SQL Async Java для Azure Cosmos DB.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 156699b8d8c1a645961f4e919bdd843d995a3d18
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142657"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Пакет SDK Async Java в Azure Cosmos DB для API-интерфейса SQL: заметки о выпуске и материалы
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Групповой исполнитель — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Групповой исполнитель — Java](sql-api-sdk-bulk-executor-java.md)

Пакет SDK Async Java для API-интерфейса SQL отличается от пакета SDK Java для API-интерфейса SQL возможностью выполнять асинхронные операции с поддержкой [библиотеки Netty](https://netty.io/). Существующий [пакет SDK Java для API-интерфейса SQL](sql-api-sdk-java.md) не поддерживает асинхронные операции. 

| |  |
|---|---|
| **Скачивание пакета SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Документация по API** |[Справочная документация по API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Участие в разработке пакета SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Начало работы** | [Начало работы с пакетом SDK для Async Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Пример кода** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Советы по улучшению производительности**| [Файл сведений GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Минимальная поддерживаемая среда выполнения**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* Режим TCP сейчас включен по умолчанию
* Метрики запросов в перекрестной секции теперь возвращают все секции
* Global strong теперь работает правильно
* Отработка отказа для запросов с несколькими хозяевами не повторяется должным образом
* Выпуклости зависимостей для исправлений безопасности

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Бугфикс для поддержки хэша версии 2

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Бугфикс для утечки ресурсов на клиенте # Close () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Добавлена поддержка маркеров продолжения для запросов между секциями.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Исправлены некоторые ошибки в режиме Direct.
* Улучшено ведение журнала в режиме прямого подключения.
* Улучшенное управление подключениями.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Режим прямого подключения теперь доступен по умолчанию. Пример использования режима прямого подключения, см. в разделе [azure-cosmosdb-java](https://github.com/Azure/azure-cosmosdb-java) репозитория GitHub.
* Добавлена поддержка QueryMetrics.
* Изменены API, принимающие java.util.Collection, для которых важен порядок принятия java.util.List. Теперь ConnectionPolicy#getPreferredLocations(), JsonSerialization и PartitionKey(.) принимают список.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-beta-1
* Добавлена поддержка режима прямого подключения.
* Изменены API, принимающие java.util.Collection, для которых важен порядок принятия java.util.List.
  Теперь ConnectionPolicy#getPreferredLocations(), JsonSerialization и PartitionKey(.) принимают список.
* Исправлена ошибка сеанса для запроса документа в режиме шлюза.
* Обновлены зависимости (netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Исправлена обработка очень больших ответов на запросы.
* Исправлена обработка маркера при инициализации клиента ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)).
* Обновлены уязвимые зависимости jackson-databind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Исправлена ошибка утечки ресурсов.
* Добавлена поддержка MultiPolygon.
* Добавлена поддержка пользовательских заголовков в RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Исправлена ошибка упаковки.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Исправлена ошибка с NPE, которая возникает при записи пути файла retry.
* Исправлена ошибка NPE, которая возникает при управлении конечными точками.
* Обновлены уязвимые зависимости ([GitHub № 68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Добавлена поддержка ведения сетевого журнала Netty для устранения неполадок.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Добавлена поддержка для операций записи в нескольких регионах.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Добавлена поддержка прокси-сервера.
* Добавлена поддержка авторизации с помощью маркера ресурса.
* Исправлена ошибка при обработке ключей больших секций ([GitHub № 63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Улучшена документация.
* Изменена структура пакета SDK с разбивкой на более детализированные модули.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Исправлена ошибка для языковых стандартов, отличных от английского ([GitHub № 51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Добавлены вспомогательные методы в раздел "Конфликт ресурсов".

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Заменена зависимость org.json от jackson из соображений производительности и лицензирования ([GitHub № 29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Удален нерекомендуемый класс OfferV2.
* Добавлен метод доступа в класс Offer для материала о пропускной способности.
* Все методы в Document/Resource, возвращающие типы org.json, изменены для возвращения типа объекта jackson.
* Метод getObject(.) классов, расширяющих JsonSerializable, изменен для возвращения типа объекта ObjectNode jackson.
* Метод getCollection(.) изменен для возвращения коллекции ObjectNode.
* Удалены конструкторы подклассов JsonSerializable с аргументом org.json.JSONObject.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) теперь использует двойной пробел для отступа.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Добавлена поддержка политики уникальных индексов.
* Добавлена поддержка ограничения размера маркера продолжения ответа в параметрах веб-канала.
* Добавлена поддержка разбиения секций в запросах между секциями.
* Исправлена ошибка в сериализации метки времени JSON ([№ 32 на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Исправлена ошибка в сериализации перечисления JSON.
* Исправлена ошибка в управлении документами размером 2 МБ ([№ 33 на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Зависимость com.fasterxml.jackson.core:jackson-databind обновлена до версии 2.9.5 из-за ошибки ([jackson-databind: № 1599 на сайте GitHub](https://github.com/FasterXML/jackson-databind/issues/1599)).
* Зависимость от rxjava-extras обновлена до версии 0.8.0.17 из-за ошибки ([rxjava-extras: № 30 на сайте GitHub](https://github.com/davidmoten/rxjava-extras/issues/30)).
* Описание метаданных в POM-файле обновлено в соответствии с остальной документацией.
* Улучшение синтаксиса ([№ 41 на сайте GitHub ](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([№ 40 на сайте GitHub](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* В запросах добавлена поддержка обратной реакции.
* В запросах добавлена поддержка идентификатора диапазона для ключей секции.
* Внесено исправление, позволяющее использовать больший маркер продолжения в заголовке запроса (исправление ошибки № 24 на сайте GitHub).
* Зависимость Netty обновлена до версии 4.1.22.Final, чтобы обеспечить завершение работы виртуальной машины Java после выполнения основного потока.
* Внесено исправление, позволяющее избежать передачи маркера сеанса при считывании ресурсов главной ветви.
* Добавлены другие примеры.
* Добавлены другие сценарии тестирования производительности.
* Исправлены файлы заголовков Java для правильного создания документа Java.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Пакет SDK в общедоступной версии с комплексной поддержкой неблокирующих операций ввода-вывода и [библиотеки Netty](https://netty.io/) в режиме шлюза. 

## <a name="release-and-retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправит уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые компоненты, функции и средства оптимизации добавлены только в текущий пакет SDK. Поэтому рекомендуем как можно раньше обновлять систему до последней версии пакета SDK.

Любые запросы к Cosmos DB с помощью выведенного из эксплуатации пакета SDK будут отклонены службой.

> [!WARNING]
> Все версии **1. x** пакета SDK для Async Java для API SQL будут прекращены **30 августа 2020**.
> 
>
<br/>

| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [2.4.3](#2.4.3) |5 марта 2019 г.|--- |
| [2.4.2](#2.4.2) |1 марта 2019 г.|--- |
| [2.4.1](#2.4.1) |20 февраля 2019 г.|--- |
| [2.4.0](#2.4.0) |8 февраля 2019 г.|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 февраля 2019 г.|--- |
| [2.3.1](#2.3.1) |15 января 2019 г.|--- |
| [2.3.0](#2.3.0) |29 ноября 2018 г.|--- |
| [2.2.2](#2.2.2) |8 ноября 2018 г.|--- |
| [2.2.1](#2.2.1) |2 ноября 2018 г.|--- |
| [2.2.0](#2.2.0) |22 сентября 2018 г.|--- |
| [2.1.0](#2.1.0) |5 сентября 2018 г.|--- |
| [2.0.1](#2.0.1) |16 августа 2018 г.|--- |
| [2.0.0](#2.0.0) |20 июня 2018 г.|--- |
| [1.0.2](#1.0.2) |18 мая 2018 г.|30 августа 2020 г. |
| [1.0.1](#1.0.1) |20 апреля 2018 г.|30 августа 2020 г. |
| [1.0.0](#1.0.0) |27 февраля 2018 г|30 августа 2020 г. |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

