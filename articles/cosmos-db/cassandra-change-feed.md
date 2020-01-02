---
title: Веб-канал изменений в Azure Cosmos DB API для Cassandra
description: Узнайте, как использовать веб-канал изменений в Azure Cosmos DB API для Cassandra, чтобы получить изменения, внесенные в данные.
author: TheovanKraay
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: conceptual
ms.date: 11/25/2019
ms.author: thvankra
ms.openlocfilehash: c2c695608653130b97bf29cc9ce48e2fbb429209
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/02/2019
ms.locfileid: "74694627"
---
# <a name="change-feed-in-the-azure-cosmos-db-api-for-cassandra"></a>Веб-канал изменений в Azure Cosmos DB API для Cassandra

Поддержка [веб-канала изменений](change-feed.md) в Azure Cosmos DB API для Cassandra доступна в предикатах запросов на языке запросов Cassandra (CQL). Используя эти условия предиката, можно запросить API веб-канала изменений. Приложения могут получать изменения, внесенные в таблицу с помощью первичного ключа (также известного как ключ секции), как это требуется в CQL. Затем можно выполнить дальнейшие действия на основе результатов. Изменения в строках таблицы фиксируются в порядке их изменения, а порядок сортировки гарантируется по ключу секции.

В следующем примере показано, как получить канал изменений для всех строк в API Cassandra таблице пространства ключей с помощью .NET. Предикат COSMOS_CHANGEFEED_START_TIME () используется непосредственно в CQL для запроса элементов в веб-канале изменений с указанного времени начала (в данном случае это текущая дата и время). Полный пример можно скачать [здесь](https://docs.microsoft.com/samples/azure-samples/azure-cosmos-db-cassandra-change-feed/cassandra-change-feed/).

В каждой итерации запрос возобновляется в момент чтения последних изменений, с использованием разбиения на страницы. В таблице в пространства ключей можно увидеть непрерывный поток новых изменений. Будут видны изменения в строках, которые вставляются или обновляются. Наблюдение за операциями удаления с помощью веб-канала изменений в API Cassandra в настоящее время не поддерживается. 

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

Чтобы получить изменения в одной строке по первичному ключу, можно добавить в запрос первичный ключ. В следующем примере показано, как отслеживанию изменений в строке, где "user_id = 1"

```C#
    //Return the latest change for all row in 'user' table where user_id = 1
    IStatement changeFeedQueryStatement = new SimpleStatement(
    $"SELECT * FROM uprofile.user where user_id = 1 AND COSMOS_CHANGEFEED_START_TIME() = '{timeBegin.ToString("yyyy-MM-ddTHH:mm:ss.fffZ", CultureInfo.InvariantCulture)}'");

```

## <a name="current-limitations"></a>Текущие ограничения

При использовании веб-канала изменений с API Cassandraми применяются следующие ограничения.

* В настоящее время поддерживаются вставки и обновления. Операция удаления пока не поддерживается. В качестве обходного решения можно добавить мягкую метку для удаляемых строк. Например, добавьте поле в строку с именем "Deleted" и задайте для него значение "true".
* Последнее обновление сохраняется как в базовом API SQL, а промежуточные обновления для сущности недоступны.


## <a name="error-handling"></a>Обработка ошибок

При использовании веб-канала изменений в API Cassandra поддерживаются следующие коды ошибок и сообщения:

* **Код ошибки HTTP 429** — если частота веб-канала изменений ограничена, она возвращает пустую страницу.

## <a name="next-steps"></a>Дальнейшие действия

* [Управление Azure Cosmos DB API Cassandra ресурсами с помощью шаблонов Azure Resource Manager](manage-cassandra-with-resource-manager.md)
