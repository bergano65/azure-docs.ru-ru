---
title: Как использовать канал изменений Azure Cosmos DB с Функциями Azure
description: Использование канала изменений Azure Cosmos DB с Функциями Azure
author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 08429ca76823b9e6c80a197cc390a5964c4198e6
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65969008"
---
# <a name="serverless-event-based-architectures-with-azure-cosmos-db-and-azure-functions"></a>Бессерверных архитектур на основе событий с помощью Azure Cosmos DB и функций Azure

Функции Azure предоставляют самый простой способ подключения к [веб-канал изменений](change-feed.md). Можно создать небольшой реактивного функций Azure, будет автоматически запущено на каждое новое событие в веб-канал изменений контейнера Azure Cosmos.

![Бессерверные функции на основе событий, работа с триггер Azure Cosmos DB](./media/change-feed-functions/functions.png)

С помощью [триггер Azure Cosmos DB](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger), вы можете использовать [обработчика канала изменений](./change-feed-processor.md)масштабирования приложения и функции обнаружения надежных событий без необходимости поддерживать любой [рабочей роли Инфраструктура](./change-feed-processor.md#implementing-the-change-feed-processor-library). Просто сосредоточиться на логике функции Azure, не беспокоясь о оставшуюся часть конвейера источников событий. Можно даже комбинировать триггер для любого другого [привязок функций Azure](../azure-functions/functions-triggers-bindings.md#supported-bindings).

> [!NOTE]
> Триггер Azure Cosmos DB, в настоящее время поддерживается для использования с Core (SQL) API только.

## <a name="requirements"></a>Требования

Чтобы реализовать поток без сервера на основе событий, вам потребуется:

* **Отслеживаемый контейнер**: Отслеживаемый контейнер является отслеживаемый контейнер Azure Cosmos и сохраняет данные, из которого создается канал изменений. Все операции вставки и изменения (например, CRUD) в отслеживаемый контейнер, отражаются в веб-канал изменений контейнера.
* **Аренда контейнера**: Аренда контейнера сохраняет сведения о состоянии в нескольких и экземпляров динамического бессерверной функции Azure и обеспечивает динамическое масштабирование. Этот контейнер аренды можно вручную или автоматически создавать по Azure Cosmos DB Trigger.To автоматически создайте Аренда контейнера, установите *CreateLeaseCollectionIfNotExists* флаг в [конфигурации](../azure-functions/functions-bindings-cosmosdb-v2.md#trigger---configuration). Аренда секционированные контейнеры должны иметь `/id` определение ключа секции.

## <a name="create-your-azure-cosmos-db-trigger"></a>Создание триггера Azure Cosmos DB

Создание функции Azure с триггером Azure Cosmos DB теперь поддерживается во всех интегрированной среды разработки функции Azure и интерфейса командной строки интеграции:

* [Расширение Visual Studio](../azure-functions/functions-develop-vs.md) для пользователей Visual Studio.
* [Расширение для Visual Studio Core](https://code.visualstudio.com/tutorials/functions-extension/create-function) для пользователей Visual Studio Code.
* И наконец [инструментарий Core CLI](../azure-functions/functions-run-local.md#create-func) для кроссплатформенный интерфейс зависит от интегрированной среды разработки.

## <a name="run-your-azure-cosmos-db-trigger-locally"></a>Локальный запуск триггера Azure Cosmos DB

Можно запустить ваш [функции Azure локально](../azure-functions/functions-develop-local.md) с [эмулятор Azure Cosmos DB](./local-emulator.md) для создания и разработки бессерверных последовательностей на основе событий без подписки Azure и без каких-либо затрат.

Если вы хотите протестировать сценариев в реальном времени в облаке, вы можете [бесплатная пробная версия Cosmos DB](https://azure.microsoft.com/try/cosmosdb/) без кредитной карты или требуется подписка Azure.

## <a name="next-steps"></a>Дальнейшие действия

Теперь вы можете получить дополнительные сведения о веб-канала в следующих статьях изменений:

* [Работа с поддержкой веб-канала изменений в Azure Cosmos DB](change-feed.md)
* [Reading Azure Cosmos DB change feed](read-change-feed.md) (Чтение веб-канала изменений Azure Cosmos DB)
* [Using the Azure Cosmos DB change feed processor library](change-feed-processor.md) (Использование библиотеки обработчика канала изменений Azure Cosmos DB)
* [Using the Azure Cosmos DB change feed processor library](change-feed-processor.md) (Использование библиотеки обработчика канала изменений Azure Cosmos DB)
* [Обработка данных бессерверных баз данных с помощью Azure Cosmos DB и Функций Azure](serverless-computing-database.md)
