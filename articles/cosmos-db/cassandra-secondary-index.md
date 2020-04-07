---
title: Индексирование в учетной записи Azure Cosmos DB Cassandra API
description: Узнайте, как работает вторичное индексирование в учетной записи Azure Azure Cosmos DB Cassandra API.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/04/2020
ms.author: thvankra
ms.reviewer: sngun
ms.openlocfilehash: 7de38097acdbfa1f9c9b90f3051c68dec5465b32
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758031"
---
# <a name="secondary-indexing-in-azure-cosmos-db-cassandra-api"></a>Вторичная индексация в Azure Cosmos DB Cassandra API

API Cassandra в Azure Cosmos DB использует базовую инфраструктуру индексирования для демонстрации силы индексирования, присущей платформе. Однако, в отличие от базового API,S'L API в Azure Cosmos DB не индексирует все атрибуты по умолчанию. Вместо этого он поддерживает вторичную индексацию для создания индекса на определенные атрибуты, который ведет себя так же, как Apache Cassandra.  

Как правило, не рекомендуется выполнять запросы фильтров на столбцах, которые не разделены. Необходимо использовать синтаксис ALLOW FILTERING явно, что приводит к операции, которая может не выполнить хорошо. В Azure Cosmos DB вы можете запускать такие запросы на атрибутах низкой степени кардинальности, поскольку они раздуваются в разных разделах для получения результатов.

Не рекомендуется создавать индекс на часто обновляемом столбце. При определении таблицы целесообразно создать индекс. Это гарантирует, что данные и индексы находятся в постоянном состоянии. В случае создания нового индекса на существующих данных в настоящее время нельзя отслеживать изменение прогресса индекса для таблицы. Если вам необходимо отслеживать ход выполнения этой операции, необходимо запросить изменение хода выполнения через [билет поддержки.]( https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request)


> [!NOTE]
> Вторичный индекс не поддерживается на следующих объектах:
> - типы данных, такие как замороженные типы сбора, десятичные и типы вариантов.
> - Статические столбцы
> - Кластерные ключи

## <a name="indexing-example"></a>Пример индексирования

Во-первых, создайте пространство ключа и таблицу образца, запустив следующие команды на подсказке оболочки C'L:

```shell
CREATE KEYSPACE sampleks WITH REPLICATION = {'class' : 'SimpleStrategy'};
CREATE TABLE sampleks.t1(user_id int PRIMARY KEY, lastname text) WITH cosmosdb_provisioned_throughput=400;
``` 

Затем вставьте образец пользовательских данных со следующими командами:

```shell
insert into sampleks.t1(user_id,lastname) values (1, 'nishu');
insert into sampleks.t1(user_id,lastname) values (2, 'vinod');
insert into sampleks.t1(user_id,lastname) values (3, 'bat');
insert into sampleks.t1(user_id,lastname) values (5, 'vivek');
insert into sampleks.t1(user_id,lastname) values (6, 'siddhesh');
insert into sampleks.t1(user_id,lastname) values (7, 'akash');
insert into sampleks.t1(user_id,lastname) values (8, 'Theo');
insert into sampleks.t1(user_id,lastname) values (9, 'jagan');
```

Если вы попытаетесь выполнить следующее утверждение, вы столкнетесь `ALLOW FILTERING`с ошибкой, которая просит вас использовать: 

```shell
select user_id, lastname from sampleks.t1 where lastname='nishu';
``` 

Хотя Cassandra API поддерживает ALLOW FILTERING, как упоминалось в предыдущем разделе, это не рекомендуется. Вместо этого следует создать индекс в том виде, в каком показано в следующем примере:

```shell
CREATE INDEX ON sampleks.t1 (lastname);
```
После создания индекса на поле "последнее имя" теперь можно успешно запустить предыдущий запрос. С помощью API Cassandra в Azure Cosmos DB не нужно указано имя индекса. Используется индекс по `tablename_columnname_idx` умолчанию с форматом. Например, ` t1_lastname_idx` это имя индекса для предыдущей таблицы.

## <a name="dropping-the-index"></a>Падение индекса 
Вы должны знать, что имя индекса является падение индекса. Запустите `desc schema` команду, чтобы получить описание таблицы. Выход этой команды включает имя индекса `CREATE INDEX tablename_columnname_idx ON keyspacename.tablename(columnname)`в формате . Затем можно использовать имя индекса для снижения индекса, как показано в следующем примере:

```shell
drop index sampleks.t1_lastname_idx;
```

## <a name="next-steps"></a>Дальнейшие действия
* Узнайте, как работает [автоматическая индексация](index-overview.md) в Azure Cosmos DB
* [Особенности Apache Cassandra поддерживаются Azure Cosmos DB Cassandra API](cassandra-support.md)
