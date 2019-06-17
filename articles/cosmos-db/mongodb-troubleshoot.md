---
title: Устранение распространенных ошибок в API Azure Cosmos DB для Mongo DB
description: Этот документ описывает способы устранения распространенных проблем, обнаруженных в API Azure Cosmos DB для MongoDB.
author: roaror
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: conceptual
ms.date: 06/05/2019
ms.author: roaror
ms.openlocfilehash: 5b3d3993a497240c1ea18f0fcf852c0e834f6e79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66735711"
---
# <a name="troubleshoot-common-issues-in-azure-cosmos-dbs-api-for-mongodb"></a>Устранение распространенных проблем в Azure Cosmos DB API для MongoDB

Azure Cosmos DB реализует сетевыми протоколами передачи общих баз данных NoSQL, включая MongoDB. Из-за реализации протокола передачи можно явным образом взаимодействовать с Azure Cosmos DB с помощью существующих клиентских пакетов SDK, драйверов и средств, работающих с базами данных NoSQL. Azure Cosmos DB не использует весь исходный код из баз данных для предоставления совместимой передачи API-интерфейсы для всех баз данных NoSQL. Любой драйвер клиента MongoDB, распознаваемые версий протокола сети могут подключаться к Azure Cosmos DB.

Хотя API для MongoDB Azure Cosmos DB совместима с 3,2 версии протокол коммутации MongoDB (операторы и функции, добавленные в версии 3.4 в настоящее время доступна в предварительной версии), существуют некоторые пользовательские коды ошибок, которые соответствуют Azure Cosmos DB конкретные ошибки. Здесь объясняются различные ошибки, коды ошибок и действия для их устранения.

## <a name="common-errors-and-solutions"></a>Распространенные ошибки и решения

| Ошибка               | Код  | Описание  | Решение  |
|---------------------|-------|--------------|-----------|
| TooManyRequests     | 16500 | Общее число использованных единиц запроса превысило число, подготовленное для коллекции, и было отрегулировано. | Попробуйте масштабировать пропускную способность, назначенных контейнер или набор контейнеров на портале Azure, или можно повторить операцию. |
| ExceededMemoryLimit | 16501 | Так как в этой службе несколько клиентов, операция превысила выделенную память для клиента. | Уменьшите область операции, применив более строгие критерии запроса, или обратитесь в службу поддержки на [портале Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Пример: `db.getCollection('users').aggregate([{$match: {name: "Andy"}}, {$sort: {age: -1}}]))` |
| Проблемы версий передачи MongoDB | - | Более ранние версии драйверы MongoDB не удается обнаружить имя учетной записи Azure Cosmos в строках подключения. | Добавление *appName = @**accountName** @*  в конце API в Cosmos DB для строки подключения MongoDB, где ***accountName*** — это имя учетной записи Cosmos DB . |


## <a name="next-steps"></a>Дальнейшие действия

- Узнайте, как [использовать Studio 3T](mongodb-mongochef.md) с API Azure Cosmos DB для MongoDB.
- Узнайте, как [использовать Robo 3T](mongodb-robomongo.md) с API Azure Cosmos DB для MongoDB.
- Ознакомьтесь с [примерами](mongodb-samples.md) MongoDB с API Azure Cosmos DB для MongoDB.

