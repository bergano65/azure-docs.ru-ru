---
title: Поддержка протокола TLS в Центре Интернета вещей Azure
description: Рекомендации по использованию безопасных TLS-подключений для связи устройств и служб с Центром Интернета вещей
services: iot-hub
author: jlian
ms.service: iot-fundamentals
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: jlian
ms.openlocfilehash: 8c52037684215d1672ed813389d0bbace9a03e42
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85080616"
---
# <a name="tls-support-in-iot-hub"></a>Поддержка протокола TLS в Центре Интернета вещей

Центр Интернета вещей использует протокол TLS для защиты подключений от устройств и служб Интернета вещей. Сейчас поддерживаются три версии протокола TLS: 1.0, 1.1 и 1.2.

Протоколы TLS 1.0 и 1.1 считаются устаревшими, и в будущем их использование будет прекращено. Дополнительные сведения см. в статье [Устаревшие протоколы TLS 1.0 и 1.1 для центра Интернета вещей](iot-hub-tls-deprecating-1-0-and-1-1.md). При подключении к центру Интернета вещей настоятельно рекомендуется использовать версию TLS 1.2.

## <a name="tls-12-enforcement-available-in-select-regions"></a>Принудительное применение TLS 1,2 доступно в выбранных регионах

Для повышения безопасности настройте центры Интернета вещей *, чтобы разрешить* подключения клиентов, которые используют TLS версии 1,2, и принудительно использовать [Рекомендуемые шифры](#recommended-ciphers). Эта функция поддерживается только в следующих регионах:

* Восточная часть США
* Центрально-южная часть США
* западная часть США 2
* US Gov (Аризона)
* US Gov (Вирджиния)

Для этой цели подготовьте новый Центр Интернета вещей в любом из поддерживаемых регионов и задайте для свойства `minTlsVersion` значение `1.2` в спецификации ресурса Центра Интернета вещей в шаблоне Azure Resource Manager:

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

Созданный с такой конфигурацией ресурс Центра Интернета вещей будет отклонять попытки подключения от устройств и служб с использованием TLS версий 1.0 и 1.1. Аналогичным образом, подтверждение TLS отклоняется, если в сообщении приветствия клиента (ClientHello) нет ни одного из [рекомендуемых шифров](#recommended-ciphers).

> [!NOTE]
> Свойство `minTlsVersion` доступно только для чтения. После создания ресурса Центра Интернета вещей изменить это свойство нельзя. Поэтому важно заранее убедиться, что *все* ваши устройства и службы Интернета вещей совместимы с TLS 1.2 и [рекомендуемыми шифрами](#recommended-ciphers).
> 
> После отработки отказа свойство `minTlsVersion` Центра Интернета вещей останется в силе в регионе, географически связанном с исходным.

## <a name="recommended-ciphers"></a>Рекомендуемые шифры

Центры Интернета вещей, настроенные на прием подключений только по протоколу TLS 1.2, также будут требовать использования следующих рекомендуемых шифров:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

В центрах Интернета вещей, для которых не настроено требование использовать только протокол TLS 1.2, версия протокола 1.2 будет работать со следующими шифрами:

* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_DHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_DHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_RSA_WITH_AES_256_CBC_SHA256`
* `TLS_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_AES_256_CBC_SHA`
* `TLS_RSA_WITH_AES_128_CBC_SHA`
* `TLS_RSA_WITH_3DES_EDE_CBC_SHA`

## <a name="use-tls-12-in-your-iot-hub-sdks"></a>Использование TLS 1.2 в пакетах SDK для Центра Интернета вещей

Используйте приведенные ниже ссылки, чтобы настроить TLS 1.2 и разрешенные шифры в пакетах SDK для клиента Центра Интернета вещей.

| Язык | Версии, поддерживающие TLS 1.2 | Документация |
|----------|------------------------------------|---------------|
| C        | Тег 2019-12-11 или более новый            | [Ссылка](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Версия 2.0.0 или более новая             | [Ссылка](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Версия 1.21.4 или более новая            | [Ссылка](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Версия 1.19.0 или более новая            | [Ссылка](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Версия 1.12.2 или более новая            | [Ссылка](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-in-your-iot-edge-setup"></a>Использование TLS 1.2 в конфигурации IoT Edge

Устройства IoT Edge можно настроить для использования TLS 1.2 при обмене данными с Центром Интернета вещей. Дополнительные сведения см. [на странице документации IoT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).