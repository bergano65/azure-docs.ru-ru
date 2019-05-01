---
title: API таблиц Azure Cosmos DB, пакет SDK для .NET Standard и ресурсы
description: Сведения об API таблиц Azure Cosmos DB и пакете SDK для .NET Standard, включая даты выпуска, даты вывода из эксплуатации и изменения, внесенные в каждую версию.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: 4b193dd41c137907e56e2bf38b18ebcf2f2b481a
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64692254"
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
|**Пример**|[Пример .NET API таблицы Cosmos DB](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Краткое руководство**|[Краткое руководство](create-table-dotnet.md)|
|**Руководство**|[Руководство](tutorial-develop-table-dotnet.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Исправления ошибок

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Выпуск общедоступной версии

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-Preview
* Как могут настраиваться CloudTableClient были внесены изменения. Теперь принимает объект TableClientConfiguration во время построения. TableClientConfiguration предоставляют разные свойства для настройки поведения клиента в зависимости от того, является ли целевая конечная точка API таблиц Cosmos DB или API таблиц хранилища Azure.
* Добавлена поддержка TableQuery результатов в отсортированном порядке по пользовательского столбца. Эта функция поддерживается только в конечных точках таблиц Cosmos DB.
* Добавлена поддержка для предоставления RequestCharges на различными типами результатов. Эта функция поддерживается только в конечных точках таблиц Cosmos DB.

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

| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [1.0.1](#1.0.1) |19 апреля 2019 г. |--- |
| [1.0.0](#1.0.0) |13 марта 2019 г. |--- |
| [0.11.0-Preview](#0.11.0-preview) |5 марта 2019 г. |--- |
| [0.10.1-preview](#0.10.1-preview) |22 января 2019 г. |--- |
| [0.10.0-preview](#0.10.0-preview) |18 декабря 2018 г. |--- |
| [0.9.1-preview](#0.9.1-preview) |18 октября 2018 г. |--- |


## <a name="faq"></a>Часто задаваемые вопросы

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения об API таблицы для базы данных Azure Cosmos DB см. в [этой статье](table-introduction.md). 
