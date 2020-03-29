---
title: Получение оповещений журнала действий для уведомлений службы Azure
description: Получайте уведомления по SMS, электронной почте или от веб-перехватчика при использовании службы Azure.
ms.topic: conceptual
ms.date: 06/27/2019
ms.openlocfilehash: d318adc76959ac24f4be9946167965a83053f632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75749306"
---
# <a name="create-activity-log-alerts-on-service-notifications"></a>Создание оповещений журнала действий для уведомлений службы
## <a name="overview"></a>Обзор

В этой статье показано, как настроить оповещения журнала действий для уведомлений о работоспособности службы с помощью портала Azure.  

Уведомления о работоспособности службы хранятся в [журнале действий Azure](../azure-monitor/platform/platform-logs-overview.md) С учетом возможного большого объема информации, хранящейся в журнале активности, существует отдельный пользовательский интерфейс, который упрощает просмотр и настройку оповещений в уведомлениях о работоспособности службы. 

Оповещения можно получать, когда Azure отправляет уведомления о работоспособности службы в вашу подписку Azure. Оповещения можно настроить на основе следующих данных.

- Класс уведомления о работоспособности службы (проблемы со службой, запланированное обслуживание, рекомендации по работоспособности).
- Затронутая подписка.
- Затронутые службы.
- Затронутые регионы.

> [!NOTE]
> Уведомления о работоспособности службы не содержат предупреждения о событиях работоспособности ресурсов.

Кроме того, можно настроить, кому должны отправляться оповещения:

- Выберите имеющуюся группу действий.
- Создайте группу действий (которую можно будет использовать для будущих оповещений).

Чтобы узнать больше о группах действий, [см.](../azure-monitor/platform/action-groups.md)

Дополнительные сведения о том, как настроить уведомления о работоспособности службы с помощью шаблонов Resource Manager Azure, см. в статье [Создание оповещения журнала действий с помощью шаблона Resource Manager](../azure-monitor/platform/alerts-activity-log.md).

### <a name="watch-a-video-on-setting-up-your-first-azure-service-health-alert"></a>Посмотрите видео о настройке первого оповещения о здоровье службы Azure

>[!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE2OaXt]

## <a name="alert-and-new-action-group-using-azure-portal"></a>Создание оповещения и новой группы действий с помощью портала Azure
1. На [портале](https://portal.azure.com)выберите **Работоспособность службы**.

    ![Служба "Работоспособность службы"](media/alerts-activity-log-service-notifications/home-servicehealth.png)

1. В разделе **Оповещения** выберите **Оповещения о работоспособности**.

    ![Вкладка "Оповещения о работоспособности"](media/alerts-activity-log-service-notifications/alerts-blades-sh.png)

1. Выберите **Create service health alert** (Создать оповещение о работоспособности службы) и заполните поля.

    ![Команда Create service health alert (Создать оповещение о работоспособности службы)](media/alerts-activity-log-service-notifications/service-health-alert.png)

1. Выберите **Подписки**, **Службы** и **Области**, чтобы получать оповещения.

    ![Диалоговое окно Add activity log alert (Добавить оповещение журнала действий)](media/alerts-activity-log-service-notifications/activity-log-alert-new-ux.png)

    > [!NOTE]
    > Эта подписка используется для сохранения оповещения журнала действий. В эту подписку развертывается ресурс оповещения, а также с ее помощью отслеживаются связанные с ним события в журнале действий.

1. Выберите **Типы событий** для получения оповещений: *Неполадка службы*, *Плановое обслуживание* и *Рекомендации по работоспособности* 

1. Определите подробности своих оповещений, введя **Имя правила оповещения** и его **Описание**.

1. Выберите **группу ресурсов** для хранения оповещений.

1. Чтобы создать новую группу действий, нажмите **Новая группа действий**. Введите имя в поля **Имя группы действий** и **Короткое имя**. Короткое имя используется в уведомлениях, которые отправляются при выводе оповещения.

    ![Создание новой группы действий](media/alerts-activity-log-service-notifications/action-group-creation.png)

1. Определите список получателей, указав данные получателя:

    а. **Имя.** Введите имя, псевдоним или идентификатор получателя.

    b. **Тип действия.** Вы можете выбрать SMS, электронную почту, веб-перехватчик, приложение Azure и другие типы.

    c. **Подробные сведения.** Сведения зависят от типа действия. Можно указать номер телефона, адрес электронной почты, универсальный код ресурса (URI) веб-перехватчика и др.

1. Чтобы создать группу действий, выберите **ОК**, а чтобы завершить оповещение — **Создать правило оповещения**.

В течение нескольких минут оповещение активируется на основе указанных во время создания условий.

Узнайте, [как настроить уведомления веб-перехватчика для существующих систем управления проблемами](service-health-alert-webhook-guide.md). Сведения о схеме веб-перехватчика для оповещений журнала действий см. в статье [Объекты webhook для оповещений журнала действий Azure](../azure-monitor/platform/activity-log-alerts-webhook.md).

>[!NOTE]
>Группу действий, определенную при выполнении этих шагов, можно использовать повторно в качестве имеющейся группы действий для всех будущих определений оповещений.
>

## <a name="alert-with-existing-action-group-using-azure-portal"></a>Создание оповещения с использованием существующей группы действий с помощью портала Azure

1. Следуйте шагам от 1 до 6 в предыдущем разделе, чтобы создать уведомление о работоспособности службы. 

1. В разделе **Определение группы действий** нажмите кнопку **Выбрать группу действий **. Выберите необходимую группу действий.

1. Чтобы добавить группу действий, выберите **Добавить**, а чтобы завершить оповещение — **Создать правило генерации оповещений**.

В течение нескольких минут оповещение активируется на основе указанных во время создания условий.

## <a name="alert-and-new-action-group-using-the-azure-resource-manager-templates"></a>Создание оповещения и новой группы действий с помощью шаблонов Azure Resource Manager

Ниже приведен пример кода, который создает группу действий с целевым объектом электронной почты и активирует все уведомления о работоспособности службы в целевой подписке.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "actionGroups_name": {
            "defaultValue": "SubHealth",
            "type": "String"
        },
        "activityLogAlerts_name": {
            "defaultValue": "ServiceHealthActivityLogAlert",
            "type": "String"
        },
        "emailAddress":{
            "type":"string"
        }
    },
    "variables": {
        "alertScope":"[concat('/','subscriptions','/',subscription().subscriptionId)]"
    },
    "resources": [
        {
            "comments": "Action Group",
            "type": "microsoft.insights/actionGroups",
            "name": "[parameters('actionGroups_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "groupShortName": "[parameters('actionGroups_name')]",
                "enabled": true,
                "emailReceivers": [
                    {
                        "name": "[parameters('actionGroups_name')]",
                        "emailAddress": "[parameters('emailAddress')]"
                    }
                ],
                "smsReceivers": [],
                "webhookReceivers": []
            },
            "dependsOn": []
        },
        {
            "comments": "Service Health Activity Log Alert",
            "type": "microsoft.insights/activityLogAlerts",
            "name": "[parameters('activityLogAlerts_name')]",
            "apiVersion": "2017-04-01",
            "location": "Global",
            "tags": {},
            "scale": null,
            "properties": {
                "scopes": [
                    "[variables('alertScope')]"
                ],
                "condition": {
                    "allOf": [
                        {
                            "field": "category",
                            "equals": "ServiceHealth"
                        },
                        {
                            "field": "properties.incidentType",
                            "equals": "Incident"
                        }
                    ]
                },
                "actions": {
                    "actionGroups": [
                        {
                            "actionGroupId": "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]",
                            "webhookProperties": {}
                        }
                    ]
                },
                "enabled": true,
                "description": ""
            },
            "dependsOn": [
                "[resourceId('microsoft.insights/actionGroups', parameters('actionGroups_name'))]"
            ]
        }
    ]
}
```

## <a name="manage-your-alerts"></a>Управление оповещениями

Созданное оповещение отображается в разделе **Оповещения** в окне **Монитор**. Выберите оповещение, которым нужно управлять, чтобы получить возможность:

* Изменить его.
* Удалить его.
* Отключить и включить его, если нужно временно остановить или возобновить получение уведомлений для этого оповещения.

## <a name="next-steps"></a>Дальнейшие действия
- Узнайте о [рекомендациях по настройке предупреждений о здоровье службы Azure.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUa)
- Узнайте, как [настроить мобильные push-уведомления для службы Azure Health.](https://www.microsoft.com/en-us/videoplayer/embed/RE2OtUw)
- Узнайте, как [настроить уведомления webhook для существующих систем управления проблемами.](service-health-alert-webhook-guide.md)
- Узнайте об [уведомлениях службы о работоспособности](service-notifications.md).
- Узнайте об [ограничении скорости уведомлений](../azure-monitor/platform/alerts-rate-limiting.md).
- Просмотрите [схему оповещения о журнале активности webhook.](../azure-monitor/platform/activity-log-alerts-webhook.md)
- Изучите [обзор оповещений журнала действий](../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.
- Подробнее о [группах действий.](../azure-monitor/platform/action-groups.md)
