---
title: Настройка времени запуска обработчика канала изменений — Azure Cosmos DB
description: Узнайте, как настроить обработчик канала изменений, чтобы начать чтение с определенной даты и времени
author: ealsur
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: maquaran
ms.openlocfilehash: 600556a06d3f58c4d2ec79a49fdee5e8e04d4036
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70093651"
---
# <a name="how-to-configure-the-change-feed-processor-start-time"></a>Настройка времени запуска обработчика канала изменений

В этой статье описывается, как настроить [обработчик канала изменений](./change-feed-processor.md), чтобы начать чтение с определенной даты и времени.

## <a name="default-behavior"></a>Поведение по умолчанию

При первом запуске обработчик канала изменений инициализирует контейнер аренды и начинает свой [жизненный цикл обработки](./change-feed-processor.md#processing-life-cycle). Все изменения, произошедшие в контейнере до инициализации обработчика канала изменений, при первом запуске не обнаруживаются.

## <a name="reading-from-a-previous-date-and-time"></a>Чтение с предыдущей даты и времени

Обработчик канала изменений можно инициализировать для чтения изменений, начиная с **указанной даты и времени**, передавая экземпляр `DateTime` в `WithStartTime` расширение построителя:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=TimeInitialization)]

Обработчик канала изменений будет инициализирован для указанной даты и времени и начнет считывать изменения, произошедшие после.

## <a name="reading-from-the-beginning"></a>Чтение с начала

В других сценариях, таких как перенос данных или анализ всей истории контейнера, нам нужно прочитать канал изменений с **начала времени существования этого контейнера**. Для этого мы можем использовать `WithStartTime` в расширении построителя, но передать `DateTime.MinValue.ToUniversalTime()`который создаст представление времени в формате UTC для минимального `DateTime` значения, например:

[!code-csharp[Main](~/samples-cosmosdb-dotnet-v3/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed/Program.cs?name=StartFromBeginningInitialization)]

Обработчик канала изменений будет инициализирован и начнет считывать изменения с начала времени существования контейнера.

> [!NOTE]
> Эти параметры настройки работают только для настройки начальной точки времени обработчика канала изменений. Когда инициализация контейнера аренды происходит впервые, изменение параметров не оказывает никакого влияния.

> [!NOTE]
> При указании точки во времени будут считываться только изменения для элементов, которые уже существуют в контейнере. Если элемент был удален, его история в Канале изменений также удаляется.

## <a name="additional-resources"></a>Дополнительные ресурсы

* [Azure Cosmos DB](sql-api-sdk-dotnet.md)
* [Примеры использования в GitHub](https://github.com/Azure/azure-cosmos-dotnet-v3/tree/master/Microsoft.Azure.Cosmos.Samples/Usage/ChangeFeed)
* [Дополнительные примеры на GitHub](https://github.com/Azure-Samples/cosmos-dotnet-change-feed-processor)

## <a name="next-steps"></a>Следующие шаги

Вы можете продолжить знакомство с обработчиком канала изменений, перейдя к следующим статьям:

* [Обработчик канала изменений в Azure Cosmos DB](change-feed-processor.md)
* [Использование оценщика канала изменений](how-to-use-change-feed-estimator.md)
