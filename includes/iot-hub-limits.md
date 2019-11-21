---
author: robinsh
ms.author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 10/26/2018
ms.openlocfilehash: 34de38e91d47457d215c7ebf65d04ed2dbae5324
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74224443"
---
The following table lists the limits associated with the different service tiers S1, S2, S3, and F1. For information about the cost of each *unit* in each tier, see [Azure IoT Hub pricing](https://azure.microsoft.com/pricing/details/iot-hub/).

| Ресурс | Стандартный S1 | Стандартный S2 | Стандартный S3 | Бесплатный F1 |
| --- | --- | --- | --- | --- |
| Сообщений в день |400 000 |6 000 000 |300 000 000 |8000 |
| Максимальное число единиц |200 |200 |10 |1 |

> [!NOTE]
> If you anticipate using more than 200 units with an S1 or S2 tier hub or 10 units with an S3 tier hub, contact Microsoft Support.
> 
> 

The following table lists the limits that apply to IoT Hub resources.

| Ресурс | Ограничение |
| --- | --- |
| Максимальное число платных центров IoT на подписку Azure |100 |
| Максимальное число бесплатных центров IoT на подписку Azure |1 |
| Maximum number of characters in a device ID | 128 |
| Максимальное число удостоверений устройств,<br/> возвращаемых в одном вызове |1000 |
| Максимальный срок хранения сообщений Центра Интернета вещей, передаваемых с устройства в облако |7 дней |
| Максимальный размер сообщения, отправляемого с устройства в облако |256 КБ |
| Максимальный размер пакета, отправляемого с устройства в облако |AMQP и HTTP: 256 КБ для всего пакета <br/>MQTT: 256 КБ для каждого сообщения |
| Максимальное число сообщений в пакете, отправляемом с устройства в облако |500 |
| Максимальный размер сообщения, отправляемого из облака на устройство |64 КБ |
| Максимальный срок жизни для сообщений, отправляемых из облака на устройство |2 дня |
| Максимальное число доставок для сообщений, <br/> отправляемых из облака на устройство |100 |
| Maximum cloud-to-device queue depth per device |50 |
| Максимальное число доставок для сообщений обратной связи <br/> в ответ на сообщение, отправляемое из облака на устройство |100 |
| Максимальный срок жизни для сообщений обратной связи <br/> в ответ на сообщение, отправляемое из облака на устройство |2 дня |
| [Maximum size of device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#device-twin-size) | 8 KB for tags section, and 32 KB for desired and reported properties sections each |
| Maximum length of device twin string key | 1 КБ |
| Maximum length of device twin string value | 4 КБ |
| [Maximum depth of object in device twin](../articles/iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format) | 10 |
| Максимальный размер полезных данных прямого метода | 128 КБ |
| Максимальный период удержания журнала заданий | 30 дней |
| Максимальное число параллельных заданий | 10 (S3), 5 (S2), 1 (S1) |
| Максимальное число дополнительных конечных точек | 10 (for S1, S2, and S3) |
| Максимальное число правил маршрутизации сообщений | 100 (for S1, S2, and S3) |
| Максимальное количество одновременно подключенных потоков устройств | 50 (только для S1, S2, S3 и F1) |
| Максимальная скорость передачи данных потоков устройств | 300 МБ в день (только для S1, S2, S3 и F1) |

> [!NOTE]
> If you need more than 100 paid IoT hubs in an Azure subscription, contact Microsoft Support.

> [!NOTE]
> Currently, the total number of devices plus modules that can be registered to a single IoT hub is capped at 1,000,000. Если вам необходимо увеличить это ограничение, обратитесь в [службу поддержки Майкрософт](https://azure.microsoft.com/support/options/).

IoT Hub throttles requests when the following quotas are exceeded.

| Регулирование | Значение концентратора |
| --- | --- |
| операции с реестром удостоверений. <br/> (create, retrieve, list, update, and delete), <br/> индивидуальный или массовый импорт и экспорт |83.33/sec/unit (5,000/min/unit) (for S3). <br/> 1,67 операций в секунду на единицу (100 операций в минуту на единицу; для S1 и S2). |
| Подключение устройств |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Минимальное значение — 100/с. |
| Передачи с устройства в облако |6,000/sec/unit (for S3), 120/sec/unit (for S2), 12/sec/unit (for S1). <br/>Минимальное значение — 100/с. |
| Передачи из облака на устройство | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S1 and S2). |
| Получение из облака на устройство |833.33/sec/unit (50,000/min/unit) (for S3), 16.67/sec/unit (1,000/min/unit) (for S1 and S2). |
| Операции отправки файлов |83.33 file upload initiations/sec/unit (5,000/min/unit) (for S3), 1.67 file upload initiations/sec/unit (100/min/unit) (for S1 and S2). <br/> 10,000 SAS URIs can be out for an Azure Storage account at one time.<br/> Допускается одновременная выдача до 10 универсальных кодов ресурса (URI) SAS на устройство. |
| Прямые методы | 24 MB/sec/unit (for S3), 480 KB/sec/unit (for S2), 160 KB/sec/unit (for S1).<br/> Based on 8-KB throttling meter size. |
| Операции чтения двойника устройства | 500/sec/unit (for S3), Maximum of 100/sec or 10/sec/unit (for S2), 100/sec (for S1) |
| Обновления двойников устройств | 250/sec/unit (for S3), Maximum of 50/sec or 5/sec/unit (for S2), 50/sec (for S1) |
| Операции заданий <br/> (create, update, list, and delete) | 83.33/sec/unit (5,000/min/unit) (for S3), 1.67/sec/unit (100/min/unit) (for S2), 1.67/sec/unit (100/min/unit) (for S1). |
| Пропускная способность для операций заданий на уровне отдельного устройства | 50/sec/unit (for S3), maximum of 10/sec or 1/sec/unit (for S2), 10/sec (for S1). |
| Начальная скорость потоков устройств | 5 new streams/sec (for S1, S2, S3, and F1 only). |
