---
title: Создание веб-перехватчиков на правилах Azure IoT Central | Документация Майкрософт
description: Создание веб-перехватчиков в Azure IoT Central для автоматического уведомления других приложений о срабатывании правил.
author: viv-liu
ms.author: viviali
ms.date: 12/02/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: corywink
ms.openlocfilehash: d97bd7a3c6de92f22a9880040f407960d5257f6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158101"
---
# <a name="create-webhook-actions-on-rules-in-azure-iot-central"></a>Создание действий веб-перехватчика на правилах Azure IoT Central

*Этот раздел предназначен для разработчиков и администраторов.*

Веб-перехватчики позволяют подключить приложение IoT Central к другим приложениям и службам для удаленного мониторинга и уведомления. Веб-перехватчики автоматически уведомляют другие приложения и службы, к которым вы подключаетесь, при каждой активации правила в приложении IoT Central. Приложение IoT Central отправляет запрос POST в конечную точку HTTP другого приложения всякий раз, когда срабатывает правило. Полезная нагрузка содержит детали устройства и детали триггера правила.

## <a name="set-up-the-webhook"></a>Настройка веб-крючка

В этом примере вы подключаетесь к RequestBin, чтобы получать уведомления, когда правила с помощью веб-крючков.

1. Откройте [RequestBin](https://requestbin.net/).

1. Создайте новый RequestBin и скопируйте **Bin URL** (URL-адрес Bin).

1. Создайте [правило телеметрии.](tutorial-create-telemetry-rules.md) Сохраните правило и добавьте новое действие.

    ![Экран создания веб-перехватчика](media/howto-create-webhooks/webhookcreate.png)

1. Выберите действие веб-перехватчика, укажите отображаемое имя и вставьте URL-адрес Bin как URL-адрес обратного вызова.

1. Сохраните правило.

Теперь, когда правило срабатывает, в RequestBin отображается новый запрос.

## <a name="payload"></a>Полезные данные

При срабатывании правила на URL-адрес обратного вызова, содержащий полезную нагрузку json с телеметрией, устройством, правилом и деталями приложения, наносится запрос HTTP POST. Полезная нагрузка может выглядеть следующим образом:

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

Если у вас есть идеи о том, как улучшить эту функцию, разместить свои предложения на нашем [форуме голос пользователя](https://feedback.azure.com/forums/911455-azure-iot-central).

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как настроить и использовать webhooks, предлагаемый следующий шаг заключается в [изучении настройки Azure Monitor Action Groups.](howto-use-action-groups.md)
