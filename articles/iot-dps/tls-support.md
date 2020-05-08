---
title: Поддержка TLS службы подготовки устройств Интернета вещей Azure (DPS)
description: Рекомендации по использованию защищенных TLS-подключений для устройств и служб, взаимодействующих со службой подготовки устройств Интернета вещей (DPS)
services: iot-dps
author: wesmc7777
ms.service: iot-dps
ms.topic: conceptual
ms.date: 05/11/2020
ms.author: wesmc
ms.openlocfilehash: 285832d80d37c8553ffc8e37c6f6eab5d7f6d943
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82984854"
---
# <a name="tls-support-in-azure-iot-hub-device-provisioning-service-dps"></a>Поддержка TLS в службе подготовки устройств для центра Интернета вещей Azure (DPS)

Служба DPS использует протокол TLS для защиты подключений с устройств IoT. В настоящее время поддерживаются три версии протокола TLS, а именно версии 1,0, 1,1 и 1,2.

Протоколы TLS 1,0 и 1,1 считаются устаревшими и планируются для устаревания. Дополнительные сведения см. в разделе [устаревшие протоколы TLS 1,0 и 1,1 для центра Интернета вещей](../iot-hub/iot-hub-tls-deprecating-1-0-and-1-1.md). При подключении к службе DPS настоятельно рекомендуется использовать TLS 1,2 в качестве предпочтительной версии TLS.

## <a name="restrict-connections-to-tls-12"></a>Ограничение подключений к TLS 1,2

Для повышения безопасности рекомендуется настроить экземпляры DPS так, чтобы *только* подключения клиентов устройств, использующие TLS версии 1,2, и принудительно использовать [рекомендованные шифры](#recommended-ciphers).

Для этого подготавливает новый ресурс DPS в любом из [поддерживаемых регионов](#supported-regions) и присвойте `minTlsVersion` свойству значение `1.2` в спецификации ресурса DPS шаблона Azure Resource Manager. В следующем примере шаблона JSON указывается `minTlsVersion` свойство для нового экземпляра DP.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources": [
        {
            "type": "Microsoft.Devices/ProvisioningServices",
            "apiVersion": "2020-01-01",
            "name": "<provide-a-valid-DPS-resource-name>",
            "location": "<any-of-supported-regions-below>",
            "properties": {
                "minTlsVersion": "1.2"
            },
            "sku": {
                "name": "S1",
                "capacity": 1
            },
        }     
    ]
}
```

Вы можете развернуть шаблон с помощью следующей команды Azure CLI. 

```azurecli
az deployment group create -g <your resource group name> --template-file template.json
```

Дополнительные сведения о создании ресурсов DPS с помощью шаблонов диспетчер ресурсов см. в разделе [Настройка политики DPS с помощью шаблона Azure Resource Manager](quick-setup-auto-provision-rm.md).

Ресурс DPS, созданный с помощью этой конфигурации, отказывает устройства, которые пытаются подключиться с помощью TLS версии 1,0 и 1,1. Аналогичным образом, подтверждение TLS будет отвергнуто, если в сообщении HELLO клиента устройства не перечислены [рекомендованные шифры](#recommended-ciphers).

> [!NOTE]
> `minTlsVersion` Свойство доступно только для чтения и не может быть изменено после создания ресурса DPS. Поэтому важно правильно протестировать и проверить, что *все* устройства Интернета вещей совместимы с TLS 1,2 и [рекомендованными шифрами](#recommended-ciphers) заранее.

## <a name="supported-regions"></a>Поддерживаемые регионы

Экземпляры DP центра Интернета вещей, требующие использования TLS 1,2, могут быть созданы в следующих регионах:

* US Gov (Аризона)
* US Gov (Вирджиния)

> [!NOTE]
> После отработки отказа `minTlsVersion` свойство DPS остается эффективным в географической области после отработки отказа.

## <a name="recommended-ciphers"></a>Рекомендуемые шифры

Экземпляры DPS, настроенные для приема только TLS 1,2, также будут применять следующие Рекомендуемые шифры:

* `TLS_ECDHE_RSA_WITH_AES_128_GCM_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_GCM_SHA384`
* `TLS_ECDHE_RSA_WITH_AES_128_CBC_SHA256`
* `TLS_ECDHE_RSA_WITH_AES_256_CBC_SHA384`

## <a name="use-tls-12-in-the-iot-sdks"></a>Использование TLS 1,2 в пакетах SDK для IoT

Используйте приведенные ниже ссылки, чтобы настроить TLS 1,2 и разрешенные шифры в пакетах SDK клиента Azure IoT.

| Язык | Версии, поддерживающие TLS 1,2 | Документация |
|----------|------------------------------------|---------------|
| C        | Тег 2019-12-11 или более поздней версии            | [Ссылка](https://aka.ms/Tls_C_SDK_IoT) |
| Python   | Версия 2.0.0 или более поздняя             | [Ссылка](https://aka.ms/Tls_Python_SDK_IoT) |
| C#       | Версия 1.21.4 или более поздняя            | [Ссылка](https://aka.ms/Tls_CSharp_SDK_IoT) |
| Java     | Версия 1.19.0 или более поздняя            | [Ссылка](https://aka.ms/Tls_Java_SDK_IoT) |
| Node.js   | Версия 1.12.2 или более поздняя            | [Ссылка](https://aka.ms/Tls_Node_SDK_IoT) |


## <a name="use-tls-12-with-iot-edge"></a>Использование TLS 1,2 с IoT Edge

IoT Edge устройства можно настроить для использования TLS 1,2 при взаимодействии с центром Интернета вещей и службой DPS. Для этой цели используйте [страницу документации IOT Edge](https://github.com/Azure/iotedge/blob/master/edge-modules/edgehub-proxy/README.md).