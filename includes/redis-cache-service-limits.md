---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238647"
---
| Ресурс | Ограничение |
| --- | --- |
| Объем кэша |530 ГБ |
| Базы данных |64 |
| Максимальное количество подключенных клиентов |40 000 |
| Кэш Azure Redis реплик, для обеспечения высокой доступности |1 |
| Сегменты кэша уровня "Премиум" с включенной кластеризацией |10 |

Ограничения и размер кэша Redis для Azure отличаются для каждой ценовой категории. Ценовые категории и соответствующих им размерах см. в разделе [кэша Azure Redis ценах](https://azure.microsoft.com/pricing/details/cache/).

Дополнительные сведения об ограничениях конфигурации кэша Redis для Azure см. в разделе о [конфигурации сервера Redis по умолчанию](../articles/azure-cache-for-redis/cache-configure.md#default-redis-server-configuration).

Так как настройка экземпляров кэша Redis для Azure и управление ими осуществляется корпорацией Майкрософт, кэш Redis для Azure поддерживает не все команды Redis. Дополнительные сведения см. в разделе о [командах Redis, неподдерживаемых в кэше Redis для Azure](../articles/azure-cache-for-redis/cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis).

