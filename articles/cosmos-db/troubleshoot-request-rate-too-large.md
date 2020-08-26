---
title: Устранение неполадок с частотой запросов Azure Cosmos DB слишком большими исключениями
description: Узнайте, как диагностировать и исправить частоту запросов слишком больших исключений.
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: f8b1129c94ecf80efb60a13a0b80b1cc1817ff3f
ms.sourcegitcommit: 927dd0e3d44d48b413b446384214f4661f33db04
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88871094"
---
# <a name="diagnose-and-troubleshoot-azure-cosmos-db-request-rate-too-large-exceptions"></a>Диагностика и устранение неполадок с частотой запросов Azure Cosmos DB слишком большими исключениями
Сообщение об ошибке "частота запросов слишком велика" или код ошибки 429 указывает на регулирование запросов.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
В следующем разделе содержатся известные причины и решения для слишком большого количества запросов.

### <a name="check-the-metrics"></a>Проверка метрик
Проверьте [Azure Cosmos DBное наблюдение](monitor-cosmos-db.md) , чтобы увидеть число исключений 429.

#### <a name="cause"></a>Причина.
Используемая пропускная способность (единицы запросов в секунду) превысила [подготовленную пропускную способность](set-throughput.md). Пакет SDK автоматически повторяет запросы на основе указанной политики повтора. Если эта ошибка возникает часто, попробуйте увеличить пропускную способность коллекции. Проверьте метрики портала, чтобы узнать, не возникают ли ошибки 429. Проверьте ключ раздела, чтобы обеспечить [равномерное распределение хранилища и объема запросов](partition-data.md).

#### <a name="solution"></a>Решение:
1. Используйте [портал или пакет SDK](set-throughput.md) для увеличения подготовленной пропускной способности.
1. Переключите базу данных или контейнер на [Автомасштабирование](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Дальнейшие шаги
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET.
* Ознакомьтесь с рекомендациями по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md).