---
author: wesmc7777
ms.service: redis-cache
ms.topic: include
ms.date: 11/09/2018
ms.author: wesmc
ms.openlocfilehash: 77019068279261a36f8ba40247e5f5694ca77892
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57554074"
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

