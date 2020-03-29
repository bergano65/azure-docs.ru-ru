---
title: Как активировать сложные действия с помощью оповещений Azure Monitor
description: Сведения о том, как создать действие приложения логики для обработки оповещений Azure Monitor.
author: dkamstra
ms.author: dukek
ms.topic: conceptual
ms.date: 07/18/2018
ms.subservice: alerts
ms.openlocfilehash: 655a3acc44a1418778b37fbef85e5df75d042317
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78206242"
---
# <a name="how-to-trigger-complex-actions-with-azure-monitor-alerts"></a>Как активировать сложные действия с помощью оповещений Azure Monitor

Эта статья описывает, как настроить и активировать приложение логики, чтобы создать беседу в Microsoft Teams при срабатывании оповещения.

## <a name="overview"></a>Обзор

Когда активируется оповещение Azure Monitor, оно вызывает [группу действий](../../azure-monitor/platform/action-groups.md). Группы действий позволяют активировать одно или несколько действий для уведомления других лиц об оповещении и устранения его причины.

Общий процесс приведен ниже:

-   Создание приложения логики для соответствующего типа оповещений.

-   Импортируйте пример полезной нагрузки для соответствующего типа оповещения в логическое приложение.

-   Определение поведения приложения логики.

-   Копирование конечной точки HTTP приложения логики в группу действий Azure.

Если требуется, чтобы приложение логики выполняло другое действие, процесс настройки аналогичен.

## <a name="create-an-activity-log-alert-administrative"></a>Создание оповещения журнала действий на портале администрирования

1.  В верхнем левом углу портала Azure выберите **Создать ресурс**.

2.  Найдите и выберите элемент **Приложение логики** и **Создать**.

3.  Присвойте **Имя** приложению логики, выберите значение **Группа ресурсов** и т. д.

    ![Создание приложения логики](media/action-groups-logic-app/create-logic-app-dialog.png "Создайте приложение логики")

4.  Выберите **Создать**, чтобы создать приложение логики. Отображается всплывающее сообщение, указывающее на создание приложения логики. Выберите **Launch Resource** (Запустить ресурс), чтобы открыть **Конструктор Logic Apps**.

5.  Выберите триггер **При получении HTTP-запроса**.

    ![Триггеры приложений логики](media/action-groups-logic-app/logic-app-triggers.png "Триггеры приложений логики")

6.  Выберите **Изменить**, чтобы изменить триггер HTTP-запроса.

    ![Триггеры запроса HTTP](media/action-groups-logic-app/http-request-trigger-shape.png "Триггеры запроса HTTP")

7.  Выберите **Использовать пример полезной нагрузки, чтобы создать схему**.

    ![Использование примера полезной нагрузки](media/action-groups-logic-app/use-sample-payload-button.png "Использование примера полезной нагрузки")

8.  Копирование и вставьте следующий образец полезной нагрузки в диалоговую коробку:

    ```json
        {
            "schemaId": "Microsoft.Insights/activityLogs",
            "data": {
                "status": "Activated",
                "context": {
                "activityLog": {
                    "authorization": {
                    "action": "microsoft.insights/activityLogAlerts/write",
                    "scope": "/subscriptions/…"
                    },
                    "channels": "Operation",
                    "claims": "…",
                    "caller": "logicappdemo@contoso.com",
                    "correlationId": "91ad2bac-1afa-4932-a2ce-2f8efd6765a3",
                    "description": "",
                    "eventSource": "Administrative",
                    "eventTimestamp": "2018-04-03T22:33:11.762469+00:00",
                    "eventDataId": "ec74c4a2-d7ae-48c3-a4d0-2684a1611ca0",
                    "level": "Informational",
                    "operationName": "microsoft.insights/activityLogAlerts/write",
                    "operationId": "61f59fc8-1442-4c74-9f5f-937392a9723c",
                    "resourceId": "/subscriptions/…",
                    "resourceGroupName": "LOGICAPP-DEMO",
                    "resourceProviderName": "microsoft.insights",
                    "status": "Succeeded",
                    "subStatus": "",
                    "subscriptionId": "…",
                    "submissionTimestamp": "2018-04-03T22:33:36.1068742+00:00",
                    "resourceType": "microsoft.insights/activityLogAlerts"
                }
                },
                "properties": {}
            }
        }
    ```

9. Отображается всплывающее окно **Конструктор приложений логики** с напоминанием о том, что запрос, отправляемый в приложение логики, должен задавать в заголовке **Content-Type** значение **application/json**. Закройте всплывающее окно. Оповещение Azure Monitor задает заголовок.

    ![Установите заголовок content-Type](media/action-groups-logic-app/content-type-header.png "Установите заголовок content-Type")

10. Выберите **+** **новый шаг,** а затем выберите **Добавить действие.**

    ![Добавление действия](media/action-groups-logic-app/add-action.png "Добавление действия")

11. Найдите и выберите соединитель Microsoft Teams. Выберите **команды Майкрософт - Опубликовать сообщение.**

    ![Действия групп майкрософт](media/action-groups-logic-app/microsoft-teams-actions.png "Действия групп майкрософт")

12. Настройте действие Microsoft Teams. **Конструктор Logic Apps** предлагает пройти проверку подлинности для учетной записи Office 365. Выберите значения параметров **Идентификатор команды** и **Идентификатор канала** для отправки сообщения.

13. Настройте сообщение, указав статический текст и ссылки на \<поля\> динамического содержимого. Скопируйте следующий текст и вставьте его в поле **Сообщение**:

    ```text
      Activity Log Alert: <eventSource>
      operationName: <operationName>
      status: <status>
      resourceId: <resourceId>
    ```

    Найдите и замените \<поля\> тегами динамического содержимого с такими же именами.

    > [!NOTE]
    > Имеются два динамических поля с именем **status**. Добавьте оба эти поля в сообщение. Используйте поле в контейнере свойств **activityLog** и удалите другое. Наведите указатель мыши на поле **status**, чтобы отобразить подсказку с полным именем поля, как показано на следующем снимке экрана:

    ![Действия команд Майкрософт: Опубликовать сообщение](media/action-groups-logic-app/teams-action-post-message.png "Действия команд Майкрософт: Опубликовать сообщение")

14. В верхней части окна **Конструктор Logic Apps** выберите **Сохранить**, чтобы сохранить приложение логики.

15. Откройте существующую группу действий и добавьте в нее действие, которое ссылается на приложение логики. Если у вас нет существующей группы действий, [см. Создать и управлять группами действий на портале Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups) для ее создания. Обязательно сохраните изменения.

    ![Обновление группы действий](media/action-groups-logic-app/update-action-group.png "Обновление группы действий")

При следующем вызове группы действий оповещением будет вызвано ваше приложение логики.

## <a name="create-a-service-health-alert"></a>Создание оповещения о работоспособности служб

Записи работоспособности служб Azure являются частью журнала действий. Процедура создания оповещения похожа на [создание предупреждения журнала действий](#create-an-activity-log-alert-administrative), но с некоторыми отличиями:

- Шаги с 1 по 7 остаются без изменений.
- Для шага 8 используйте следующую полезную нагрузку образца для триггера запроса HTTP:

    ```json
    {
        "schemaId": "Microsoft.Insights/activityLogs",
        "data": {
            "status": "Activated",
            "context": {
                "activityLog": {
                    "channels": "Admin",
                    "correlationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "description": "…",
                    "eventSource": "ServiceHealth",
                    "eventTimestamp": "2018-04-03T22:44:43.7467716+00:00",
                    "eventDataId": "9ce152f5-d435-ee31-2dce-104228486a6d",
                    "level": "Informational",
                    "operationName": "Microsoft.ServiceHealth/incident/action",
                    "operationId": "e416ed3c-8874-4ec8-bc6b-54e3c92a24d4",
                    "properties": {
                        "title": "...",
                        "service": "...",
                        "region": "Global",
                        "communication": "...",
                        "incidentType": "Incident",
                        "trackingId": "...",
                        "impactStartTime": "2018-03-22T21:40:00.0000000Z",
                        "impactMitigationTime": "2018-03-22T21:41:00.0000000Z",
                        "impactedServices": "[{"ImpactedRegions"}]",
                        "defaultLanguageTitle": "...",
                        "defaultLanguageContent": "...",
                        "stage": "Active",
                        "communicationId": "11000001466525",
                        "version": "0.1.1"
                    },
                    "status": "Active",
                    "subscriptionId": "...",
                    "submissionTimestamp": "2018-04-03T22:44:50.8013523+00:00"
                }
            },
            "properties": {}
        }
    }
    ```

-  Шаги 9 и 10 остаются без изменений.
-  В шагах с 11 по 14 используйте следующий процесс:

   1. Выберите **+** **новый шаг,** а затем выберите **Добавить условие.** Задайте следующие условия, чтобы приложение логики выполнялось, только когда входные данные соответствуют указанным ниже значениям.  При вводе значения версии в текстовое поле поместите его в кавычки ("0.1.1"), чтобы гарантировать, что оно будет вычисляться как строка, а не числовой тип.  Система не отображает кавычки, если вы вернетесь к странице, однако базовый код сохраняет строковый тип.   
       - `schemaId == Microsoft.Insights/activityLogs`
       - `eventSource == ServiceHealth`
       - `version == "0.1.1"`

      !["Состояние полезной нагрузки службы здравоохранения"](media/action-groups-logic-app/service-health-payload-condition.png "Состояние полезной нагрузки Service Health")

   1. В условии **Если истинно** выполните инструкции для шагов с 11 по 13 из раздела [Создание оповещения журнала действий](#create-an-activity-log-alert-administrative), чтобы добавить действие Microsoft Teams.

   1. Определите сообщение, используя сочетание кода HTML и динамического содержимого. Скопируйте приведенный ниже текст и вставьте его в поле **Сообщение**. Замените поля `[incidentType]`, `[trackingID]`, `[title]` и `[communication]` тегами динамического содержимого с таким же именем:

       ```html
       <p>
       <b>Alert Type:</b>&nbsp;<strong>[incidentType]</strong>&nbsp;
       <strong>Tracking ID:</strong>&nbsp;[trackingId]&nbsp;
       <strong>Title:</strong>&nbsp;[title]</p>
       <p>
       <a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.</a>
       </p>
       <p>[communication]</p>
       ```

       !["Служба Здравоохранения истинное состояние после действия"](media/action-groups-logic-app/service-health-true-condition-post-action.png "Служба Здоровье истинное состояние после действия")

   1. Для условия **Если ложно** введите имеющее смысл сообщение:

       ```html
       <p><strong>Service Health Alert</strong></p>
       <p><b>Unrecognized alert schema</b></p>
       <p><a href="https://ms.portal.azure.com/#blade/Microsoft_Azure_Health/AzureHealthBrowseBlade/serviceIssues">For details, log in to the Azure Service Health dashboard.\</a></p>
       ```

       !["Служба Здравоохранения ложное состояние после действия"](media/action-groups-logic-app/service-health-false-condition-post-action.png "Служба Здоровье ложное состояние после действия")

- Шаг 15 остается без изменений. Следуйте инструкциям, чтобы сохранить приложение логики и обновить группу действий.

## <a name="create-a-metric-alert"></a>Создание оповещения для метрики

Процедура создания оповещения для метрики похожа на [создание предупреждения для журнала действий](#create-an-activity-log-alert-administrative), но с некоторыми отличиями:

- Шаги с 1 по 7 остаются без изменений.
- Для шага 8 используйте следующую полезную нагрузку образца для триггера запроса HTTP:

    ```json
    {
    "schemaId": "AzureMonitorMetricAlert",
    "data": {
        "version": "2.0",
        "status": "Activated",
        "context": {
        "timestamp": "2018-04-09T19:00:07.7461615Z",
        "id": "...",
        "name": "TEST-VM CPU Utilization",
        "description": "",
        "conditionType": "SingleResourceMultipleMetricCriteria",
        "condition": {
            "windowSize": "PT15M",
            "allOf": [
                {
                    "metricName": "Percentage CPU",
                    "dimensions": [
                    {
                        "name": "ResourceId",
                        "value": "d92fc5cb-06cf-4309-8c9a-538eea6a17a6"
                    }
                ],
                "operator": "GreaterThan",
                "threshold": "5",
                "timeAggregation": "PT15M",
                "metricValue": 1.0
            }
            ]
        },
        "subscriptionId": "...",
        "resourceGroupName": "TEST",
        "resourceName": "test-vm",
        "resourceType": "Microsoft.Compute/virtualMachines",
        "resourceId": "...",
        "portalLink": "..."
        },
        "properties": {}
    }
    }
    ```

- Шаги 9 и 10 остаются без изменений.
- В шагах с 11 по 14 используйте следующий процесс:

  1. Выберите **+** **новый шаг,** а затем выберите **Добавить условие.** Задайте следующие условия, чтобы приложение логики выполнялось, только когда входные данные соответствуют указанным ниже значениям. При вводе значения версии в текстовое поле поместите его в кавычки ("2.0"), чтобы гарантировать, что оно будет вычисляться как строка, а не числовой тип.  Система не отображает кавычки, если вы вернетесь к странице, однако базовый код сохраняет строковый тип. 
     - `schemaId == AzureMonitorMetricAlert`
     - `version == "2.0"`
       
       !["Условие полезной нагрузки метеосигнала"](media/action-groups-logic-app/metric-alert-payload-condition.png "Состояние полезной нагрузки метео-оповещения")

  1. В условии **Если истинно** добавьте цикл **For each** и действие Microsoft Teams. Определите сообщение, используя сочетание кода HTML и динамического содержимого.

      !["Метрика оповещения истинное состояние после действия"](media/action-groups-logic-app/metric-alert-true-condition-post-action.png "Метрическое предупреждение истинное состояние пост действий")

  1. В **ложном** состоянии If определите действие групп Майкрософт, чтобы сообщить, что метрика не соответствует ожиданиям приложения логики. Включите полезные данные JSON. Обратите внимание на то, как указываются ссылки на динамическое содержимое `triggerBody` в выражении `json()`.

      !["Метрика оповещения ложное состояние после действия"](media/action-groups-logic-app/metric-alert-false-condition-post-action.png "Метическое оповещение ложное состояние пост действий")

- Шаг 15 остается без изменений. Следуйте инструкциям, чтобы сохранить приложение логики и обновить группу действий.

## <a name="calling-other-applications-besides-microsoft-teams"></a>Вызов других приложений помимо Microsoft Teams
Служба Logic Apps имеет ряд различных соединителей, которые позволяют активировать действия в самых разных приложениях и базах данных. К таким приложениям, например, относятся Slack, SQL Server, Oracle, Salesforce. Дополнительные сведения о соединителях Azure Logic Apps см. в [этой статье](../../connectors/apis-list.md).  

## <a name="next-steps"></a>Дальнейшие действия
* Изучите [обзор оповещений журнала действий Azure](../../azure-monitor/platform/alerts-overview.md) и узнайте, как получать оповещения.  
* Узнайте, как [настроить оповещения при поступлении уведомлений о Работоспособности служб Azure](../../azure-monitor/platform/alerts-activity-log-service-notifications.md).
* Подробнее о [группах действий.](../../azure-monitor/platform/action-groups.md)

