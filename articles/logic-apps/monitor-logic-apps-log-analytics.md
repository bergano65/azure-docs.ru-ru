---
title: Мониторинг логических приложений с помощью журналов Azure Monitor
description: Устранение неполадок логических приложений путем настройки журналов Azure Monitor и сбора диагностических данных для приложений логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79270242"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Настройка журналов Azure Monitor и сбор диагностических данных для приложений логики Azure

Чтобы получить более богатую отладку информации о логических приложениях во время выполнения, можно настроить и использовать [журналы Azure Monitor](../azure-monitor/platform/data-platform-logs.md) для записи и хранения информации о данных и событиях времени выполнения, таких как события запуска, события запуска и события действия в [рабочей области Log Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) [Azure Monitor](../azure-monitor/overview.md) помогает отслеживать облачные и штатные среды, чтобы было легче поддерживать их доступность и производительность. Используя журналы Azure Monitor, можно создать [запросы журналов,](../azure-monitor/log-query/log-query-overview.md) которые помогут вам собрать и просмотреть эту информацию. Вы также можете [использовать эти диагностические данные с другими службами Azure,](#extend-data)такими как Azure Storage и Azure Event Hubs.

Чтобы настроить журнал для приложения логики, вы можете [включить Log Analytics при создании приложения для логики,](#logging-for-new-logic-apps)или вы можете [установить решение Logic Apps Management](#install-management-solution) в рабочем пространстве журнала Analytics для существующих логических приложений. Это решение предоставляет агрегированную информацию для выполнения приложения логики и включает в себя конкретные сведения, такие как состояние, время выполнения, статус повторной отправки и идентиматизм корреляции. Затем, чтобы включить журнал и создание запросов для этой [информации, навязайте журналы Azure Monitor.](#set-up-resource-logs)

В этой статье показано, как включить log Analytics при создании логических приложений, как установить и настроить решение Logic Apps Management, а также как настроить и создать запросы для журналов Azure Monitor.

## <a name="prerequisites"></a>Предварительные требования

Перед запуском необходимо [рабочее пространство Log Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) Если у вас нет рабочего пространства, узнайте, [как создать рабочее пространство Log Analytics.](../azure-monitor/learn/quick-create-workspace.md)

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Включить аналитику журналов для новых логических приложений

Вы можете включить log Analytics при создании приложения логики.

1. На [портале Azure](https://portal.azure.com)на панели, где вы предоставляете информацию для создания приложения логики, выполните следующие действия:

   1. Под **журналом Analytics**, выберите **на**.

   1. Из списка **рабочего пространства Log Analytics** выберите рабочее пространство, где требуется отправить данные из работаемого приложения логики.

      ![Предоставление сведений о приложении логики](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      По завершении этого шага Azure создает приложение логики, которое теперь связано с рабочей областью Log Analytics. Кроме того, этот шаг автоматически устанавливает решение Logic Apps Management в рабочей области.

1. Когда все будет готово, выберите **Создать**.

1. После запуска приложения логики просмотр приложения логики [продолжайте выполнять следующие действия.](#view-logic-app-runs)

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Установка решения Logic Apps Management

Если вы включили Log Analytics при создании приложения логики, пропустите этот шаг. У вас уже есть решение logic Apps Management, установленное в рабочей области Log Analytics.

1. В поле поиска [портала Azure](https://portal.azure.com)введите, `log analytics workspaces`а затем выберите **рабочие области log Analytics.**

   ![Выберите рабочие области «Аналитика журнала»](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. В **рабочих областях анализа журналов**выберите рабочее пространство.

   ![Выбор рабочей области Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. На панели **Обзора,** под **Начало работы с журналом Analytics** > **Настройка мониторинга решений,** выберите Просмотр **решений**.

   ![На панели обзора выберите "Посмотреть решения"](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. Под **обзором,** выберите **Добавить**.

   ![На панели обзора добавьте новое решение](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. После открытия **Marketplace** в поле поиска `logic apps management`введите и выберите **Управление приложениями логики.**

   ![Из Marketplace выберите "Управление логическими приложениями"](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. На панели описания решения выберите **Создать**.

   ![Выберите "Создать" для добавления решения "Управление логическими приложениями"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Просмотрите и подтвердите рабочее пространство Log Analytics, где требуется установить решение, и выберите **Создать** снова.

   ![Выберите "Создать" для "Управление логическими приложениями"](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   После развертывания Azure в группе ресурсов Azure, содержащей рабочее пространство журнала Analytics, решение отображается в сводном стекол рабочего пространства рабочего места.

   ![Панель сводкового пространства рабочего пространства](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>настройка журналов Azure Monitor;

При хранении информации о событиях выполнения и данных в [журналах Azure Monitor](../azure-monitor/platform/data-platform-logs.md)можно создавать [запросы журналов,](../azure-monitor/log-query/log-query-overview.md) которые помогут вам найти и просмотреть эту информацию.

1. На [портале Azure](https://portal.azure.com) найдите и выберите требуемое приложение логики.

1. В меню приложения логики под **мониторингом**выберите **настройки диагностики Добавлены диагностические настройки.** > **Add diagnostic setting**

   ![В рамках "Мониторинга" выберите "Диагностические настройки" > "Добавить диагностическую настройку"](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Чтобы создать настройку, выполните следующие действия:

   1. Укажите имя для настройки.

   1. Установите флажок **Отправить в Log Analytics**.

   1. Для **подписки**выберите подписку Azure, связанную с рабочим пространством аналитики журналов.

   1. Для **рабочего пространства аналитики журнала**выберите рабочее пространство, которое вы хотите использовать.

   1. Под **журналом**выберите категорию **WorkflowRuntime,** которая определяет категорию событий, которую вы хотите записать.

   1. Чтобы выбрать все метрики, в соответствии с **метрикой,** выберите **AllMetrics**.

   1. Когда все будет готово, нажмите кнопку **Сохранить**.

   Пример:

   ![Выберите рабочую область Log Analytics и данные для ведения журнала](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Просмотр состояния приложения логики

После запуска приложения логики можно просматривать данные о них в рабочей области Log Analytics.

1. На [портале Azure](https://portal.azure.com)найдите и откройте рабочее пространство для анализа журналов.

1. В меню рабочего пространства выберите резюме > Управления**приложениями** **Workspace.**

   ![Состояние выполнения приложения логики и счетчик](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Если плитка Logic Apps Management не сразу показывает результаты после запуска, попробуйте выбрать **Refresh** или подождите недолго, прежде чем пытаться снова.

   Здесь сведения о выполнении приложений логики группируются по имени или по состоянию выполнения. Кроме того, можно просмотреть сведения о сбоях в действиях или триггерах для выполнений приложения логики.

   ![Сводные данные о состоянии выполнения приложений логики](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Чтобы просмотреть все запуски для конкретного приложения или статуса логики, выберите строку для этого приложения логики или статуса.

   Ниже приведен пример, в котором показаны все сведения о выполнении конкретного приложения логики.

   ![Просмотр запусков и состояния приложения логики](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Для действий, в которых [настроены отслеживаемые свойства,](#extend-data)можно также просмотреть эти свойства, выбрав **View** в столбце **Отслеживаемых Свойств.** Для поиска отслеживаемых свойств можно использовать фильтр столбцов.

   ![Просмотр отслеживаемых свойств для приложения логики](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Отслеживаемые свойства или завершенные события могут испытывать 10-15-минутные задержки перед появлением в рабочей области Log Analytics.
   > Кроме того, возможность **повторной отправки** на этой странице в настоящее время недоступна.

1. Чтобы отфильтровать результаты, можно выполнять фильтрацию как на стороне клиента, так и на сервере.

   * **Фильтр для клиентов:** для каждого столбца выберите нужные фильтры, например:

     ![Пример фильтров столбцов](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Фильтр стороны сервера:** Чтобы выбрать определенное временное окно или ограничить количество отображаемых запусков, используйте элемент управления областью в верхней части страницы. По умолчанию за раз отображается только 1000 записей.

     ![Изменение временного окна](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Чтобы просмотреть все действия и их детали для определенного запуска, выберите строку для запуска приложения логики.

   Вот пример, который показывает все действия и триггеры для определенного запуска приложения логики:

   ![Просмотр действий для выполнения приложения логики](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Отправка диагностических данных в концентраторы хранения azure и события Azure

Наряду с журналами Azure Monitor можно расширить способ использования диагностических данных приложения логики с другими службами Azure, например:

* [Архивация журналов ресурсов Azure в учетной записи хранения](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Поток журналов платформы Azure для концентраторов событий Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

После этого можно организовать мониторинг в режиме реального времени с помощью данных телеметрии и аналитики из других служб, таких как [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) и [Power BI](../azure-monitor/platform/powerbi.md). Пример:

* [Потоковая передача данных из Центров событий в Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Анализ потоковой передачи данных с помощью Stream Analytics и создание панели мониторинга в Power BI для анализа данных в режиме реального времени](../stream-analytics/stream-analytics-power-bi-dashboard.md)

Основываясь на местоположениях, где требуется отправить диагностические данные, убедитесь, что сначала [создадите учетную запись хранения Azure](../storage/common/storage-create-storage-account.md) или [создайте концентратор событий Azure.](../event-hubs/event-hubs-create.md) Затем можно выбрать пункты назначения, по которым вы хотите отправить эти данные. Периоды удержания применяются только при использовании учетной записи хранилища.

![Отправка данных в учетную запись хранения Azure или в концентратор событий](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>Диагностические события Azure Monitor

Каждое событие диагностики содержит подробные сведения о приложении логики и данном событии, например такие, как состояние, время начала, время окончания и т. д. Для программной настройки мониторинга, отслеживания и регистрации можно использовать эту информацию с [помощью REST API для приложений логики Azure](https://docs.microsoft.com/rest/api/logic) и [REST API для Azure Monitor.](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows) Вы также можете `clientTrackingId` `trackedProperties` использовать свойства и свойства, которые отображаются в 

* `clientTrackingId`. Если это свойство не указано, Azure автоматически создает этот идентификатор и сопоставляет события во время выполнения приложения логики, включая все вложенные рабочие процессы, вызываемые из приложения логики. Вы можете вручную указать этот идентификатор в триггере, передав `x-ms-client-tracking-id` заголовок с пользовательским значением ID в запросе триггера. Можно использовать триггер запроса, триггер HTTP или триггер веб-перехватчика.

* `trackedProperties`: Чтобы отслеживать входные данные или выходы `trackedProperties` в диагностических данных, можно добавить раздел к действию либо с помощью Logic App Designer, либо непосредственно в определении JSON приложения логики. Отслеживаемые свойства позволяют отслеживать входные и выходные данные только одного действия. Однако можно использовать свойства `correlation` событий, чтобы сопоставлять действия в выполнении. Чтобы отследить несколько свойств, одно или `trackedProperties` несколько свойств, добавьте раздел и свойства, которые вы хотите, в определение действия.

  Вот пример, который показывает, как определение исходного действия **инициализации** включает отслеживаемые свойства от ввода действия, где входный данный является массивом, а не записью.

  ``` json
  {
     "Initialize_variable": {
        "type": "InitializeVariable",
        "inputs": {
           "variables": [
              {
                 "name": "ConnectorName", 
                 "type": "String", 
                 "value": "SFTP-SSH" 
              }
           ]
        },
        "runAfter": {},
        "trackedProperties": { 
           "myTrackedPropertyName": "@action().inputs.variables[0].value"
        }
     }
  }
  ```

  В этом примере отображены несколько отслеживаемых свойств:

  ``` json
  "HTTP": {
     "type": "Http",
     "inputs": {
        "body": "@triggerBody()",
        "headers": {
           "Content-Type": "application/json"
        },
        "method": "POST",
        "uri": "http://store.fabrikam.com",
     },
     "runAfter": {},
     "trackedProperties": {
        "myActionHTTPStatusCode": "@action()['outputs']['statusCode']",
        "myActionHTTPValue": "@action()['outputs']['body']['<content>']",
        "transactionId": "@action()['inputs']['body']['<content>']"
     }
  }
  ```

В этом примере `ActionCompleted` показано, как событие включает в `clientTrackingId` себя и `trackedProperties` атрибуты:

```json
{
   "time": "2016-07-09T17:09:54.4773148Z",
   "workflowId": "/subscriptions/XXXXXXXXXXXXXXX/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp",
   "resourceId": "/subscriptions/<subscription-ID>/resourceGroups/MyResourceGroup/providers/Microsoft.Logic/workflows/MyLogicApp/runs/<run-ID>/actions/Http",
   "category": "WorkflowRuntime",
   "level": "Information",
   "operationName": "Microsoft.Logic/workflows/workflowActionCompleted",
   "properties": {
      "$schema": "2016-06-01",
      "startTime": "2016-07-09T17:09:53.4336305Z",
      "endTime": "2016-07-09T17:09:53.5430281Z",
      "status": "Succeeded",
      "code": "OK",
      "resource": {
         "subscriptionId": "<subscription-ID>",
         "resourceGroupName": "MyResourceGroup",
         "workflowId": "<logic-app-workflow-ID>",
         "workflowName": "MyLogicApp",
         "runId": "08587361146922712057",
         "location": "westus",
         "actionName": "Http"
      },
      "correlation": {
         "actionTrackingId": "e1931543-906d-4d1d-baed-dee72ddf1047",
         "clientTrackingId": "<my-custom-tracking-ID>"
      },
      "trackedProperties": {
         "myTrackedPropertyName": "<value>"
      }
   }
}
```

## <a name="next-steps"></a>Дальнейшие действия

* [Создание, мониторинг и отслеживание запросов](../logic-apps/create-monitoring-tracking-queries.md)
* [Мониторинг сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
