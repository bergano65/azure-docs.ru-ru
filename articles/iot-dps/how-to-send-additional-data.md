---
title: Как передавать полезные данные между устройством и службой подготовки устройств Azure
description: В этом документе описывается передача полезных данных между устройством и службой подготовки устройств (DPS).
author: menchi
ms.author: menchi
ms.date: 02/11/2020
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: de6bb3fe0879a69467283e93a04a355876a02cba
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80246693"
---
# <a name="how-to-transfer-a-payload-between-device-and-dps"></a>Как передавать полезные данные между устройством и службой DPS
Иногда служба DPS требует больше данных от устройств, чтобы правильно подготавливать их в нужный центр Интернета вещей, и эти данные должны предоставляться устройством. Наоборот, служба DPS может возвращать данные на устройство для упрощения логики на стороне клиента. 

## <a name="when-to-use-it"></a>Сценарии использования
Эта функция может использоваться в качестве расширения для [пользовательского выделения](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Например, вы хотите выделить устройства на основе модели устройства без вмешательства человека. В этом случае вы будете использовать [пользовательское выделение](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Вы можете настроить устройство для передачи сведений о модели в рамках [вызова регистрации устройства](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). Служба DPS передаст полезные данные устройства в пользовательский веб-перехватчик выделения. Функция позволяет решить, к какому центру Интернета вещей будет обращаться это устройство при получении сведений о модели устройства. Аналогично, если веб-перехватчику нужно вернуть некоторые данные на устройство, он передаст данные обратно в виде строки в ответе веб-перехватчика.  

## <a name="device-sends-data-payload-to-dps"></a>Устройство отправляет полезные данные в службу DPS
Когда устройство отправляет в службу DPS [вызов регистрации устройства](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) , можно улучшить вызов регистрации, чтобы взять другие поля в тексте. Текст выглядит следующим образом: 
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

## <a name="dps-returns-data-to-the-device"></a>Служба DPS возвращает данные на устройство
Если пользовательскому веб-перехватчику политики распределения требуется вернуть на устройство какие-либо данные, он передаст их в виде строки в ответе веб-перехватчика. Изменение находится в разделе полезных данных ниже. 
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
Эта функция доступна в пакетах [SDK для клиента](https://docs.microsoft.com/azure/iot-dps/)C, C#, JAVA и Node.js.  

## <a name="next-steps"></a>Дальнейшие действия
* Разработка с использованием [пакета SDK для Azure IoT]( https://github.com/Azure/azure-iot-sdks) для Центра Интернета Azure и службы подготовки устройств к добавлению в Центр Интернета вещей
