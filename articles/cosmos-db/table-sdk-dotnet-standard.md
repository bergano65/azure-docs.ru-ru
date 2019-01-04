---
title: API таблиц Azure Cosmos DB, пакет SDK для .NET Standard и ресурсы
description: Сведения об API таблиц Azure Cosmos DB и пакете SDK для .NET Standard, включая даты выпуска, даты вывода из эксплуатации и изменения, внесенные в каждую версию.
services: cosmos-db
author: donghexu
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.author: dox
ms.openlocfilehash: dd095328f8ba4cc22eea21133e7c8600a2cd64e1
ms.sourcegitcommit: c2e61b62f218830dd9076d9abc1bbcb42180b3a8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/15/2018
ms.locfileid: "53438665"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>API таблиц Azure Cosmos DB и пакет SDK для .NET Standard: заметки о скачивании и выпуске
> [!div class="op_single_selector"]

> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**Скачивание пакета SDK**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Текущая поддерживаемая платформа**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-preview
* Добавлена поддержка основных операций CRUD, пакетных операций и операций запросов для конечных точек таблиц службы хранилища Azure. [!NOTE] Некоторые функциональные возможности в предыдущих версиях пакета SDK для таблиц службы Azure еще не поддерживаются, такие как шифрование на стороне клиента.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Пакет SDK .NET Standard для таблиц Azure Cosmos DB — это кроссплатформенная библиотека .NET, которая предоставляет эффективный доступ к модели данных таблиц в Cosmos DB. Этот начальный выпуск поддерживает все запросы и CRUD-операции для таблиц и сущностей и содержит API-интерфейсы, подобные тем, что используются в [пакете SDK .NET Framework для таблиц Cosmos DB](table-sdk-dotnet.md). [!NOTE] Конечные точки таблиц службы хранилища Azure не поддерживаются в версии 0.9.1-preview.

## <a name="release-and-retirement-dates"></a>Даты выпуска и вывода из эксплуатации
Корпорация Майкрософт отправляет уведомление минимум за **12 месяцев** до вывода пакета SDK из эксплуатации, чтобы обеспечить более плавный переход на новую или поддерживаемую версию.

| Version (версия) | Дата выпуска | Дата вывода |
| --- | --- | --- |
| [0.10.0-preview](#0.10.0-preview) |18 декабря 2018 г. |--- |
| [0.9.1-preview](#0.9.1-preview) |18 октября 2018 г. |--- |


## <a name="faq"></a>Часто задаваемые вопросы

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также
Дополнительные сведения об API таблицы для базы данных Azure Cosmos DB см. в [этой статье](table-introduction.md). 
