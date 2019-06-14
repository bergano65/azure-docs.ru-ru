---
title: Несколько действий выполнялись из правило Azure IoT Central | Документация Майкрософт
description: Запустите несколько действий из одного правила IoT Central и создать повторно используемых групп действий, которые можно выполнять из нескольких правил.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 03/19/2019
ms.topic: conceptual
ms.service: iot-central
manager: philmea
ms.openlocfilehash: 857d747fa691d1ec2b386d5931a7edea08b7e609
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60517220"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Сгруппировать несколько действий для запуска из одного или нескольких правил

*Эта статья относится к разработчиков и администраторов.*

В Azure IoT Central как создать правила для выполнения действий при выполнении условия. Правила основаны на данных телеметрии устройства или события. Например можно отправят сообщение оператору при температуру устройства превышает пороговое значение. В этой статье описывается использование [Azure Monitor](../azure-monitor/overview.md) *группы действий* для присоединения нескольких действий к правилу IoT Central. Группы действий можно назначить несколько правил. [Группы действий](../azure-monitor/platform/action-groups.md) — это коллекция настройки уведомлений, определенные владельцем подписки Azure.

## <a name="prerequisites"></a>Технические условия

- приложение с оплатой по мере использования;
- Учетная запись Azure и подписки для создания и управления группы действий Azure Monitor

## <a name="create-action-groups"></a>Создание групп действий

Вы можете [создания групп действий на портале Azure и управления](../azure-monitor/platform/action-groups.md) или [шаблона Azure Resource Manager](../azure-monitor/platform/action-groups-create-resource-manager-template.md).

Группы действий можно:

- Отправлять уведомления, такие как сообщения электронной почты, SMS-сообщение, или совершить голосовой вызов.
- Выполните действие, например вызов веб-перехватчика.

На следующем снимке экрана показан группу действий, которое отправляет сообщения электронной почты и SMS-уведомлений и вызывает веб-перехватчика:

![Группа действий](media/howto-use-action-groups/actiongroup.png)

Чтобы использовать группы действий в правиле IoT Central, группы действий необходимо в той же подписке Azure, как приложение IoT Central.

## <a name="use-an-action-group"></a>Используйте группу действий

Чтобы использовать группы действий в приложении IoT Central, необходимо сначала создайте правило телеметрии или события. При добавлении действия в правило, выберите **группы действий Azure Monitor**:

![Выбор действия](media/howto-use-action-groups/chooseaction.png)

Выберите группу действий из подписки Azure:

![Выберите группу действий](media/howto-use-action-groups/chooseactiongroup.png)

Щелкните **Сохранить**. Группа действий будет отображаться в списке действий для выполнения при запуске правила:

![Сохранить группу действий](media/howto-use-action-groups/savedactiongroup.png)

В следующей таблице перечислены сведения, передаваемые в поддерживаемых типах:

| Тип действия | Формат выходных данных |
| ----------- | -------------- |
| Email       | Стандартный шаблон сообщения электронной почты IoT Central |
| SMS         | Оповещение Azure IoT Central: ${applicationName} - «${ruleName}» в «${deviceName}» $ ${triggerDate} {triggerTime} |
| Голосовая связь       | Оповещения Azure I.O.T Central: правило «${ruleName}» запущено на устройстве «${deviceName}» $ ${triggerDate} {triggerTime}, из приложений ${applicationName} |
| webhook     | { "schemaId" : «AzureIoTCentralRuleWebhook», «данные»: {[полезные данные webhook регулярных](#payload)}} |

Ниже приведен пример сообщения SMS от группы действий.

`iotcentral: Azure IoT Central alert: Sample Contoso 22xu4spxjve - "Low pressure alert" triggered on "Refrigerator 2" at March 20, 2019 10:12 UTC`

<a id="payload"></a> Приведенный ниже код JSON показано приведен пример веб-перехватчика действие полезных данных.

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

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать группы действий с правилами, мы предлагаем — Узнайте, как [управления устройствами](howto-manage-devices.md).
