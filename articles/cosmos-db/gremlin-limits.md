---
title: Ограничения Azure Cosmos DB Gremlin
description: Справочная документация по ограничениям среды выполнения подсистемы Graph
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: sngun
ms.openlocfilehash: 4ac5222e735e0699e507ca1392dc5dd42fc77105
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91400474"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Ограничения Azure Cosmos DB для Gremlin
В этой статье рассказывается об ограничениях Azure Cosmos DB Gremlin Engine и объясняется, как они могут повлиять на обход клиентов.

Cosmos DB Gremlin построен на основе Cosmos DB инфраструктуры. Из-за этого все ограничения, описанные в [Azure Cosmos DB ограничениях службы](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) , по-прежнему применяются.

## <a name="limits"></a>Ограничения

При достижении ограничения Gremlin обход отменяется с **кодом x-MS-status** 429, указывающим на ошибку регулирования. Дополнительные сведения см. в разделе [Gremlin Server Response Headers](gremlin-limits.md) .

**Ресурс**    | **Ограничение по умолчанию** | **Пояснение**
--- | --- | ---
*Длина скрипта* | **64 КБ** | Максимальная длина для скрипта обхода Gremlin на один запрос.
*Количество операторов* | **400** |  Общее количество уникальных шагов в обходе. Например, ```g.V().out()``` имеет число операторов 2: v () и out (), ```g.V('label').repeat(out()).times(100)``` имеет глубину оператора 3: V (), Repeat () и out (), поскольку ```.times(100)``` является параметром ```.repeat()``` оператора.
*Степень параллелизма* | **32** | Максимальное количество секций хранилища, к которым можно отправить один запрос на уровне хранилища. Это ограничение влияет на графики с сотнями секций.
*Ограничение повторов* | **32** | Максимальное количество итераций оператора ```.repeat()```. Каждая итерация ```.repeat()``` шага в большинстве случаев выполняет обход по ширине, что означает, что любой обход ограничен максимум до 32 прыжков между вершинами.
*Время ожидания обхода* | **30 секунд** | Обход будет отменен, когда он превысит это время. Cosmos DB Graph — это база данных OLTP, большинство обходов в которой выполняется за миллисекунды. Для выполнения запросов OLAP на Cosmos DB графе используйте [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) с [кадрами данных графа](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) и [Cosmos DB соединителя Spark](https://github.com/Azure/azure-cosmosdb-spark).
*Время простоя подключения* | **1 час** | Период времени, в течение которого служба Gremlin будет оставаться в состоянии простоя подключений WebSocket. Пакеты поддержания активности TCP или HTTP-запросы на проверку активности не расширяют продолжительность существования подключения за пределами этого ограничения. Модуль Cosmos DB Graph рассматривает соединения WebSocket как бездействующие, если на нем не выполняются активные запросы Gremlin.
*Количество используемых маркеров ресурсов в час* | **100** | Число уникальных маркеров ресурсов, используемых клиентами Gremlin для подключения к учетной записи Gremlin в регионе. Когда приложение превышает почасовую уникальность маркера, `"Exceeded allowed resource token limit of 100 that can be used concurrently"` будет возвращено при следующем запросе проверки подлинности.

## <a name="next-steps"></a>Дальнейшие шаги
* [Заголовки ответа Azure Cosmos DB Gremlin](gremlin-headers.md)
* [Маркеры ресурса Azure Cosmos DB с Gremlin](how-to-use-resource-tokens-gremlin.md)
