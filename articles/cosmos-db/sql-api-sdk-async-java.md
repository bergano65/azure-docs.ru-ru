---
title: Azure Cosmos DB — API-интерфейс, ресурсы и пакет SDK Async Java SQL
description: Сведения о пакете SDK и API-интерфейсе SQL Async Java, включая даты выхода и прекращения использования, а также изменения, внесенные в каждую версию пакета SDK SQL Async Java для Azure Cosmos DB.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: c2cc8663896f9513d5b6ccfb024fac8b826b0d5d
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83660471"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Пакет SDK Async Java в Azure Cosmos DB для API-интерфейса SQL: Заметки о выпуске и материалы
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

Пакет SDK Async Java для API-интерфейса SQL отличается от пакета SDK Java для API-интерфейса SQL возможностью выполнять асинхронные операции с поддержкой [библиотеки Netty](https://netty.io/). Существующий [пакет SDK Java для API-интерфейса SQL](sql-api-sdk-java.md) не поддерживает асинхронные операции. 

> [!IMPORTANT]  
> Это *не* последняя версия пакета SDK для Java для Azure Cosmos DB! Для проекта рекомендуется использовать [пакет SDK для Java версии 4 для Azure Cosmos DB](sql-api-sdk-java-v4.md). Следуйте инструкциям из руководства по [переходу на использование пакета SDK для Java версии 4 для Azure Cosmos DB](migrate-java-v4-sdk.md) и статье о [Reactor и RxJava](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples/blob/master/reactor-rxjava-guide.md). 
>

| |  |
|---|---|
| **Скачивание пакета SDK** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**Документация по API** |[Справочная документация по API Java](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Участие в разработке пакета SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Начало работы** | [Начало работы с пакетом SDK для Async Java](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Пример кода** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Советы по улучшению производительности**| [Файл сведений GitHub](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Минимальная поддерживаемая среда выполнения**|[JDK 8](/java/azure/jdk/?view=azure-java-stable); | 

[!INCLUDE [Release notes](~/azure-cosmosdb-java-v2/changelog/README.md)]
## <a name="faq"></a>ВОПРОСЫ И ОТВЕТЫ
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>См. также раздел
Дополнительные сведения о Cosmos DB см. на странице службы [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/).

