---
title: Как использовать канал изменений Azure Cosmos DB с Функциями Azure
description: Используйте функции Azure для подключения к веб-каналу изменений Azure Cosmos DB. Позже вы сможете создавать реактивные функции Azure, активируемые при каждом новом событии.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/03/2019
ms.reviewer: sngun
ms.openlocfilehash: 215ecc1e392f8e7051173fb6f589fb940c26f17d
ms.sourcegitcommit: 9405aad7e39efbd8fef6d0a3c8988c6bf8de94eb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/05/2019
ms.locfileid: "74872253"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Бессерверные архитектуры на основе событий с Azure Cosmos DB и функциями Azure

Функции Azure предоставляют самый простой способ подключения к веб- [каналу изменений](change-feed.md). Вы можете создать небольшие функции повторной подготовки Azure, которые будут автоматически запускаться для каждого нового события в веб-канале изменений контейнера Azure Cosmos.

![Функции на основе событий без сервера, работающие с триггером функций Azure для Cosmos DB](./media/change-feed-functions/functions.png)

При [активации функций Azure для Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger)можно использовать функции масштабирования и надежности [обработчика веб-канала изменений](./change-feed-processor.md)без необходимости поддерживать какую-либо [рабочую инфраструктуру](./change-feed-processor.md). Просто сосредоточьтесь на логике функции Azure, не беспокоясь о остальной части конвейера по источникам событий. Вы даже можете смешивать триггеры с другими [привязками функций Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> В настоящее время триггер функций Azure для Cosmos DB поддерживается только для API ядра (SQL).

## <a name="requirements"></a>Требования

Чтобы реализовать поток на основе событий без сервера, вам потребуется:

* **Отслеживаемый контейнер**. отслеживаемый контейнер — это контейнер Cosmos Azure, который отслеживается, и в нем хранятся данные, из которых создается веб-канал изменений. Любые операции вставки, обновления отслеживаемого контейнера отражаются в веб-канале изменений контейнера.
* **Контейнер аренды**. контейнер аренды сохраняет состояние в нескольких динамических экземплярах функций Azure без сервера и включает динамическое масштабирование. Этот контейнер аренды можно вручную или автоматически создать с помощью триггера функций Azure для Cosmos DB. Чтобы автоматически создать контейнер аренды, установите флаг *креателеасеколлектионифнотексистс* в [конфигурации](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Секционированные контейнеры аренды должны иметь `/id`ное определение ключа секции.

## <a name="create-your-azure-functions-trigger-for-cosmos-db"></a>Создание триггера функций Azure для Cosmos DB

Создание функции Azure с триггером функций Azure для Cosmos DB теперь поддерживается во всех интеграциях IDE и CLI функций Azure.

* [Расширение Visual Studio](../azure-functions/functions-develop-vs.md) для пользователей Visual Studio.
* [Расширение ядра Visual Studio](/azure/javascript/tutorial-vscode-serverless-node-01) для пользователей Visual Studio Code.
* И, наконец, [основные средства интерфейса командной строки](../azure-functions/functions-run-local.md#create-func) для кросс-платформенного интерфейса IDE.

## <a name="run-your-trigger-locally"></a>Запуск триггера локально

Вы можете запустить свою [функцию Azure локально](../azure-functions/functions-develop-local.md) с помощью [эмулятора Azure Cosmos DB](./local-emulator.md) , чтобы создавать и разрабатывать бессерверные потоки, основанные на событиях, без подписки Azure и не требуя каких-либо затрат.

Если вы хотите протестировать сценарии в реальном времени в облаке, вы можете [попробовать Cosmos DB бесплатно](https://azure.microsoft.com/try/cosmosdb/) без какой-либо кредитной карты или подписки Azure.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы можете продолжить изучение веб-канала изменений в следующих статьях:

* [Работа с поддержкой веб-канала изменений в Azure Cosmos DB](change-feed.md)
* [Чтение канала изменений Azure Cosmos DB](read-change-feed.md)
* [Using the Azure Cosmos DB change feed processor library](change-feed-processor.md) (Использование библиотеки обработчика канала изменений Azure Cosmos DB)
* [Using the Azure Cosmos DB change feed processor library](change-feed-processor.md) (Использование библиотеки обработчика канала изменений Azure Cosmos DB)
* [Обработка данных бессерверных баз данных с помощью Azure Cosmos DB и Функций Azure](serverless-computing-database.md)
