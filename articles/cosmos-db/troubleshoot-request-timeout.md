---
title: Устранение неполадок с исключениями времени ожидания запроса на обслуживание Azure Cosmos DB
description: Узнайте, как диагностировать и исправлять исключения времени ожидания запроса на обслуживание Azure Cosmos DB.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: a18c4cf2f2bc1882a82afe0ba201f0d8b070924d
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93081371"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-timeout-exceptions"></a>Диагностика и устранение неполадок Azure Cosmos DB запроса исключений времени ожидания
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Azure Cosmos DB вернул время ожидания запроса HTTP 408.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
Следующий список содержит известные причины и решения для исключений времени ожидания запроса.

### <a name="check-the-sla"></a>Проверка соглашения об уровне обслуживания
Проверьте [Azure Cosmos DBное наблюдение](monitor-cosmos-db.md) , чтобы узнать, нарушает ли количество исключений 408 Azure Cosmos DB SLA.

#### <a name="solution-1-it-didnt-violate-the-azure-cosmos-db-sla"></a>Решение 1. оно не нарушает соглашение об уровне обслуживания Azure Cosmos DB
Приложение должно справиться с этим сценарием и повторить попытку для этих временных сбоев.

#### <a name="solution-2-it-did-violate-the-azure-cosmos-db-sla"></a>Решение 2. оно нарушает соглашение об уровне обслуживания Azure Cosmos DB
Обратитесь в [службу поддержки Azure](https://aka.ms/azure-support).
 
### <a name="hot-partition-key"></a>Ключ горячей секции
Azure Cosmos DB распределяет общую пропускную способность равномерно по физическим секциям. При наличии горячей секции один или несколько ключей логических секций в физическом разделе потребляют все единицы запросов физического раздела в секунду (единиц запроса/с). В то же время единицы запросов/s на других физических секциях будут недоступными. В качестве симптома общий объем занятых единиц запросов в секунду будет меньше, чем общая подготовленная единица запросов/s в базе данных или контейнере. Вы по-прежнему увидите регулирование (429s) для запросов к ключу горячей логической секции. Используйте [нормализованную метрику потребления на единицу](monitor-normalized-request-units.md) , чтобы определить, обнаруживается ли в рабочей нагрузке горячую секцию. 

#### <a name="solution"></a>Решение.
Выберите хороший ключ секции, который равномерно распределяет объем запросов и хранилище. Узнайте, как [изменить ключ секции](https://devblogs.microsoft.com/cosmosdb/how-to-change-your-partition-key/).

## <a name="next-steps"></a>Дальнейшие действия
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET.
* Ознакомьтесь с рекомендациями по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md).