---
title: Azure Cosmos DB — API .NET Standard SQL, & ресурсов пакета SDK
description: Узнайте все об API SQL и пакете SDK для .NET, включая даты выпуска, даты выбытия и изменения, внесенные в каждую версию пакета SDK для .NET Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 07/12/2019
ms.author: dech
ms.openlocfilehash: 0368e99135dd6e377dd2820b3e673c55182319b9
ms.sourcegitcommit: e3b0fb00b27e6d2696acf0b73c6ba05b74efcd85
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/30/2019
ms.locfileid: "68663808"
---
# <a name="azure-cosmos-db-net-standard-sdk-for-sql-api-download-and-release-notes"></a>Пакет SDK для Azure Cosmos DB .NET Standard для API SQL: заметки о скачивании и выпуске
> [!div class="op_single_selector"]
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
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

| |  |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Документация по API**|[Справочная документация по API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Примеры**|[Примеры кода для .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Начало работы**|[Azure Cosmos DB. Приступая к работе с API DocumentDB](sql-api-get-started.md)|
|**Учебник по веб-приложениям**|[Руководство по ASP.NET MVC. Разработка веб-приложений в Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Заметки о выпуске
### <a name="a-name310310"></a><a name="3.1.0"/>3.1.0
#### <a name="added"></a>Добавлено
- [#541](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/541) Уровень согласованности добавлен в параметры клиента и запроса
- [#544](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/544) Добавлена поддержка маркеров продолжения для LINQ.
- [#557](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/557) Добавлены параметры триггера для параметров запроса элемента.
- [#571](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/571) Добавлен сериализатор JSON.net по умолчанию с дополнительными параметрами.
- [#572](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/572) Добавлена проверка ключа секции в Креатеконтаинерифнотексистсасинк
- [#581](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/581) Добавление LINQ to QueryDefinition API
- [#592](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/592) Добавлен CreateIfNotExistsAsync в построитель контейнеров.
- [#597](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/597) Свойство токена продолжения Добавлено в Респонсемессаже
- [#604](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/604) Добавлен метод расширения LINQ Тостреамитератор

#### <a name="fixed"></a>Фиксированное
- [#548](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/548) Исправлено неправильное типизированное сообщение в Космосексцептион. ToString ();
- [#558](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/558) Исправление конфликта блокировки Локатионкаче Конкуррентдикт
- [#561](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/561) Жетитемлинккуерябле теперь работает с нулевым запросом
- [#567](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/567) Запрос правильно обрабатывает языки и региональные параметры на разных языках
- [#574](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/574) Исправлено пустое сообщение об ошибке, если при анализе запроса происходит сбой из непредвиденного исключения
- [#576](https://github.com/Azure/azure-cosmos-dotnet-v3/pull/576) Запрос правильно сериализует входные данные в поток

### <a name="a-name300300"></a><a name="3.0.0"/>3.0.0 
* Общая доступность пакета SDK для .NET [версии 3.0.0](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)
* Целевые объекты .NET Standard 2,0, поддерживающие .NET Framework 4.6.1 + и .NET Core 2.0 +
* Новая объектная модель с Космосклиент верхнего уровня и методами, разделенными между соответствующими классами базы данных и контейнера
* Новые высокопроизводительные API-интерфейсы потоковой передачи
* Встроенная поддержка API-интерфейсов обработчика веб-канала изменений
* API-интерфейсы Fluent построителя для обработчика веб-канала Космосклиент, контейнера и изменения
* API управления пропускной способностью идиоматическим
* Детализированные RequestOptions и Респонсетипес для запросов к базам данных, контейнерам, элементам, запросам и пропускной способности
* Возможность масштабирования несекционированных контейнеров 
* Расширяемый и настраиваемый сериализатор
* Расширяемый конвейер запросов с поддержкой пользовательских обработчиков


## <a name="release--retirement-dates"></a>Даты выпуска и выбытия
Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Новые функции, возможности и оптимизации добавляются только в текущую версию пакета SDK, поэтому рекомендуется как можно раньше обновлять пакет SDK до последней версии. 

Любые запросы к базе данных Azure Cosmos DB с помощью выведенного из эксплуатации пакета SDK отклоняются службой.

<br/>

| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [3.1.0](#3.1.0) |29 июля 2019 г. |--- |
| [3.0.0](#3.0.0) |15 июля 2019 г. |--- |


## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

