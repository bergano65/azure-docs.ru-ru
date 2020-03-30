---
title: Как передать полезную нагрузку между устройством и службой обеспечения устройств Azure
description: В этом документе описывается, как передать полезную нагрузку между устройством и службой обеспечения устройств (DPS)
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80246693"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Как передать полезную нагрузку между устройством и DPS
Иногда DPS требуется больше данных с устройств, чтобы должным образом предоставить их в правый концентратор IoT, и что данные должны быть предоставлены устройством. Наоборот, DPS может возвращать данные на устройство для облегчения логики стороны клиента. 

## <a name="when-to-use-it"></a>Когда использовать
Эта функция может быть использована в качестве усовершенствования для [пользовательского распределения.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Например, необходимо распределить устройства на основе модели устройства без вмешательства человека. В этом случае вы будете использовать [пользовательское распределение.](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies) Можно настроить устройство, чтобы сообщить информацию о модели как часть [вызова регистрируемого устройства.](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) DPS передаст полезную нагрузку устройства в веб-крюк специального распределения. И ваша функция может решить, какой IoT концентратор это устройство будет идти, когда он получает информацию о модели устройства. Аналогичным образом, если webhook хочет вернуть некоторые данные на устройство, он будет передавать данные обратно в качестве строки в ответ на веб-крюк.  

## <a name="device-sends-data-payload-to-dps"></a>Устройство отправляет полезную нагрузку данных в DPS
Когда устройство отправляет [вызов регистратора в](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) DPS, вызов регистра может быть расширен, чтобы взять другие поля в организме. Тело выглядит следующим образом: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “payload”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>DPS возвращает данные на устройство
Если политика пользовательского распределения webhook желает вернуть некоторые данные на устройство, он передаст данные обратно в виде строки в ответе webhook. Изменение находится в разделе полезной нагрузки ниже. 
   ```
   { 
       "iotHubHostName": "sample-iot-hub-1.azure-devices.net", 
       "initialTwin": { 
           "tags": { 
               "tag1": true 
               }, 
               "properties": { 
                   "desired": { 
                       "tag2": true 
                    } 
                } 
            }, 
        "payload": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Поддержка пакетов SDK
Эта функция доступна в C, C, JAVA и Node.js [клиент SDKs](https://docs.microsoft.com/azure/iot-dps/).  

## <a name="next-steps"></a>Дальнейшие действия
* Разработка с использованием [пакета SDK для Azure IoT]( https://github.com/Azure/azure-iot-sdks) для Центра Интернета Azure и службы подготовки устройств к добавлению в Центр Интернета вещей
