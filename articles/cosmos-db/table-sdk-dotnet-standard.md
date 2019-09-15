---
title: API таблиц Azure Cosmos DB, пакет SDK для .NET Standard и ресурсы
description: Сведения об API таблиц Azure Cosmos DB и пакете SDK для .NET Standard, включая даты выпуска, даты вывода из эксплуатации и изменения, внесенные в каждую версию.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: c1422b6411fc60383cffa6c3082108bb940c4343
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/14/2019
ms.locfileid: "70996215"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API таблиц Azure Cosmos DB и пакет SDK для .NET Standard: заметки о скачивании и выпуске
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Пример**|[Пример Cosmos DB API таблиц .NET](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Краткое руководство**|[Краткое руководство](create-table-dotnet.md)|
|**Руководство**|[Руководство](tutorial-develop-table-dotnet.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Сообщить о проблемах**|[Сообщить о проблемах](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Заметки о выпуске для ряда 2.0.0
2.0.0 Series использует зависимость от [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)с улучшенной производительностью и консолидацией пространства имен для Cosmos DB конечной точки.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-preview
* Первоначальный предварительный просмотр пакета SDK для таблиц 2.0.0, который принимает зависимость от [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)с повышением производительности и консолидации пространства имен для Cosmos DB конечной точки. Открытый API остается прежним.

## <a name="release-notes-for-100-series"></a>Заметки о выпуске для серии 1.0.0
Серия 1.0.0 использует зависимость от [Microsoft. Azure. DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/).

### <a name="a-name105105"></a><a name="1.0.5"/>1.0.5
* Познакомьтесь с новой конфигурацией в разделе Таблеклиентконфигуратион, чтобы использовать исполнитель RESTful для взаимодействия с Cosmos DB API таблиц

### <a name="a-name105-preview105-preview"></a><a name="1.0.5-preview"/>1.0.5 — Предварительная версия
* Исправления ошибок

### <a name="a-name104104"></a><a name="1.0.4"/>1.0.4
* Исправления ошибок
* Укажите параметр Хттпклиенттимеаут для Рестексекуторконфигуратион.

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4 — Предварительная версия
* Исправления ошибок
* Укажите параметр Хттпклиенттимеаут для Рестексекуторконфигуратион.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Исправления ошибок

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Выпуск общедоступной версии

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0 — Предварительная версия
* В то, как можно настроить CloudTableClient, были внесены изменения. Теперь он принимает объект Таблеклиентконфигуратион во время создания. Таблеклиентконфигуратион предоставляет различные свойства для настройки поведения клиента в зависимости от того, является ли Целевая конечная точка Cosmos DB API таблиц или API таблиц хранилища Azure.
* Добавлена поддержка TableQuery, которая возвращает результаты в отсортированном порядке по пользовательскому столбцу. Эта функция поддерживается только в конечных точках таблицы Cosmos DB.
* Добавлена поддержка предоставления Рекуестчаржес для различных типов результатов. Эта функция поддерживается только в конечных точках таблицы Cosmos DB.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-preview
* Добавлена поддержка для маркера SAS, операций TablePermissions, ServiceProperties и ServiceStats относительно конечных точек таблицы службы хранилища Azure. 
   > [!NOTE]
   > Некоторые функциональные возможности в предыдущих версиях пакета SDK для таблиц службы хранилища Azure еще не поддерживаются, такие как шифрование на стороне клиента.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Добавлена поддержка основных операций CRUD, пакетных операций и операций запросов для конечных точек таблиц службы хранилища Azure. 
   > [!NOTE]
   > Некоторые функциональные возможности в предыдущих версиях пакета SDK для таблиц службы хранилища Azure еще не поддерживаются, такие как шифрование на стороне клиента.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Пакет SDK .NET Standard для таблиц Azure Cosmos DB — это кроссплатформенная библиотека .NET, которая предоставляет эффективный доступ к модели данных таблиц в Cosmos DB. Этот начальный выпуск поддерживает все запросы и CRUD-операции для таблиц и сущностей и содержит API-интерфейсы, подобные тем, что используются в [пакете SDK .NET Framework для таблиц Cosmos DB](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Конечные точки таблиц Службы хранилища Azure не поддерживаются в версии 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

Эта кросс-платформенная библиотека .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) заменит библиотеку .NET Framework [Microsoft. Azure. CosmosDB. Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).

### <a name="200-series"></a>Серия 2.0.0
| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [2.0.0-preview](#2.0.0-preview) |Аугуест 22, 2019 |--- |

### <a name="100-series"></a>Серия 1.0.0
| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.0.5](#1.0.5) |13 сентября 2019 г. |--- |
| [1.0.5 — Предварительная версия](#1.0.5-preview) |Аугуест 20, 2019 |--- |
| [1.0.4](#1.0.4) |Аугуест 12, 2019 |--- |
| [1.0.4 — Предварительная версия](#1.0.4-preview) |26 июля 2019 г. |--- |
| 1.0.2 — Предварительная версия |2 мая 2019 г. |--- |
| [1.0.1](#1.0.1) |19 апреля 2019 г. |--- |
| [1.0.0](#1.0.0) |13 марта 2019 г. |--- |
| [0.11.0 — Предварительная версия](#0.11.0-preview) |5 марта 2019 г. |--- |
| [0.10.1-preview](#0.10.1-preview) |22 января 2019 г. |--- |
| [0.10.0-preview](#0.10.0-preview) |18 декабря 2018 г. |--- |
| [0.9.1-preview](#0.9.1-preview) |18 октября 2018 г. |--- |


## <a name="faq"></a>Часто задаваемые вопросы

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения об API таблицы для базы данных Azure Cosmos DB см. в [этой статье](table-introduction.md).
