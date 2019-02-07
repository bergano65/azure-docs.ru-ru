---
title: Создание веб-перехватчиков на правилах Azure IoT Central | Документация Майкрософт
description: Создание веб-перехватчиков в Azure IoT Central для автоматического уведомления других приложений о срабатывании правил.
author: viv-liu
ms.author: viviali
ms.date: 02/05/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 62599419d5634bea7cd25c93fbada8b472b95c4d
ms.sourcegitcommit: 415742227ba5c3b089f7909aa16e0d8d5418f7fd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55772749"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Создание действий веб-перехватчика на правилах Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

Веб-перехватчики позволяют подключить приложение IoT Central к другим приложениям и службам для удаленного мониторинга и уведомления. Веб-перехватчики автоматически уведомляют другие приложения и службы, к которым вы подключаетесь, при каждой активации правила в приложении IoT Central. Приложение IoT Central отправляет запрос POST к конечной точке HTTP другого приложения при каждой активации правила. Полезные данные будут содержать сведения об устройстве и об активации правила. 

## <a name="how-to-set-up-the-webhook"></a>Настройка веб-перехватчика
В этом примере необходимо подключиться к RequestBin, чтобы с помощью веб-перехватчиков получать уведомления при срабатывании правил. 

1. Откройте [RequestBin](http://requestbin.net/). 
1. Создайте новый RequestBin и скопируйте **Bin URL** (URL-адрес Bin). 
1. Создайте [Правило телеметрии](howto-create-telemetry-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json) или [Правило события](howto-create-event-rules-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json). Сохраните правило и добавьте новое действие.
    ![Экран создания веб-перехватчика](media/howto-create-webhooks-experimental/webhookcreate.png)
1. Выберите действие веб-перехватчика, укажите отображаемое имя и вставьте URL-адрес Bin как URL-адрес обратного вызова. 
1. Сохраните правило.

Теперь при срабатывании правила в RequestBin должен отобразиться новый запрос.

## <a name="payload"></a>Полезные данные
При запуске правила HTTP-запрос POST обрабатывается URL-адресом обратного вызова, содержащим полезные данные json со сведениями измерений, устройства, правила и приложения. Для правила телеметрии полезные данные могут выглядеть следующим образом.

```json
{
    "id": "ID",
    "timestamp": "date-time",
    "device" : {
        "id":"ID",
        "name":  "Refrigerator1",
        "simulated" : true,
        "deviceId": "deviceID",
        "deviceTemplate":{
            "id": "ID",
            "version":"1.0.0"
        },
        "properties":{
            "device":{
                "firmwareversion":"1.0"
            },
            "cloud":{
                "location":"One Microsoft Way"
            }
        },
        "measurements":{
            "telemetry":{
                "temperature":20,
                "pressure":10
            }
        }

    },
    "rule": {
        "id": "ID",
        "name": "High temperature alert",
        "enabled": true,
        "deviceTemplate": {
            "id":"GUID",
            "version":"1.0.0"
        }
    },
    "application": {
        "id": "ID",
        "name": "Contoso app",
        "subdomain":"contoso-app"
    }
}
```

## <a name="known-limitations"></a>Известные ограничения
В настоящее время нет программного метода подписки или отмены подписки на эти веб-перехватчики через API.

Если имеются идеи по улучшению этой функции, опубликуйте свои предложения на [форуме Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Дополнительная информация
Теперь, когда вы узнали, как настроить и использовать веб-перехватчики, ознакомьтесь с [созданием рабочих процессов в Microsoft Flow](howto-add-microsoft-flow-experimental.md?toc=/azure/iot-central-experimental/toc.json&bc=/azure/iot-central-experimental/breadcrumb/toc.json).
