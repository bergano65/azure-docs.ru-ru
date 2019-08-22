---
title: Выполнение нескольких действий из правила IoT Central Azure | Документация Майкрософт
description: Выполнение нескольких действий из одного правила IoT Central и создание многократно используемых групп действий, которые можно запускать из нескольких правил.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 07/10/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: ad5f660ff72eceecbb6db2e9557b023ed2c6ea99
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69875815"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Группирование нескольких действий для запуска из одного или нескольких правил

*Эта статья относится к сборщикам и администраторам.*

[!INCLUDE [iot-central-original-pnp](../../includes/iot-central-original-pnp-note.md)]

В IoT Central Azure вы создаете правила для выполнения действий при выполнении условия. Правила основаны на телеметрии или событиях устройства. Например, можно уведомить оператора, если температура на устройстве превышает пороговое значение. В этой статье описывается, как использовать *группы действий* [Azure Monitor](../azure-monitor/overview.md) для присоединения нескольких действий к правилу IOT Central. Группу действий можно подключить к нескольким правилам. [Группа действий](../azure-monitor/platform/action-groups.md) — это набор параметров уведомлений, определенных владельцем подписки Azure.

## <a name="prerequisites"></a>Предварительные требования

- приложение с оплатой по мере использования;
- Учетная запись Azure и подписка для создания групп действий Azure Monitor и управления ими

## <a name="create-action-groups"></a>Создание групп действий

Вы можете [создавать группы действий и управлять ими в портал Azure](../azure-monitor/platform/action-groups.md) или с помощью [шаблона Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Группа действий может:

- Отправка уведомлений, таких как электронная почта, SMS или звонок.
- Выполните действие, например вызов веб-перехватчика.

На следующем снимке экрана показана группа действий, которая отправляет сообщения электронной почты и SMS и вызывает веб-перехватчик:

![Группа действий](media/howto-use-action-groups/actiongroup.png)

Чтобы использовать группу действий в правиле IoT Central, Группа действий должна находиться в той же подписке Azure, что и приложение IoT Central.

## <a name="use-an-action-group"></a>Использование группы действий

Чтобы использовать группу действий в приложении IoT Central, сначала создайте данные телеметрии или правила событий. При добавлении действия в правило выберите **Azure Monitor группы действий**:

![Выбор действия](media/howto-use-action-groups/chooseaction.png)

Выберите группу действий из подписки Azure:

![Выбор группы действий](media/howto-use-action-groups/chooseactiongroup.png)

Щелкните **Сохранить**. Группа действий теперь отображается в списке действий, выполняемых при активации правила.

![Группа действий сохранена](media/howto-use-action-groups/savedactiongroup.png)

В следующей таблице представлены сведения, передаваемые в поддерживаемые типы действий.

| Тип действия | Формат выходных данных |
| ----------- | -------------- |
| Адрес эл. почты       | Шаблон электронной почты "Стандартный IoT Central" |
| SMS         | Оповещение Azure IoT Central: $ {applicationName}-"$ {ruleName}" активировано на "$ {deviceName}" в $ {Тригжердате} $ {Тригжертиме} |
| Голос       | Основное оповещение Azure I. O. T: правило "$ {ruleName}", запущенное на устройстве "$ {deviceName}" в $ {Тригжердате} $ {Тригжертиме}, в приложении $ {applicationName} |
| Веб-перехватчик     | {"Счемаид": "Азуреиотцентралрулевебхук", "данные": {[обычные полезные данные веб](#payload)-перехватчика}} |

Следующий текст представляет собой пример SMS-сообщения из группы действий:

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a>В следующем примере JSON показаны полезные данные действия веб-перехватчика:

```json
{
  "schemaId":"AzureIoTCentralRuleWebhook",
  "data":{
    "id":"97ae27c4-17c5-4e13-9248-65c7a2c57a1b",
    "timestamp":"2019-03-20T10:53:17.059Z",
    "rule":{
      "id":"031b660e-528d-47bb-b33d-f1158d7e31bf",
      "name":"Low pressure alert",
      "enabled":true,
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      }
    },
    "device":{
      "id":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "name":"Refrigerator 2",
      "simulated":true,
      "deviceId":"2383d8ba-c98c-403a-b4d5-8963859643bb",
      "deviceTemplate":{
        "id":"c318d580-39fc-4aca-b995-843719821049",
        "version":"1.0.0"
      },
      "measurements":{
        "telemetry":{
           "pressure":343.269190673549
        }
      }
    },
    "application":{
      "id":"8e70742b-0d5c-4a1d-84f1-4dfd42e61c7b",
      "name":"Sample Contoso",
      "subdomain":"sample-contoso"
    }
  }
}
```

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как использовать группы действий с правилами, предлагаем следующий шаг — Узнайте, как [управлять устройствами](howto-manage-devices.md).
