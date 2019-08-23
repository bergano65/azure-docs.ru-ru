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
ms.openlocfilehash: 22a27ba19670344f351205b6a4865bf85daf0bab
ms.sourcegitcommit: d3dced0ff3ba8e78d003060d9dafb56763184d69
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/22/2019
ms.locfileid: "69900256"
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

## <a name="release-notes"></a>Заметки о выпуске

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

| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
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
