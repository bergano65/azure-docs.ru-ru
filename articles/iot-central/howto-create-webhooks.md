---
title: Создание веб-перехватчиков на правилах Azure IoT Central | Документация Майкрософт
description: Создание веб-перехватчиков в Azure IoT Central для автоматического уведомления других приложений о срабатывании правил.
author: viv-liu
ms.author: viviali
ms.date: 06/16/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 508e8b4b3a909e87f538f67b1ad9a5efdbcd9551
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876024"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Создание действий веб-перехватчика на правилах Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

Веб-перехватчики позволяют подключить приложение IoT Central к другим приложениям и службам для удаленного мониторинга и уведомления. Веб-перехватчики автоматически уведомляют другие приложения и службы, к которым вы подключаетесь, при каждой активации правила в приложении IoT Central. Приложение IoT Central отправляет запрос POST в конечную точку HTTP другого приложения при каждом запуске правила. Полезные данные содержат сведения об устройстве и сведения о триггере правила.

## <a name="set-up-the-webhook"></a>Настройка веб-перехватчика

В этом примере вы подключаетесь к RequestBin, чтобы получать уведомления при срабатывании правил с помощью веб-перехватчиков.

1. Откройте [RequestBin](https://requestbin.net/).

1. Создайте новый RequestBin и скопируйте **Bin URL** (URL-адрес Bin).

1. Создайте [Правило телеметрии](howto-create-telemetry-rules.md) или [Правило события](howto-create-event-rules.md). Сохраните правило и добавьте новое действие.

    ![Экран создания веб-перехватчика](media/howto-create-webhooks/webhookcreate.png)

1. Выберите действие веб-перехватчика, укажите отображаемое имя и вставьте URL-адрес Bin как URL-адрес обратного вызова.

1. Сохраните правило.

Теперь, когда правило будет активировано, вы увидите, что в RequestBin появится новый запрос.

## <a name="payload"></a>Полезные данные

При запуске правила HTTP-запрос POST обрабатывается URL-адресом обратного вызова, содержащим полезные данные json со сведениями измерений, устройства, правила и приложения. Для правила телеметрии полезная нагрузка выглядит следующим образом:

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

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как настраивать и использовать веб-перехватчики, предлагаем следующий шаг — изучение [процесса создания рабочих процессов в Microsoft Flow](howto-add-microsoft-flow.md).
