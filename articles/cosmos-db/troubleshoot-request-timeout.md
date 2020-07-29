---
title: Устранение неполадок с исключением времени ожидания запроса службы Azure Cosmos DB
description: Диагностика и исправление исключения времени ожидания запроса на обслуживание Cosmos DB
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: bd8102ee73721e2a56185709d1076e489ecc7607
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294529"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout"></a>Диагностика и устранение неполадок Azure Cosmos DB время ожидания запроса
Azure Cosmos DB вернул время ожидания запроса HTTP 408

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
Следующий список содержит известные причины и решения для исключений времени ожидания запроса.

### <a name="1-check-the-sla"></a>1. Проверьте соглашение об уровне обслуживания
Клиент должен проверить [Azure Cosmos DB мониторинг](monitor-cosmos-db.md) , чтобы проверить, нарушает ли число 408 исключения Cosmos DB SLA.

#### <a name="solution-1-it-did-not-violate-the-cosmos-db-sla"></a>Решение 1. оно не нарушает соглашение об уровне обслуживания Cosmos DB
Приложение должно справиться с этим сценарием и повторить попытку для этих временных сбоев.

#### <a name="solution-2-it-did-violate-the-cosmos-db-sla"></a>Решение 2. оно нарушает соглашение об уровне обслуживания Cosmos DB
Обратитесь в службу поддержки Azure:https://aka.ms/azure-support
 
### <a name="2-hot-partition-key"></a>2. ключ горячей секции
Azure Cosmos DB распределяет общую пропускную способность равномерно по физическим секциям. При наличии критической секции один или несколько ключей логических секций в физическом разделе используют все единицы запросов к физическому разделу, в то время как единица запросов/с в других физических секциях не используется. В качестве симптома общее потребление единиц запросов в секунду будет меньше, чем общая подготовленная единица запросов/s в базе данных или контейнере, но вы по-прежнему увидите регулирование (429s) для запросов к ключу логической секции. Используйте [нормализованную метрику потребления на единицу](monitor-normalized-request-units.md) , чтобы определить, обнаруживается ли в рабочей нагрузке горячую секцию. 

#### <a name="solution"></a>Решение:
Выберите хороший ключ секции, который равномерно распределяет объем запросов и хранилище. Узнайте, как [изменить ключ секции](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Дальнейшие действия
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET
* Сведения о рекомендациях по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md)