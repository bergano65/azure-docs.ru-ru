---
title: Устранение распространенных ошибок в API Azure Cosmos DB для Mongo DB
description: В этом документе обсуждаются способы устранения распространенных проблем, возникающих в API Azure Cosmos DB для MongoDB.
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: troubleshooting
ms.date: 07/15/2020
ms.author: lbosq
ms.openlocfilehash: f75374fc88923a0f131d513bebf0ffe1feeca359
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87076768"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Устранение распространенных неполадок в API Azure Cosmos DB для MongoDB

В следующей статье описаны распространенные ошибки и решения для баз данных с использованием Azure Cosmos DB API для MongoDB.

>[!Note]
> В Azure Cosmos DB не размещен модуль MongoDB. Он предоставляет реализацию [протокола MongoDB Wire версии 3,6](mongodb-feature-support-36.md) и устаревшей поддержки [протокола проводной сети версии 3,2](mongodb-feature-support.md), поэтому некоторые из этих ошибок доступны только в API Azure Cosmos DB для MongoDB. 

## <a name="common-errors-and-solutions"></a>Распространенные ошибки и решения

| Ошибка               | Код  | Описание  | Решение  |
|---------------------|-------|--------------|-----------|
| ексцеедедтимелимит   | 50 | В запросе превышено время ожидания в 60 секунд выполнения. | Эта ошибка может быть вызвана многими причинами. Одна из причин заключается в том, что текущая выделенная мощность единиц запросов недостаточно для выполнения запроса. Это можно устранить, увеличив единицы запроса этой коллекции или базы данных. В других случаях эту ошибку можно обойти, разделив большой запрос на более мелкие. |
| TooManyRequests     | 16500 | Общее число использованных единиц запроса превысило число, подготовленное для коллекции, и было отрегулировано. | Рассмотрите возможность масштабирования пропускной способности, назначенной контейнеру или набору контейнеров, из портал Azure или можно повторить операцию. |
| ExceededMemoryLimit | 16501 | Так как в этой службе несколько клиентов, операция превысила выделенную память для клиента. | Уменьшите область операции, применив более строгие критерии запроса, или обратитесь в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Пример: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Путь индекса, соответствующий указанному элементу упорядочения, исключается, а запрос ORDER BY не имеет соответствующего составного индекса, из которого он может быть обслужен. | 2 | Запрос запрашивает сортировку по неиндексированному полю. | Создание совпадающего индекса (или составного индекса) для предпринятого запроса сортировки. |
| Проблемы с версией протокола в MongoDB | - | Более старые версии драйверов MongoDB не могут обнаружить имя учетной записи Azure Cosmos в строках подключения. | Добавьте *AppName = @**AccountName** @ * в конце API Cosmos DB для строки подключения к MongoDB, где ***AccountName*** — это имя учетной записи Cosmos DB. |

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.

