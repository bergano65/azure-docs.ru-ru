---
title: Как передавать дополнительные данные между устройством и службой подготовки устройств Azure
description: В этом документе описывается, как передавать дополнительные данные между устройством и службой подготовки устройств (DPS).
author: menchi
ms.author: menchi
ms.date: 10/29/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: e9482f7069616d61efb98f66590ce33cfe3cf350
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74974859"
---
# <a name="how-to-transfer-additional-data-between-device-and-dps"></a>Как передавать дополнительные данные между устройством и службой DPS
Иногда DPS требуются дополнительные данные от устройств, чтобы правильно подготовить их для использования надлежащим центром Интернета вещей. Устройство должно предоставить такие данные. Наоборот, служба DPS может возвращать данные на устройство для упрощения логики на стороне клиента. 

## <a name="when-to-use-it"></a>Когда использовать
Эта функция может использоваться в качестве расширения для [пользовательского выделения](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Например, вы хотите выделить устройства на основе модели устройства без вмешательства человека. В этом случае вы будете использовать [пользовательское выделение](https://docs.microsoft.com/azure/iot-dps/how-to-use-custom-allocation-policies). Вы можете настроить устройство для передачи сведений о модели в рамках [вызова регистрации устройства](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice). Служба DPS передаст сведения об устройстве в веб-перехватчик пользовательского выделения. Функция позволяет решить, к какому центру Интернета вещей будет обращаться это устройство при получении сведений о модели устройства. Аналогичным образом, если веб-перехватчику нужно вернуть определенные данные на устройство, он передаст данные обратно в виде строки в ответе.  

## <a name="device-sends-data-to-dps"></a>Устройство отправляет данные в службу DPS
Когда устройство отправляет в службу DPS [вызов регистрации устройства](https://docs.microsoft.com/rest/api/iot-dps/runtimeregistration/registerdevice) , можно улучшить вызов регистрации, чтобы взять другие поля в тексте. Текст выглядит следующим образом: 
   ```
   { 
       “registrationId”: “mydevice”, 
       “tpm”:                
       { 
           “endorsementKey”: “stuff”, 
           “storageRootKey”: “things” 
       }, 
       “interfaces”: “TODO: get how interfaces are reported by devices from PnP folks.”, 
       “data”: “your additional data goes here. It can be nested JSON.” 
    } 
   ```

## <a name="dps-returns-data-to-the-device"></a>Служба DPS возвращает данные на устройство
Если пользовательскому веб-перехватчику политики распределения требуется вернуть на устройство какие-либо данные, он передаст их в виде строки в ответе веб-перехватчика. Изменение представлено в разделе Ретурндата ниже. 
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
        "returnData": "whatever is returned by the webhook" 
    } 
   ```

## <a name="sdk-support"></a>Поддержка пакетов SDK
Эта функция доступна в клиентских пакетах C# [SDK](https://docs.microsoft.com/azure/iot-dps/)для C,, Java и Node. js.  

## <a name="next-steps"></a>Дальнейшие действия
* Разработка с использованием [пакета SDK для Azure IoT]( https://github.com/Azure/azure-iot-sdks) для Центра Интернета Azure и службы подготовки устройств к добавлению в Центр Интернета вещей
