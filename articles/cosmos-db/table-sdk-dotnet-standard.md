---
title: API таблиц Azure Cosmos DB, пакет SDK для .NET Standard и ресурсы
description: Сведения об API таблиц Azure Cosmos DB и пакете SDK для .NET Standard, включая даты выпуска, даты вывода из эксплуатации и изменения, внесенные в каждую версию.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771586"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API таблиц Azure Cosmos DB и .NET Standard. Скачивание и заметки о выпуске
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Загрузка SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Пример**|[Космос DB Таблица API .NET Пример](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Краткое руководство**|[Краткое руководство](create-table-dotnet.md)|
|**Учебник**|[Учебник](tutorial-develop-table-dotnet.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Сообщить о проблеме**|[Сообщить о проблеме](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Примечания к выпуску серии 2.0.0
Серия 2.0.0 получает зависимость от [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)с улучшением производительности и консолидацией пространства имен в конечную точку Cosmos DB.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* первоначальный предварительный просмотр 2.0.0 Таблица SDK, который принимает зависимость от [Microsoft.Azure.Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/), с улучшением производительности и консолидации пространства имен в конечную точку Космос DB. Общественный API остается прежним.

## <a name="release-notes-for-100-series"></a>Примечания к выпуску серии 1.0.0
Серия 1.0.0 получает зависимость от [Microsoft.Azure.DocumentDB.Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Ввести новую конфигурацию под TableClientConfiguration для использования Rest Executor для связи с Cosmos DB Table API

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-предварительный просмотр
* Исправления ошибок

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Исправления ошибок
* Предоставьте опцию HttpClientTimeout для RestExecutorConfiguration.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-предварительный просмотр
* Исправления ошибок
* Предоставьте опцию HttpClientTimeout для RestExecutorConfiguration.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Исправления ошибок

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Выпуск общедоступной версии

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-предварительный просмотр
* Были внесены изменения в способ настройки CloudTableClient. Теперь он принимает объект TableClientConfiguration во время строительства. TableClientConfiguration предоставляет различные свойства для настройки поведения клиента в зависимости от того, является ли конечная точка Cosmos DB Table API или API таблицы хранения Azure.
* Добавлена поддержка таблицы, чтобы вернуть результаты в отсортированном порядке на пользовательском столбце. Эта функция поддерживается только на конечных точках таблицы Cosmos DB Table.
* Добавлена поддержка для выявления запросов на различные типы результатов. Эта функция поддерживается только на конечных точках таблицы Cosmos DB Table.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Добавлена поддержка для маркера SAS, операций TablePermissions, ServiceProperties и ServiceStats относительно конечных точек таблицы службы хранилища Azure. 
   > [!NOTE]
   > Некоторые функциональные возможности в предыдущих версиях пакета SDK для таблиц службы хранилища Azure еще не поддерживаются, такие как шифрование на стороне клиента.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Добавлена поддержка основных операций CRUD, пакетных операций и операций запросов для конечных точек таблиц службы хранилища Azure. 
   > [!NOTE]
   > Некоторые функциональные возможности в предыдущих версиях пакета SDK для таблиц службы хранилища Azure еще не поддерживаются, такие как шифрование на стороне клиента.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Пакет SDK .NET Standard для таблиц Azure Cosmos DB — это кроссплатформенная библиотека .NET, которая предоставляет эффективный доступ к модели данных таблиц в Cosmos DB. Этот начальный выпуск поддерживает все запросы и CRUD-операции для таблиц и сущностей и содержит API-интерфейсы, подобные тем, что используются в [пакете SDK .NET Framework для таблиц Cosmos DB](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Конечные точки таблиц Службы хранилища Azure не поддерживаются в версии 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Эта кросс-платформа .NET Стандартная библиотека [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) заменит рамочную библиотеку .NET [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Серия 2.0.0
| Версия | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>Серия 1.0.0
| Версия | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 сентября 2019 г. |--- |
| [1.0.5-предварительный просмотр](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-предварительный просмотр](#1.0.4-preview) |26 июля 2019 г. |--- |
| 1.0.2-предварительный просмотр |2 мая 2019 г. |--- |
| [1.0.1](#1.0.1) |19 апреля 2019 года |--- |
| [1.0.0](#1.0.0) |13 марта 2019 г. |--- |
| [0.11.0-предварительный просмотр](#0.11.0-preview) |5 марта 2019 г. |--- |
| [0.10.1-preview](#0.10.1-preview) |22 января 2019 г. |--- |
| [0.10.0-preview](#0.10.0-preview) |18 декабря 2018 г. |--- |
| [0.9.1-предварительный просмотр](#0.9.1-preview) |18 октября 2018 г. |--- |


## <a name="faq"></a>часто задаваемые вопросы

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения об API таблицы для базы данных Azure Cosmos DB см. в [этой статье](table-introduction.md).
