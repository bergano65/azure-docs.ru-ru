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
ms.openlocfilehash: 01645e66907e01b2e8fcb771976b50e5bbc8abf4
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68229028"
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
> * [BulkExecutor — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor — Java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)|
|**Документация по API**|[Справочная документация по API .NET](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Примеры**|[Примеры кода для .NET](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/CodeSamples)|
|**Начало работы**|[Azure Cosmos DB. Приступая к работе с API DocumentDB](sql-api-get-started.md)|
|**Учебник по веб-приложениям**|[Руководство по ASP.NET MVC. Разработка веб-приложений в Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Standard 2.0](/dotnet/standard/net-standard)|

## <a name="release-notes"></a>Заметки о выпуске

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
| [3.0.0](#3.0.0) |15 июля 2019 г. |--- |

## <a name="faq"></a>Часто задаваемые вопросы
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/). 

