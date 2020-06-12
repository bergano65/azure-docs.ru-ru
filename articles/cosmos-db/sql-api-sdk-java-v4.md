---
title: 'Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK Java версии 4 для API-интерфейса SQL'
description: Сведения о пакете SDK версии 4 и API-интерфейсе SQL для Azure Cosmos DB, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK SQL Async Java для Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.openlocfilehash: b222a94ee754b24192261451d8ddc429886e705c
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83725658"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB: заметки о выпуске и материалы по пакету SDK Java версии 4 для API-интерфейса Core (SQL)
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [Веб-канал изменений в .NET](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Пакет SDK для Java версии 4](sql-api-sdk-java-v4.md)
> * [Пакет SDK для Async Java версии 2](sql-api-sdk-async-java.md)
> * [Пакет SDK для Sync Java версии 2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [Поставщик ресурсов REST](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Исполнитель массовых операций — .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Исполнитель массовых операций — Java](sql-api-sdk-bulk-executor-java.md)

Пакет SDK Java версии 4 для Azure Cosmos DB для Core (SQL) объединяет асинхронный и синхронный интерфейсы API в один артефакт Maven. Пакет SDK версии 4 обеспечивает более высокую производительность, новые функции API и поддержку асинхронных операций на основе проекта Reactor и [библиотеки Netty](https://netty.io/). При использовании пакета SDK Java версии 4 для Azure Cosmos DB пользователи могут получить более высокую производительность по сравнению с [пакетом SDK Async Java версии 2 для Azure Cosmos DB](sql-api-sdk-async-java.md) и [пакетом SDK Sync Java версии 2 для Azure Cosmos DB](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Эти заметки о выпуске применимы только к пакету SDK Java для Azure Cosmos DB версии 4. Если сейчас вы используете более раннюю версию, чем версия 4, руководство [Перевод приложения на использование пакета средств разработки Java для Azure Cosmos DB версии 4](migrate-java-v4-sdk.md) поможет вам обновить его до версии 4.
>
> Ниже приведены шаги, позволяющие быстро приступить к работе.
> 1. Установите [минимальную поддерживаемую среду выполнения Java (JDK 8)](/java/azure/jdk/?view=azure-java-stable), чтобы можно было использовать пакет SDK.
> 2. Ознакомьтесь с [кратким руководством по пакету SDK Java версии 4 для Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java), чтобы получить доступ к артефакту Maven и изучить основные запросы к Azure Cosmos DB.
> 3. Ознакомьтесь с [советами по повышению производительности](performance-tips-java-sdk-v4-sql.md) пакета SDK Java версии 4 для Azure Cosmos DB и руководствами по [устранению неполадок](troubleshoot-java-sdk-v4-sql.md), чтобы оптимизировать пакет SDK для приложения.
>
> [Семинары и лабораторные занятия по Azure Cosmos DB](https://aka.ms/cosmosworkshop) — еще один отличный ресурс для освоения пакета SDK Java версии 4 для Azure Cosmos DB.
>

| |  |
|---|---|
| **Скачивание пакета SDK** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**Документация по API** | [Справочная документация по API Java](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-cosmos/4.0.1-beta.3/index.html) |
|**Участие в разработке пакета SDK** | [Центральный репозиторий пакета SDK Azure для Java в GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Начало работы** | [Краткое руководство. Создайте приложение Java для управления данными API SQL для Azure Cosmos DB](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) — [репозиторий GitHub с простым примером кода](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) | 
|**Простые примеры кода** | [Разработка Примеры Java для API SQL](sql-api-java-sdk-samples.md) — [репозиторий GitHub с примером кода](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Консольное приложение с каналом изменений**| [Канал изменений — пример использования пакета SDK Java версии 4](create-sql-api-java-changefeed.md) — [репозиторий GitHub с примером кода](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Пример веб-приложения**| [Создание веб-приложения с помощью пакета SDK Java версии 4](sql-api-java-application.md) — [репозиторий GitHub с примером кода](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Советы по улучшению производительности**| [Советы по повышению производительности при использовании пакета SDK для Java версии 4](performance-tips-java-sdk-v4-sql.md)| 
| **Устранение неполадок** | [Устранение неполадок с пакетом SDK для Java версии 4](troubleshoot-java-sdk-v4-sql.md) |
| **Переход на пакет SDK версии 4 с более ранней версии** | [Переход на пакет SDK для Java версии 4](migrate-java-v4-sdk.md) |
| **Минимальная поддерживаемая среда выполнения**|[JDK 8](/java/azure/jdk/?view=azure-java-stable); | 
| **Семинары и лабораторные занятия по Azure Cosmos DB** |[Домашняя страница семинаров по Cosmos DB](https://aka.ms/cosmosworkshop)

