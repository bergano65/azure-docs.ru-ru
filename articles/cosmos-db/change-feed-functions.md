---
title: Как использовать канал изменений Azure Cosmos DB с Функциями Azure
description: Используйте функции Azure для подключения к каналу изменения DB Azure Cosmos. Позже можно создать реактивные функции Azure, которые срабатывают на каждом новом событии.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 7a74635551d8416bf60689b1f1403f29883e81bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78851371"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Архитектуры на основе серверов с функциями Azure Cosmos DB и Azure

Функции Azure обеспечивают самый простой способ подключения к [ленте изменений.](change-feed.md) Можно создать небольшие реактивные функции Azure, которые будут автоматически срабатывать на каждом новом событии в ленте изменения контейнера Azure Cosmos.

![Серверные функции на основе событий, работающие с триггером Azure Functions для Cosmos DB](./media/change-feed-functions/functions.png)

С [помощью триггера Azure Functions для Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md)можно использовать масштабирование процессора [Change Feed](./change-feed-processor.md)и надежную функциональность обнаружения событий без необходимости поддерживать какую-либо [рабочую инфраструктуру.](./change-feed-processor.md) Просто сосредоточьтесь на логике функции Azure, не беспокоясь об остальной части конвейера поиска событий. Вы даже можете смешивать триггер с любыми [другими привязками функций Azure.](../azure-functions/functions-triggers-bindings.md#supported-bindings)

> [!NOTE]
> В настоящее время триггер Azure Functions для Cosmos DB поддерживается для использования только с Помощью API Core (S'L).

## <a name="requirements"></a>Требования

Для реализации бессерверного потока на основе событий необходимо:

* **Контролируемый контейнер:** Контролируемый контейнер является контейнером Azure Cosmos, на котором ведется мониторинг, и в нем хранятся данные, из которых генерируется канал изменений. Любые вставки, обновления в контролируемом контейнере отражаются в канале изменения контейнера.
* **Контейнер аренды:** Контейнер аренды поддерживает состояние в нескольких и динамических серверных экземплярах функции Azure и обеспечивает динамическое масштабирование. Этот контейнер аренды может быть создан вручную или автоматически с помощью триггера Azure Functions для Cosmos DB. Чтобы автоматически создать контейнер аренды, установите флаг *CreateLeaseCollectionNotExists* в [конфигурации.](../azure-functions/functions-bindings-cosmosdb-v2-trigger.md#configuration) Разделированные контейнеры аренды должны `/id` иметь ключевое определение раздела.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Создайте триггер Azure Functions для Cosmos DB

Создание функции Azure с триггером Azure Functions для Cosmos DB теперь поддерживается во всех интеграциях Azure Functions IDE и CLI:

* [Визуальное расширение студии](../azure-functions/functions-develop-vs.md) для пользователей Visual Studio.
* [Расширение кода Visual Studio](/azure/javascript/tutorial-vscode-serverless-node-01) для пользователей Visual Studio Code.
* И, наконец, [Core CLI инструментарий](../azure-functions/functions-run-local.md#create-func) для кросс-платформенного iDE агностик опыт.

## <a name="run-your-trigger-locally"></a>Выполнить триггер локально

Функция [Azure локально](../azure-functions/functions-develop-local.md) запущена с помощью [эмулятора Azure Cosmos DB](./local-emulator.md) для создания и разработки потоков на основе серверов без серверов без подписки Azure или понесания каких-либо затрат.

Если вы хотите протестировать сценарии в реальном времени в облаке, вы можете [попробовать Cosmos DB бесплатно](https://azure.microsoft.com/try/cosmosdb/) без какой-либо кредитной карты или подписки Azure.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы можете продолжать, чтобы узнать больше об изменении корма в следующих статьях:

* [Работа с поддержкой веб-канала изменений в Azure Cosmos DB](change-feed.md)
* [Чтение канала изменений Azure Cosmos DB](read-change-feed.md)
* [Использование библиотеки процессоров изменяемых каналов](change-feed-processor.md)
* [Using the Azure Cosmos DB change feed processor library](change-feed-processor.md) (Использование библиотеки обработчика канала изменений Azure Cosmos DB)
* [Обработка данных бессерверных баз данных с помощью Azure Cosmos DB и Функций Azure](serverless-computing-database.md)
