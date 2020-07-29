---
title: Устранение неполадок Azure Cosmos DB частоте запросов к большому исключению
description: Как диагностировать и исправить частоту запросов до большого исключения
author: j82w
ms.service: cosmos-db
ms.date: 07/13/2020
ms.author: jawilley
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: b20ee2e10648d2d8eb248fc3f98c4fb33805f914
ms.sourcegitcommit: dccb85aed33d9251048024faf7ef23c94d695145
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87294524"
---
# <a name="diagnose-and-troubleshoot-cosmos-db-too-many-requests"></a>Диагностика и устранение неполадок Cosmos DB слишком много запросов
"Частота запросов слишком велика" или код ошибки 429 означает, что ваши запросы регулируемы.

## <a name="troubleshooting-steps"></a>Действия по устранению неполадок
Следующий список содержит известные причины и решения для слишком большого числа запросов.

### <a name="1-check-the-metrics"></a>1. Проверка метрик
Клиент должен проверить [Azure Cosmos DB мониторинг](monitor-cosmos-db.md) , чтобы проверить, не исключено ли число 429.

## <a name="cause"></a>Причина.
Потребляемая пропускная способность (единиц запросов/с) превысила [подготовленную пропускную способность](set-throughput.md). Пакет SDK будет автоматически повторять запросы в соответствии с указанной политикой повтора. Если эта ошибка возникает часто, попробуйте увеличить пропускную способность коллекции. Проверьте метрики портала, чтобы узнать, не возникают ли ошибки 429. Проверьте ключ раздела, чтобы обеспечить [равномерное распределение хранилища и объема запросов](partition-data.md).

## <a name="solution"></a>Решение:
1. Используйте [портал или пакет SDK](set-throughput.md) для увеличения подготовленной пропускной способности.
2. Переключите базу данных или контейнер на [Автомасштабирование](provision-throughput-autoscale.md).

## <a name="next-steps"></a>Дальнейшие действия
* [Диагностика и устранение неполадок](troubleshoot-dot-net-sdk.md) при использовании пакета SDK для Azure Cosmos DB .NET
* Сведения о рекомендациях по производительности для [.NET v3](performance-tips-dotnet-sdk-v3-sql.md) и [.NET v2](performance-tips.md)