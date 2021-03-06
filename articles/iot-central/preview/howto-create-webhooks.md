---
title: Создание веб-перехватчиков на правилах Azure IoT Central | Документация Майкрософт
description: Создание веб-перехватчиков в Azure IoT Central для автоматического уведомления других приложений о срабатывании правил.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: 206c93c291654d22e945c3064e3c581dceb50cea
ms.sourcegitcommit: 8bd85510aee664d40614655d0ff714f61e6cd328
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/06/2019
ms.locfileid: "74895475"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central-preview-features"></a>Создание действий веб-перехватчика для правил в IoT Central Azure (Предварительная версия функций)

*Этот раздел предназначен для разработчиков и администраторов.*

Веб-перехватчики позволяют подключить приложение IoT Central к другим приложениям и службам для удаленного мониторинга и уведомления. Веб-перехватчики автоматически уведомляют другие приложения и службы, к которым вы подключаетесь, при каждой активации правила в приложении IoT Central. Приложение IoT Central отправляет запрос POST в конечную точку HTTP другого приложения при каждом запуске правила. Полезные данные содержат сведения об устройстве и сведения о триггере правила.

## <a name="set-up-the-webhook"></a>Настройка веб-перехватчика

В этом примере вы подключаетесь к RequestBin, чтобы получать уведомления при срабатывании правил с помощью веб-перехватчиков.

1. Откройте [RequestBin](https://requestbin.net/).

1. Создайте новый RequestBin и скопируйте **Bin URL** (URL-адрес Bin).

1. Создайте [правило телеметрии](tutorial-create-telemetry-rules.md). Сохраните правило и добавьте новое действие.

    ![Экран создания веб-перехватчика](media/howto-create-webhooks/webhookcreate.png)

1. Выберите действие веб-перехватчика, укажите отображаемое имя и вставьте URL-адрес Bin как URL-адрес обратного вызова.

1. Сохраните правило.

Теперь, когда правило будет активировано, вы увидите, что в RequestBin появится новый запрос.

## <a name="payload"></a>Полезные данные

При активации правила запрос HTTP POST выполняется в URL-адрес обратного вызова, содержащий полезные данные JSON с данными телеметрии, устройством, правилом и приложением. Полезная нагрузка может выглядеть следующим образом:

```json
{
    "id": "<id>",
    "displayName": "Webhook 1",
    "timestamp": "2019-10-24T18:27:13.538Z",
    "rule": {
        "id": "<id>",
        "displayName": "High temp alert",
        "enabled": true
    },
    "device": {
        "id": "mx1",
        "displayName": "MXChip IoT DevKit - mx1",
        "instanceOf": "<device-template-id>",
        "simulated": true,
        "provisioned": true,
        "approved": true
    },
    "data": [{
        "@id": "<id>",
        "@type": ["Telemetry"],
        "name": "temperature",
        "displayName": "Temperature",
        "value": 66.27310467496761,
        "interfaceInstanceName": "sensors"
    }],
    "application": {
        "id": "<id>",
        "displayName": "x - Store Analytics Checkout---PnP",
        "subdomain": "<subdomain>",
        "host": "<host>"
    }
}
```

## <a name="known-limitations"></a>Известные ограничения

В настоящее время нет программного метода подписки или отмены подписки на эти веб-перехватчики через API.

Если имеются идеи по улучшению этой функции, опубликуйте свои предложения на [форуме Uservoice](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как настраивать и использовать веб-перехватчики, предлагаем следующий шаг: изучение [настройки групп действий Azure Monitor](howto-use-action-groups.md).
