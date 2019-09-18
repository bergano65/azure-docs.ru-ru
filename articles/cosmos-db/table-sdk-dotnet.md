---
title: Пакет SDK для .NET и ресурсы API таблиц для базы данных Azure Cosmos DB
description: Сведения об API таблиц Azure Cosmos DB, в том числе даты выпуска и даты вывода из эксплуатации, а также изменения, внесенные в каждую версию.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 5e98c40384207c77b4ea7e9557a7d1ebebd95e47
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058590"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>API таблиц .NET для базы данных Azure Cosmos DB: скачивание и заметки о выпуске

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://aka.ms/acdbtablenuget)|
|**Краткое руководство**|[Краткое руководство. Создание приложения API таблиц с помощью .NET и Azure Cosmos DB](create-table-dotnet.md)|
|**Руководство**|[Разработка с помощью API таблиц Azure Cosmos DB с использованием пакета SDK для .NET](tutorial-develop-table-dotnet.md)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Framework 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> Библиотека [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) пакета SDK для .NET Framework находится в режиме обслуживания. Ее поддержка скоро будет прекращена. Выполните обновление до новой библиотеки .NET Standard [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), чтобы и дальше получать последние функции, которые поддерживает API таблиц.

> Если вы создали учетную запись API таблиц на этапе работы с предварительной версией, для работы с общедоступными пакетами SDK для API таблиц создайте [новую учетную запись](create-table-dotnet.md#create-a-database-account).
>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Исправления ошибок

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Исправления ошибок

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Добавлена поддержка для операций записи в нескольких регионах
* Исправлены основные зависимости пакетов NuGet в Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial.

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Исправлены зависимости пакетов NuGet от Microsoft.Azure.Storage.Common и Microsoft.Azure.DocumentDB.
* Исправление ошибок в сериализации таблиц при настройке JsonConvert.DefaultSettings.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* Добавлена проверка тегов сущности неправильного формата в режиме прямого подключения.
* Исправлена ошибка, связанная с запросом LINQ, в режиме шлюза.
* Синхронные API теперь запускаются в пуле потоков с помощью SynchronizationContext.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Добавлены TableRequestOptions TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism и TableQueryContinuationTokenLimitInKb
* Исправления ошибок

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Выпуск общедоступной версии

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0 (предварительная версия)

* Первоначальный выпуск предварительной версии

## <a name="release-and-retirement-dates"></a>Даты выпуска и вывода из эксплуатации

Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

`Microsoft.Azure.CosmosDB.Table` Библиотека в настоящее время доступна только для .NET Framework и находится в режиме обслуживания и скоро будет считаться устаревшей. Новые функции и возможности и оптимизации добавляются только в библиотеку .NET Standard [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), поэтому рекомендуется выполнить обновление до [Microsoft. Azure. Cosmos. Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

Пакет предварительной версии [WindowsAzure. Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) является устаревшим. Поддержка пакета SDK WindowsAzure.Storage PremiumTable будет прекращена 15 ноября 2018 г. С этого времени запросы к устаревшему пакету SDK будут запрещены. 

Любые запросы к базе данных Azure Cosmos DB с помощью выведенного из эксплуатации пакета SDK отклоняются службой.
<br/>

| Version | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [2.1.2](#2.1.2) |16 сентября 2019 г.| |
| [2.1.0](#2.1.0) |22 января 2019 г.|01 апреля, 2020 |
| [2.0.0](#2.0.0) |26 сентября 2018 г.|01 марта 2020 г. |
| [1.1.3](#1.1.3) |17 июля 2018 г.|01 декабря 2019 г. |
| [1.1.1](#1.1.1) |26 марта 2018 г.|01 декабря 2019 г. |
| [1.1.0](#1.1.0) |21 февраля 2018 г.|01 декабря 2019 г. |
| [1.0.0](#1.0.0) |15 ноября 2017 г.|15 ноября 2019 г. |
| 0.9.0 (предварительная версия) |11 ноября 2017 г. |11 ноября 2019 г. |

## <a name="troubleshooting"></a>Устранение неполадок

Если возникает ошибка 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

при попытке использовать NuGet-пакет Microsoft.Azure.CosmosDB.Table, ее можно устранить двумя способами.

* Установите пакет Microsoft.Azure.CosmosDB.Table и его зависимости с помощью консоли диспетчера пакетов. Для этого введите в консоли диспетчера пакетов такую команду: 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Используя предпочитаемый инструмент управления пакетами NuGet, сначала установите пакет Microsoft.Azure.Storage.Common, а потом — Microsoft.Azure.CosmosDB.Table.

## <a name="faq"></a>Часто задаваемые вопросы

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также

Дополнительные сведения об API таблицы для базы данных Azure Cosmos DB см. в [этой статье](table-introduction.md). 
