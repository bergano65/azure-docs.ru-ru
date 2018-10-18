---
title: API-интерфейс, пакет SDK и ресурсы .NET для библиотеки Bulk Executor (Azure Cosmos DB) | Документы Майкрософт
description: Сведения об API и пакетах SDK .NET для библиотеки Bulk Executor, в том числе даты выхода, даты снятия с учета и изменения, внесенные в каждую версию пакета SDK .NET для Bulk Executor в Azure Cosmos DB.
services: cosmos-db
author: tknandu
manager: kfile
editor: cgronlun
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 09/14/2018
ms.author: ramkris
ms.openlocfilehash: ffd8f438429cd8769ac0dbff7f489327166e0000
ms.sourcegitcommit: cf606b01726df2c9c1789d851de326c873f4209a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46294464"
---
# <a name="net-bulk-executor-library-download-information"></a>Библиотека Bulk Executor .NET — информация о скачивании 

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
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [Bulk Executor — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk Executor — Java](sql-api-sdk-bulk-executor-java.md)

<table>

<tr><td>**Описание**</td><td>Библиотека Bulk Executor позволяет клиентским приложениям выполнять массовые операции в учетных записях Azure Cosmos DB. Библиотека Bulk Executor предоставляет пространства имен BulkImport, BulkUpdate и BulkDelete. Модуль BulkImport может оптимизировать массовый прием документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции. Модуль BulkUpdate позволяет выполнять массовое обновление существующих данных в контейнерах Azure Cosmos DB в качестве исправлений. Модуль BulkDelete может оптимизировать массовое удаление документов, обеспечивая использование максимального объема пропускной способности, подготовленной для коллекции.</td></tr>

<tr><td>**Скачивание пакета SDK**</td><td>[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.BulkExecutor/)</td></tr>

<tr><td>**Библиотека BulkExecutor в GitHub**</td><td>[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-dotnet-getting-started)</td></tr>

<tr><td>**Документация по API**</td><td>[Справочная документация по API .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cosmosdb.bulkexecutor?view=azure-dotnet)</td></tr>

<tr><td>**Начало работы**</td><td>[Начало работы с пакетом SDK для .NET для библиотеки Bulk Executor](bulk-executor-dot-net.md)</td></tr>

<tr><td>**Текущая поддерживаемая платформа**</td><td><ul><li>[Microsoft.Azure.DocumentDB](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)(версия >= 2.0.0)</li><li>
[Newtonsoft.Json](https://www.nuget.org/packages/Newtonsoft.Json/)(версия >= 9.0.1)
</li></ul></td></tr>
</table></br>

## <a name="release-notes"></a>Заметки о выпуске

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Добавлена поддержка операции BulkDelete для учетных записей API Azure Cosmos DB SQL.
* Добавлена поддержка операции BulkImport для учетных записей API Azure Cosmos DB MongoDB.
* Зависимость пакета SDK для .NET DocumentDB обновлена до версии 2.0.0. 

### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2

* Добавлена поддержка операции BulkImport для учетных записей API Azure Cosmos DB Gremlin.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1

* Исправлена незначительная ошибка операции BulkImport для учетных записей API Azure Cosmos DB SQL.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Добавлена поддержка операции BulkImport и BulkUpdate для учетных записей API Azure Cosmos DB SQL.
