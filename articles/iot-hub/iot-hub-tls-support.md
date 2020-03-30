---
title: Поддержка TLS-центра Azure IoT
description: Лучшие практики в использовании безопасных соединений TLS для устройств и служб, взаимодействуют с IoT Hub
services: iot-hub
author: rezasherafat
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 01/10/2020
ms.author: rezas
ms.openlocfilehash: 7ab3b48d22f116a707f68cbf6284928c7d2557e3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79409509"
---
# <a name="tls-support-in-iot-hub"></a>Поддержка TLS в концентраторе IoT

IoT Hub использует Transport Layer Security (TLS) для обеспечения связи с устройствами и службами IoT. В настоящее время поддерживаются три версии протокола TLS, а именно версии 1.0, 1.1 и 1.2.

TLS 1.0 и 1.1 считаются устаревшими и планируются для амортизмов. Для получения дополнительной информации [см. Deprecating TLS 1.0 и 1.1 для Концентратора IoT](iot-hub-tls-deprecating-1-0-and-1-1.md). Настоятельно рекомендуется использовать TLS 1.2 в качестве предпочтительной версии TLS при подключении к IoT Hub.

## <a name="restrict-connections-to-tls-12-in-your-iot-hub-resource"></a>Ограничьте подключение к TLS 1.2 на ресурсе IoT Hub

Для дополнительной безопасности рекомендуется настроить ioT-концентраторы, чтобы разрешить *только* клиентские соединения, которые используют версию TLS 1.2, и обеспечить использование [рекомендуемых шифров.](#recommended-ciphers)

Для этого в любом из [поддерживаемых регионов](#supported-regions) будет установлен `minTlsVersion` новый концентратор IoT и установить свойство `1.2` в спецификации концентратора Концентратора IoT шаблона Поиска ресурсов:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/IotHubs",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "<your-hubs-SKU-name>",
                "tier": "<your-hubs-SKU-tier>",
                "capacity": 1
            }
        }
    ]
}
```

Созданный ресурс IoT Hub, использующий эту конфигурацию, откажет сядек и обслуживания клиентов, которые пытаются подключиться с помощью версий TLS 1.0 и 1.1. Аналогичным образом, в рукопожатии TLS будет отказано, если в сообщении HELLO клиента не будет указан ни один из [рекомендуемых шифров.](#recommended-ciphers)

> [!NOTE]
> Свойство `minTlsVersion` читается только и не может быть изменено после создания ресурса IoT Hub. Поэтому очень важно, чтобы вы должным образом проверили и подтвердили, что *все* ваши IoT-устройства и службы совместимы с TLS 1.2 и [рекомендуемыми шифрами](#recommended-ciphers) заранее.

### <a name="supported-regions"></a>Поддерживаемые регионы

Концентраторы IoT, требующие использования TLS 1.2, могут быть созданы в следующих регионах:

* Восточная часть США
* Центрально-южная часть США
* западная часть США 2
* US Gov (Аризона)
* US Gov (Вирджиния)

> [!NOTE]
> После неудач `minTlsVersion` исправления свойства вашего Концентратора IoT останутся эффективными в геопаревом регионе после неудачи.

### <a name="recommended-ciphers"></a>Рекомендуемые шифры

Концентраторы IoT, настроенные на прием только TLS 1.2, также обеспечивают использование следующих рекомендуемых шифров:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

### <a name="use-tls-12-in-your-iot-hub-sdks"></a>Используйте TLS 1.2 в SDK-центре IoT

Используйте ссылки ниже, чтобы настроить TLS 1.2 и разрешенные шифры в SDK-клиенте IoT Hub.

| Язык | Версии, поддерживающие TLS 1.2 | Документация |
|----------|------------------------------------|---------------|
| C        | Тег 2019-12-11 или новее            | [Ссылку](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Версия 2.0.0 или более новая             | [Ссылку](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Версия 1.21.4 или более новая            | [Ссылку](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Версия 1.19.0 или более новая            | [Ссылку](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Версия 1.12.2 или более новая            | [Ссылку](https://aka.ms/Tls_Node_SDK_IoT) |


### <a name="use-tls-12-in-your-iot-edge-setup"></a>Используйте TLS 1.2 в настройке IoT Edge

Устройства IoT Edge можно настроить на использование TLS 1.2 при общении с Концентратором IoT. Для этого используйте [страницу документации IoT Edge.](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md)