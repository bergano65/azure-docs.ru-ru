---
title: Устранение распространенных ошибок в API Azure Cosmos DB для Mongo DB
description: В этом документе обсуждаются способы устранения распространенных проблем, возникающих в API Azure Cosmos DB для MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 41d667f4e90114052a17c5707989634bbb5fc421
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/07/2020
ms.locfileid: "75719840"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Устранение распространенных неполадок в API Azure Cosmos DB для MongoDB

Azure Cosmos DB реализует сетевые протоколы общих баз данных NoSQL, включая MongoDB. Из-за реализации протокола проводной связи можно прозрачно взаимодействовать с Azure Cosmos DB с помощью существующих клиентских пакетов SDK, драйверов и средств, работающих с базами данных NoSQL. Azure Cosmos DB не использует исходный код баз данных для предоставления интерфейсов API, совместимых с, для любой из баз данных NoSQL. Любой драйвер клиента MongoDB, который понимает версии протокола Wire, может подключаться к Azure Cosmos DB.

Несмотря на то, что API Azure Cosmos DB для MongoDB совместим с версией протокола проводной связи MongoDB версии 3,2 (в настоящее время поддерживаются операторы запросов и функции, добавленные в версии 3,4), существуют некоторые настраиваемые коды ошибок, соответствующие Azure Cosmos DB конкретные ошибки. В этой статье описываются различные ошибки, коды ошибок и действия по устранению этих ошибок.

## <a name="common-errors-and-solutions"></a>Распространенные ошибки и решения

| Ошибка               | Код  | Description  | Решение  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Общее число использованных единиц запроса превысило число, подготовленное для коллекции, и было отрегулировано. | Рассмотрите возможность масштабирования пропускной способности, назначенной контейнеру или набору контейнеров, из портал Azure или можно повторить операцию. |
| ExceededMemoryLimit | 16501 | Так как в этой службе несколько клиентов, операция превысила выделенную память для клиента. | Уменьшите область операции, применив более строгие критерии запроса, или обратитесь в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Пример: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Путь индекса, соответствующий указанному элементу упорядочения, исключается, а запрос ORDER BY не имеет соответствующего составного индекса, из которого он может быть обслужен. | 2 | Запрос запрашивает сортировку по неиндексированному полю. | Создание совпадающего индекса (или составного индекса) для предпринятого запроса сортировки. |
| Проблемы с версией сети MongoDB | - | Более старые версии драйверов MongoDB не могут обнаружить имя учетной записи Azure Cosmos в строках подключения. | Добавление *appName = @**accountName**@* в конце API в Cosmos DB для строки подключения MongoDB, где ***accountName*** — это имя учетной записи Cosmos DB . |


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.

