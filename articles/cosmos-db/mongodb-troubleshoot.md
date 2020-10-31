---
title: Устранение распространенных ошибок в API Azure Cosmos DB для Mongo DB
description: В этом документе обсуждаются способы устранения распространенных проблем, возникающих в API Azure Cosmos DB для MongoDB.
author: jasonwhowell
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: jasonh
ms.openlocfilehash: fa33e2ccc5c6cca94ab4e2294a4865745145c1ce
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93096331"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Устранение распространенных неполадок в API Azure Cosmos DB для MongoDB
[!INCLUDE[appliesto-mongodb-api](includes/appliesto-mongodb-api.md)]

В следующей статье описаны распространенные ошибки и решения для баз данных с использованием Azure Cosmos DB API для MongoDB.

>[!Note]
> В Azure Cosmos DB не размещен модуль MongoDB. Он предоставляет реализацию [протокола MongoDB Wire версии 3,6](mongodb-feature-support-36.md) и устаревшей поддержки [протокола проводной сети версии 3,2](mongodb-feature-support.md), поэтому некоторые из этих ошибок доступны только в API Azure Cosmos DB для MongoDB. 

## <a name="common-errors-and-solutions"></a>Распространенные ошибки и способы их устранения

| Ошибка               | Код  | Описание  | Решение  |
|---------------------|-------|--------------|-----------|
| ексцеедедтимелимит   | 50 | Время запроса превысило время ожидания выполнения на 60 с. | Эта ошибка может быть вызвана многими причинами. Одна из причин в том, что текущей выделенной мощности единиц запросов недостаточно для выполнения запроса. Эту проблему можно устранить, увеличив единицы запроса этой коллекции или базы данных. В других случаях эту ошибку можно обойти, разделив большой запрос на более мелкие. |
| TooManyRequests     | 16500 | Общее число использованных единиц запроса превысило число, подготовленное для коллекции, и было отрегулировано. | Рекомендуем масштабировать пропускную способность, назначенную контейнеру или набору контейнеров на портале Azure, или можно выполнить операцию еще раз. |
| ExceededMemoryLimit | 16501 | Так как в этой службе несколько клиентов, операция превысила выделенную память для клиента. | Уменьшите область операции, применив более строгие критерии запроса, или обратитесь в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Пример: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Исключается путь индекса, соответствующий указанному элементу упорядочивания, а запрос ORDER BY не имеет соответствующего составного индекса, из которого он может обслуживаться. | 2 | Запрос вызывает сортировку по неиндексированному полю. | Создание совпадающего индекса (или составного индекса) для предпринятого запроса сортировки. |
| Проблемы с версией протокола в MongoDB | - | Более старые версии драйверов MongoDB не могут обнаружить имя учетной записи Azure Cosmos в строках подключения. | Добавьте *AppName = @ **AccountName** @* в конце API Cosmos DB для строки подключения к MongoDB, где ***AccountName*** — это имя учетной записи Cosmos DB. |

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.

