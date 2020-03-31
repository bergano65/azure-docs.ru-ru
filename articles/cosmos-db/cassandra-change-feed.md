---
title: Изменение корма в API DB Azure Cosmos для Cassandra
description: Узнайте, как использовать канал изменений в API DB Azure Cosmos для Cassandra, чтобы получить изменения, внесенные в ваши данные.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74694627"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Изменение корма в API DB Azure Cosmos для Cassandra

[Поддержка подачи изменений](change-feed.md) в API DB Azure Cosmos DB для Cassandra доступна через предикаты запроса на языке запросов Кассандра (C'L). Используя эти условия предиката, можно задать запрос API-канала на изменение. Приложения могут получать изменения, внесенные в таблицу, используя основной ключ (также известный как ключ раздела), как это требуется в C'L. Затем можно предпринять дальнейшие действия на основе полученных результатов. Изменения строк в таблице фиксируются в порядке их времени изменения, а порядок сортировки гарантируется на ключ раздела.

Ниже приводится следующий пример, как получить канал изменения на всех строках в таблице Keyspace Cassandra API с помощью .NET. ПредикатCOSMOS_CHANGEFEED_START_TIME() используется непосредственно в рамках КЗЛ для запроса элементов в ленте изменения с указанного времени начала (в данном случае текущего времени даты). Вы можете скачать полный образец [здесь](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

В каждой итерации резюме запроса в последней точке изменения были прочитаны, используя состояние paging. Мы видим непрерывный поток новых изменений в таблице в Keyspace. Мы увидим изменения в строках, которые вставляются или обновляются. Просмотр операций удаления с помощью ленты изменений в API Cassandra в настоящее время не поддерживается. 

```C#
    //set initial start time for pulling the change feed
     DateTime timeBegin = DateTime.UtcNow;

    //initialise variable to store the continuation token
    byte[] pageState = null;
    while (true)
    {
        try
        {

            //Return the latest change for all rows in 'user' table    
            IStatement changeFeedQueryStatement = new SimpleStatement(
            $"SELECT * FROM uprofile.user where COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");
            if (pageState != null)
            {
                changeFeedQueryStatement = changeFeedQueryStatement.SetPagingState(pageState);
            }
            Console.WriteLine("getting records from change feed at last page state....");
            RowSet rowSet = session.Execute(changeFeedQueryStatement);

            //store the continuation token here
            pageState = rowSet.PagingState;

            List<Row> rowList = rowSet.ToList();
            if (rowList.Count != 0)
            {
                for (int i = 0; i < rowList.Count; i++)
                {
                    string value = rowList[i].GetValue<string>("user_name");
                    int key = rowList[i].GetValue<int>("user_id");
                    // do something with the data - e.g. compute, forward to another event, function, etc.
                    // here, we just print the user name field
                    Console.WriteLine("user_name: " + value);
                }
            }
            else
            {
                Console.WriteLine("zero documents read");
            }
        }
        catch (Exception e)
        {
            Console.WriteLine("Exception " + e);
        }
    }

```

Для того, чтобы получить изменения в одну строку по основному ключу, можно добавить основной ключ в запросе. Ниже приводится следующий пример, как отслеживать изменения для строки, где "user_id No 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Текущие ограничения

При использовании канала изменений с помощью API Cassandra API применяются следующие ограничения:

* Вставки и обновления в настоящее время поддерживаются. Операция удаления еще не поддерживается. В качестве обходного пути можно добавить мягкий маркер на строках, которые удаляются. Например, добавить поле в строке под названием "удалено" и установить его на "истинное".
* Последнее обновление сохраняется, так как в базовом API S'L и промежуточные обновления для сущности недоступны.


## <a name="error-handling"></a>Обработка ошибок

При использовании ленты изменений в API Cassandra API поддерживаются следующие коды ошибок и сообщения:

* **КОД ошибки HTTP 429** - Когда скорость изменения ограничена, он возвращает пустую страницу.

## <a name="next-steps"></a>Дальнейшие действия

* [Управление API-ресурсами Azure Cosmos Cassandra API с помощью шаблонов управления ресурсами Azure](manage-cassandra-with-resource-manager.md)
