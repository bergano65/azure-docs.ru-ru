---
title: Выполнить несколько действий из центрального правила Azure IoT (ru) Документы Майкрософт
description: Выполнить несколько действий из одного правила IoT Central и создать многоразовые группы действий, которые можно выполнить из нескольких правил.
services: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/06/2019
ms.topic: how-to
ms.service: iot-central
manager: philmea
ms.openlocfilehash: b447f44d0c95693e560fd5bbfbff8c8daeec964e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80157693"
---
# <a name="group-multiple-actions-to-run-from-one-or-more-rules"></a>Группа нескольких действий для выполнения одного или нескольких правил

*Эта статья относится к строителям и администраторам.*

В Azure IoT Central вы создаете правила для выполнения действий при выполнении состояния. Правила основаны на телеметрии устройства или событиях. Например, можно уведомить оператора, когда температура устройства превышает пороговую. В этой статье описывается, как использовать *группы действий* [Azure Monitor](../../azure-monitor/overview.md) для прикрепления нескольких действий к правилу IoT Central. Можно прикрепить группу действий к нескольким правилам. [Группа действий](../../azure-monitor/platform/action-groups.md) представляет собой набор предпочтений уведомлений, определенных владельцем подписки Azure.

## <a name="prerequisites"></a>Предварительные требования

- Приложение, созданное с использованием стандартного плана ценообразования
- Учетная запись и подписка Azure для создания и управления группами действий Azure Monitor

## <a name="create-action-groups"></a>Создание групп действий

Вы можете [создавать и управлять группами действий на портале Azure](../../azure-monitor/platform/action-groups.md) или с [помощью шаблона управления ресурсами Azure.](../../azure-monitor/platform/action-groups-create-resource-manager-template.md)

Группа действий может:

- Отправить уведомления, такие как электронная почта, SMS, или сделать голосовой вызов.
- Выполнить действие, такое как вызов веб-крючка.

На следующем скриншоте показана группа действий, которая отправляет уведомления по электронной почте и SMS и вызывает веб-крюк:

![Группа действий](media/howto-use-action-groups/actiongroup.png)

Чтобы использовать группу действий в правилах IoT Central, группа действий должна находиться в той же подписке Azure, что и приложение IoT Central.

## <a name="use-an-action-group"></a>Использование группы действий

Чтобы использовать группу действий в приложении IoT Central, сначала создайте правило. При добавлении действия в правило выберите **группы действий Azure Monitor:**

![Выбор действия](media/howto-use-action-groups/chooseaction.png)

Выберите группу действий из подписки Azure:

![Выберите группу действий](media/howto-use-action-groups/chooseactiongroup.png)

Нажмите кнопку **Сохранить**. Группа действий теперь отображается в списке действий, которые необходимо выполнить при срабатывании правила:

![Сохраненная группа действий](media/howto-use-action-groups/savedactiongroup.png)

В следующей таблице кратко излагается информация, отправленная поддерживаемым типам действий:

| Тип действия | Формат вывода |
| ----------- | -------------- |
| Email       | Стандартный шаблон электронной почты IoT Central |
| SMS         | Оповещение Azure IoT Центральное: $»ApplicationName» - «$»правилоName» срабатывает на «$»deviceName» на $»triggerDate ».» |
| Голосовая связь       | Центральное оповещение Azure I.O.T: правило "$'ruleName" срабатывает на устройстве "$'deviceName" на $(triggerDate) $(triggerTime), в приложении $'applicationName |
| webhook     | "schemaId" : "AzureIoTCentralRuleWebhook", "данные":[regular webhook payload](howto-create-webhooks.md#payload) |

Следующий текст представляет собой пример SMS-сообщения из группы действий:

`iotcentral: Azure IoT Central alert: Contoso - "Low pressure alert" triggered on "Motion sensor 2" at March 20, 2019 10:12 UTC`

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы узнали, как использовать группы действий с правилами, следующий шаг, чтобы узнать, как [управлять устройствами.](howto-manage-devices.md)
