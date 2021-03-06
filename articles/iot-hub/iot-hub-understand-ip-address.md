---
title: Основные сведения об IP-адресе центра Интернета вещей | Документация Майкрософт
description: Узнайте, как выполнять запросы к IP-адресу центра Интернета вещей и его свойствам. IP-адрес центра Интернета вещей может изменяться в определенных сценариях, таких как аварийное восстановление или региональная отработка отказа.
author: philmea
ms.author: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 11/21/2019
ms.openlocfilehash: f05be2725ef766bb1e5fd7f2624e754a2e21698a
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75563179"
---
# <a name="iot-hub-ip-addresses"></a>IP-адреса центра Интернета вещей

Префиксы IP-адресов общедоступных конечных точек центра Интернета вещей периодически публикуются в [теге службы](../virtual-network/service-tags-overview.md) _азуреиосуб_ . Эти префиксы IP-адресов можно использовать для управления подключением между центром Интернета вещей и устройствами или сетевыми ресурсами для реализации различных целей сетевой изоляции.

| Цель | Применимые сценарии | Подход |
|------|-----------|----------|
| Обеспечение взаимодействия устройств и служб с конечными точками центра Интернета вещей | [Обмен](./iot-hub-devguide-messages-c2d.md) сообщениями между устройствами и [облаком](./iot-hub-devguide-messaging.md), [прямые методы](./iot-hub-devguide-direct-methods.md), [устройства и потоки двойников](./iot-hub-devguide-device-twins.md) и [устройства](./iot-hub-device-streams-overview.md) | Используйте теги службы _азуреиосуб_ и _EventHub_ для обнаружения центра Интернета вещей, а также префиксы IP-адресов концентратора событий и настройки разрешения для соответствующих префиксов IP-адресов в параметрах брандмауэра для устройств и служб. Удалите трафик на другие IP-адреса назначения, с которыми устройства или службы не должны взаимодействовать. |
| Убедитесь, что конечная точка устройства центра Интернета вещей получает подключения только от ваших устройств и сетевых ресурсов. | [Обмен](./iot-hub-devguide-messages-c2d.md) сообщениями между устройствами и [облаком](./iot-hub-devguide-messaging.md), [прямые методы](./iot-hub-devguide-direct-methods.md), [устройства и потоки двойников](./iot-hub-devguide-device-twins.md) и [устройства](./iot-hub-device-streams-overview.md) | Используйте [функцию IP-фильтра](iot-hub-ip-filtering.md) центра Интернета вещей, чтобы разрешить подключения с устройств и IP-адреса сетевых ресурсов (см. раздел [ограничения](#limitations-and-workarounds) ). | 
| Убедитесь, что пользовательские ресурсы конечной точки маршрутов (учетные записи хранения, служебная шина и концентраторы событий) доступны только из сетевых ресурсов. | [Маршрутизация сообщений](./iot-hub-devguide-messages-d2c.md) | Следуйте указаниям по ограничению подключения (например, с помощью [правил брандмауэра](../storage/common/storage-network-security.md), [частных ссылок](../private-link/private-endpoint-overview.md)или [конечных точек служб](../virtual-network/virtual-network-service-endpoints-overview.md)). Используйте теги службы _азуреиосуб_ для обнаружения ПРЕФИКСОВ IP-адресов центра Интернета вещей и добавления правил разрешения для этих префиксов IP-адресов в конфигурации брандмауэра ресурса (см. раздел [ограничения](#limitations-and-workarounds) ). |



## <a name="best-practices"></a>Рекомендации

* При добавлении правил Разрешить правила в конфигурацию брандмауэра устройства рекомендуется предоставлять определенные [порты, используемые соответствующими протоколами](./iot-hub-devguide-protocols.md#port-numbers).

* Префиксы IP-адресов центра Интернета вещей могут быть изменены. Эти изменения публикуются периодически с помощью тегов службы, прежде чем они вступают в действие. Поэтому важно разрабатывать процессы для регулярного получения и использования последних версий тегов служб. Этот процесс можно автоматизировать с помощью [API обнаружения тегов служб](../virtual-network/service-tags-overview.md#service-tags-on-premises).

* Используйте *азуреиосуб. [ Region]* тег для обозначения префиксов IP-адресов, используемых конечными точками центра Интернета вещей в определенном регионе. Для учета аварийного восстановления центра обработки данных или для [региональной отработки отказа](iot-hub-ha-dr.md) убедитесь, что также включена возможность подключения к префиксам IP-адресов в регионе геопар центра Интернета вещей.


## <a name="limitations-and-workarounds"></a>Ограничения и обходные решения

* Функция IP-фильтра центра Интернета вещей имеет ограничение в 10 правил. Это ограничение и может быть создано через запросы через службу поддержки клиентов Azure. 

* Настроенные [правила фильтрации IP-адресов](iot-hub-ip-filtering.md) применяются только к конечным точкам IP центра Интернета вещей, а не к встроенной конечной точке концентратора событий центра Интернета вещей. Если также требуется, чтобы фильтрация IP-адресов была применена в концентраторе событий, где хранятся сообщения, вы можете сделать это, доведя свой собственный ресурс концентратора событий, где можно настроить нужные правила фильтрации IP-адресов напрямую. Для этого необходимо предоставить собственный ресурс концентратора событий и настроить [маршрутизацию сообщений](./iot-hub-devguide-messages-d2c.md) для отправки сообщений в этот ресурс вместо встроенного концентратора событий центра Интернета вещей. Наконец, как описано в таблице выше, чтобы включить функции маршрутизации сообщений, необходимо также разрешить подключение из префиксов IP-адресов центра Интернета вещей к ресурсу подготовленного концентратора событий.

* При маршрутизации к учетной записи хранения разрешить трафик из префиксов IP-адресов центра Интернета вещей можно только в том случае, если учетная запись хранения находится в другом регионе, что и центр Интернета вещей.

## <a name="support-for-ipv6"></a>Поддержка IPv6 

IPv6 сейчас не поддерживается в центре Интернета вещей.
