---
title: Поддерживаемые функции Azure Synapse Link для Azure Cosmos DB
description: Описание текущего набора действий, поддерживаемых Azure Synapse Link для Azure Cosmos DB
services: synapse-analytics
author: ArnoMicrosoft
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/21/2020
ms.author: acomet
ms.reviewer: jrasnick
ms.openlocfilehash: 1b94d7677026f3695d07be4d83a5059373078c2e
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83599036"
---
# <a name="azure-synapse-link-for-azure-cosmos-db-supported-features"></a>Поддерживаемые функции Azure Synapse Link для Azure Cosmos DB

В этой статье описаны функциональные возможности, которые в данный момент поддерживаются Azure Synapse Link для Azure Cosmos DB. 

## <a name="azure-synapse-support"></a>Поддержка Azure Synapse

В Azure Cosmos DB существует два типа контейнеров:
* Контейнер HTAP — контейнер с поддержкой Synapse Link. Для этого контейнера предусмотрено как хранилище транзакций, так и аналитическое хранилище. 
* Контейнер OLTP — контейнер только с хранилищем транзакций; Synapse Link не поддерживается. 

Вы можете подключиться к контейнеру Azure Cosmos DB без включения Synapse Link. В этом случае вы сможете только считывать и записывать данные в хранилище транзакций. Ниже приведен список поддерживаемых в настоящее время функций в Synapse Link для Azure Cosmos DB. 

| Категория              | Описание |[Spark](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) | [Бессерверная среда SQL](https://docs.microsoft.com/azure/synapse-analytics/sql/on-demand-workspace-overview) |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- | ----------------------------------------------------------- | ----------------------------------------------------------- |
| **Поддержка среды выполнения** |Поддержка чтения или записи средой выполнения Azure Synapse| ✓ | [Связаться с нами](mailto:AskSynapse@microsoft.com?subject=[Enable%20Preview%20Feature]%20SQL%20serverless%20for%20Cosmos%20DB)|
| **Поддержка API Azure Cosmos DB** |Поддержка API как Synapse Link| SQL/MongoDB | SQL/MongoDB |
| **Объект**  |Такие объекты, как таблица, которую можно создать, указав непосредственно на контейнер Azure Cosmos DB| Представление, таблица | Представление |
| **Чтение**    |Чтение данных из контейнера Azure Cosmos DB| OLTP/HTAP | HTAP  |
| **запись**   |Запись данных из среды выполнения в контейнер Azure Cosmos DB| OLTP | Недоступно |

* При записи данных в контейнер Azure Cosmos DB из Spark этот процесс выполняется через хранилище транзакций Azure Cosmos DB и будет влиять на транзакционную производительность Azure Cosmos DB из-за использования единиц запросов.
* Интеграция пула SQL с помощью внешних таблиц в настоящее время не поддерживается.

## <a name="supported-code-generated-actions-for-spark"></a>Поддерживаемые действия, создаваемые кодом, для Spark

| Жест              | Описание |OLTP |HTAP  |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Загрузить в DataFrame** |Загрузка и считывание данных в Spark DataFrame |X| ✓ |
| **Создание таблицы Spark** |Создание таблицы, указывающей на контейнер Azure Cosmos DB|X| ✓ |
| **Запись DataFrame в контейнер** |Запись данных в контейнер|✓| ✓ |
| **Загрузка DataFrame для потоковой передачи из контейнера** |Потоковая передача данных с помощью канала изменений Azure Cosmos DB|✓| ✓ |
| **Запись DataFrame для потоковой передачи в контейнер** |Потоковая передача данных с помощью канала изменений Azure Cosmos DB|✓| ✓ |



## <a name="supported-code-generated-actions-for-sql-serverless"></a>Поддерживаемые действия, создаваемые кодом, для бессерверной среды SQL

| Жест              | Описание |OLTP |HTAP |
| -------------------- | ----------------------------------------------------------- |----------------------------------------------------------- |----------------------------------------------------------- |
| **Выбор первых 100 элементов** |Предварительный просмотр первых 100 элементов из контейнера|X| ✓ |
| **Создание представления** |Создание представления для непосредственного доступа к бизнес-аналитике в контейнере с помощью Synapse SQL|X| ✓ |

## <a name="next-steps"></a>Дальнейшие действия

* Узнайте, как [подключиться к Synapse Link для Azure Cosmos DB](../quickstart-connect-synapse-link-cosmos-db.md).
* [Узнайте, как отправлять запросы в аналитическое хранилище с помощью Spark](how-to-query-analytical-store-spark.md).