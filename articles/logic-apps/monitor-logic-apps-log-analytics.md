---
title: Мониторинг приложений логики с помощью журналов Azure Monitor
description: Устранение неполадок приложений логики путем настройки журналов Azure Monitor и сбора диагностических данных для Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 3e41f92f9e41f7a05102e8c0e1c2edb81fa50bf3
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2020
ms.locfileid: "77426332"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-azure-logic-apps"></a>Настройка журналов Azure Monitor и получение диагностических данных для Azure Logic Apps

Чтобы получить более широкие отладочные сведения о приложениях логики во время выполнения, можно настроить и использовать [журналы Azure Monitor](../azure-monitor/platform/data-platform-logs.md) для записи и хранения сведений о данных и событиях времени выполнения, таких как события триггера, события запуска и события действий в [рабочей области log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). [Azure Monitor](../azure-monitor/overview.md) помогает отслеживать облачные и локальные среды, чтобы можно было легко поддерживать доступность и производительность. С помощью журналов Azure Monitor можно создавать запросы к [журналам](../azure-monitor/log-query/log-query-overview.md) , которые помогают в собрании и проверке этих данных. Вы также можете [использовать эти диагностические данные с другими службами Azure](#extend-data), такими как служба хранилища Azure и концентраторы событий Azure.

Чтобы настроить ведение журнала для приложения логики, можно [включить log Analytics при создании приложения логики](#logging-for-new-logic-apps)или [установить решение по управлению Logic Apps](#install-management-solution) в рабочей области log Analytics для существующих приложений логики. Это решение предоставляет статистические сведения о выполнении приложений логики и содержит конкретные сведения, такие как состояние, время выполнения, состояние повторной отправки и идентификаторы корреляции. Затем, чтобы включить ведение журнала и создать запросы для этих сведений, [Настройте журналы Azure Monitor](#set-up-resource-logs).

В этой статье показано, как включить Log Analytics при создании приложений логики, установке и настройке решения для управления Logic Apps, а также о настройке и создании запросов для журналов Azure Monitor.

## <a name="prerequisites"></a>предварительные требования

Перед началом работы вам потребуется [Рабочая область log Analytics](../azure-monitor/platform/resource-logs-collect-workspace.md). Если у вас нет рабочей области, Узнайте, [как создать рабочую область log Analytics](../azure-monitor/learn/quick-create-workspace.md).

<a name="logging-for-new-logic-apps"></a>

## <a name="enable-log-analytics-for-new-logic-apps"></a>Включение Log Analytics для новых приложений логики

Вы можете включить Log Analytics при создании приложения логики.

1. В [портал Azure](https://portal.azure.com)на панели, где вы предоставляете сведения для создания приложения логики, выполните следующие действия.

   1. В разделе **log Analytics**выберите **вкл**.

   1. В списке **Рабочая область log Analytics** выберите рабочую область, в которой нужно отправить данные из приложения логики.

      ![Предоставление сведений о приложении логики](./media/monitor-logic-apps-log-analytics/create-logic-app-details.png)

      По завершении этого шага Azure создает приложение логики, которое теперь связано с рабочей областью Log Analytics. Кроме того, этот шаг автоматически устанавливает решение по управлению Logic Apps в рабочей области.

1. Когда все будет готово, выберите **Создать**.

1. После запуска приложения логики для просмотра запущенных приложений логики [выполните следующие действия](#view-logic-app-runs).

<a name="install-management-solution"></a>

## <a name="install-logic-apps-management-solution"></a>Установка решения Logic Apps Management

Если вы включили Log Analytics при создании приложения логики, пропустите этот шаг. Решение для управления Logic Apps уже установлено в рабочей области Log Analytics.

1. В поле поиска [портал Azure](https://portal.azure.com)введите `log analytics workspaces`, а затем выберите **log Analytics рабочие области**.

   ![Выберите "Log Analytics рабочие области".](./media/monitor-logic-apps-log-analytics/find-select-log-analytics-workspaces.png)

1. В разделе **log Analytics рабочие области**выберите рабочую область.

   ![Выбор рабочей области Log Analytics](./media/monitor-logic-apps-log-analytics/select-log-analytics-workspace.png)

1. В области **Обзор** в разделе **начало работы с log Analytics** > **настроить решения мониторинга**выберите пункт **Просмотреть решения**.

   ![На панели "Обзор" выберите "Просмотр решений".](./media/monitor-logic-apps-log-analytics/log-analytics-workspace.png)

1. В разделе **Обзор**выберите **Добавить**.

   ![В области "Обзор" добавьте новое решение](./media/monitor-logic-apps-log-analytics/add-logic-apps-management-solution.png)

1. После открытия **Marketplace** в поле поиска введите `logic apps management`и выберите **Logic Apps управление**.

   ![В Marketplace выберите "Управление Logic Apps".](./media/monitor-logic-apps-log-analytics/select-logic-apps-management.png)

1. В области описание решения выберите **создать**.

   ![Выберите "создать", чтобы добавить решение "Управление Logic Apps"](./media/monitor-logic-apps-log-analytics/create-logic-apps-management-solution.png)

1. Проверьте и подтвердите Log Analytics рабочей области, в которую нужно установить решение, и нажмите кнопку **создать** еще раз.

   ![Выберите "создать" для "управления Logic Apps".](./media/monitor-logic-apps-log-analytics/confirm-log-analytics-workspace.png)

   После того, как Azure развернет решение в группу ресурсов Azure, содержащую рабочую область Log Analytics, решение появится в области сводки вашей рабочей области.

   ![Панель сводки по рабочей области](./media/monitor-logic-apps-log-analytics/workspace-summary-pane-logic-apps-management.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>настройка журналов Azure Monitor;

При хранении сведений о событиях и данных времени выполнения в [Azure Monitor журналах](../azure-monitor/platform/data-platform-logs.md)можно создавать [запросы журналов](../azure-monitor/log-query/log-query-overview.md) , помогающие находить и просматривать эти сведения.

1. На [портале Azure](https://portal.azure.com) найдите и выберите требуемое приложение логики.

1. В меню приложения логики в разделе **мониторинг**выберите **параметры диагностики** > **Добавить параметр диагностики**.

   ![В разделе "Мониторинг" выберите "параметры диагностики" > "добавить параметр диагностики".](./media/monitor-logic-apps-log-analytics/logic-app-diagnostics.png)

1. Чтобы создать параметр, выполните следующие действия.

   1. Укажите имя параметра.

   1. Установите флажок **Отправить в Log Analytics**.

   1. В поле **Подписка**выберите подписку Azure, связанную с рабочей областью log Analytics.

   1. Для **log Analytics рабочей области**выберите рабочую область, которую хотите использовать.

   1. В разделе **Журнал**выберите категорию **WorkflowRuntime** , которая указывает категорию событий, которую необходимо записать.

   1. Чтобы выбрать все метрики, в разделе **Метрика**выберите **аллметрикс**.

   1. Когда все будет готово, нажмите кнопку **Сохранить**.

   Пример:

   ![Выберите рабочую область Log Analytics и данные для ведения журнала](./media/monitor-logic-apps-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-logic-app-runs"></a>

## <a name="view-logic-app-runs-status"></a>Просмотр состояния выполнения приложения логики

После запуска приложения логики можно просмотреть данные об этих запусках в рабочей области Log Analytics.

1. В [портал Azure](https://portal.azure.com)найдите и откройте рабочую область log Analytics.

1. В меню рабочей области выберите **Сводка по рабочей области** > **Logic Apps управление**.

   ![Состояние выполнения приложения логики и счетчик](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary.png)

   > [!NOTE]
   > Если плитка управления Logic Apps не выводит результаты сразу после выполнения, попробуйте выбрать **Обновить** или подождать некоторое время, прежде чем повторять попытку.

   Здесь сведения о выполнении приложений логики группируются по имени или по состоянию выполнения. Кроме того, можно просмотреть сведения о сбоях в действиях или триггерах для выполнений приложения логики.

   ![Сводные данные о состоянии выполнения приложений логики](./media/monitor-logic-apps-log-analytics/logic-app-runs-summary-details.png)

1. Чтобы просмотреть все запуски для конкретного приложения логики или состояния, выберите строку для этого приложения логики или состояния.

   Ниже приведен пример, в котором показаны все сведения о выполнении конкретного приложения логики.

   ![Просмотр запусков и состояния приложений логики](./media/monitor-logic-apps-log-analytics/logic-app-run-details.png)

   Для действий, в которых [настроены отслеживании свойств](#extend-data), можно также просмотреть эти свойства, выбрав **представление** в столбце " **Отслеживание свойств** ". Для поиска отслеживаемых свойств можно использовать фильтр столбцов.

   ![Просмотр отслеживаемых свойств для приложения логики](./media/monitor-logic-apps-log-analytics/logic-app-tracked-properties.png)

   > [!NOTE]
   > Для отслеживания свойств или завершенных событий может возникнуть задержка 10-15 минут перед отображением в рабочей области Log Analytics.
   > Кроме того, возможность **повторной отправки** на этой странице сейчас недоступна.

1. Чтобы отфильтровать результаты, можно выполнить фильтрацию на стороне клиента и на стороне сервера.

   * **Фильтр на стороне клиента**: для каждого столбца выберите нужные фильтры, например:

     ![Пример фильтров столбцов](./media/monitor-logic-apps-log-analytics/filters.png)

   * **Фильтр на стороне сервера**. чтобы выбрать конкретное временное окно или ограничить число отображаемых запусков, используйте элемент управления "область" в верхней части страницы. По умолчанию за раз отображается только 1000 записей.

     ![Изменение временного окна](./media/monitor-logic-apps-log-analytics/change-interval.png)

1. Чтобы просмотреть все действия и сведения о конкретном запуске, выберите строку для запуска приложения логики.

   Ниже приведен пример, в котором показаны все действия и триггеры для конкретного запуска приложения логики.

   ![Просмотр действий для выполнения приложения логики](./media/monitor-logic-apps-log-analytics/logic-app-action-details.png)

<!-------------
   * **Resubmit**: You can resubmit one or more logic apps runs that failed, succeeded, or are still running. Select the check boxes for the runs that you want to resubmit, and then select **Resubmit**.

     ![Resubmit logic app runs](./media/monitor-logic-apps-log-analytics/logic-app-resubmit.png)
--------------->

<a name="extend-data"></a>

## <a name="send-diagnostic-data-to-azure-storage-and-azure-event-hubs"></a>Отправка диагностических данных в службу хранилища Azure и концентраторы событий Azure

Наряду с журналами Azure Monitor можно расширить возможности использования диагностических данных приложения логики с другими службами Azure, например:

* [Архивация журналов ресурсов Azure в учетную запись хранения](../azure-monitor/platform/resource-logs-collect-storage.md)
* [Потоковая передача журналов платформы Azure в концентраторы событий Azure](../azure-monitor/platform/resource-logs-stream-event-hubs.md)

После этого можно организовать мониторинг в режиме реального времени с помощью данных телеметрии и аналитики из других служб, таких как [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) и [Power BI](../azure-monitor/platform/powerbi.md). Пример:

* [Потоковая передача данных из Центров событий в Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Анализ потоковой передачи данных с помощью Stream Analytics и создание панели мониторинга в Power BI для анализа данных в режиме реального времени](../stream-analytics/stream-analytics-power-bi-dashboard.md)

На основе расположений, в которых вы хотите отправить диагностические данные, сначала [Создайте учетную запись хранения Azure](../storage/common/storage-create-storage-account.md) или [концентратор событий Azure](../event-hubs/event-hubs-create.md). Затем можно выбрать назначения для отправки этих данных. Периоды хранения применяются только при использовании учетной записи хранения.

![Отправка данных в учетную запись хранения Azure или в концентратор событий](./media/monitor-logic-apps-log-analytics/diagnostics-storage-event-hub-log-analytics.png)

<a name="diagnostic-event-properties"></a>

## <a name="azure-monitor-diagnostics-events"></a>События диагностики Azure Monitor

Каждое событие диагностики содержит подробные сведения о приложении логики и данном событии, например такие, как состояние, время начала, время окончания и т. д. Чтобы программно настроить мониторинг, отслеживание и ведение журнала, эти сведения можно использовать с [REST API для Azure Logic Apps](https://docs.microsoft.com/rest/api/logic) и [REST API для Azure Monitor](../azure-monitor/platform/metrics-supported.md#microsoftlogicworkflows). Можно также использовать свойства `clientTrackingId` и `trackedProperties`, которые отображаются в 

* `clientTrackingId`. Если это свойство не указано, Azure автоматически создает этот идентификатор и сопоставляет события во время выполнения приложения логики, включая все вложенные рабочие процессы, вызываемые из приложения логики. Этот идентификатор можно указать вручную в триггере, передав заголовок `x-ms-client-tracking-id` со значением пользовательского идентификатора в запросе триггера. Можно использовать триггер запроса, триггер HTTP или триггер веб-перехватчика.

* `trackedProperties`. чтобы контролировать входные или выходные данные в диагностических данных, можно добавить раздел `trackedProperties` в действие с помощью конструктора приложений логики или непосредственно в определении JSON приложения логики. Отслеживаемые свойства позволяют отслеживать входные и выходные данные только одного действия. Однако можно использовать свойства `correlation` событий, чтобы сопоставлять действия в выполнении. Для отслеживания нескольких свойств, одного или нескольких свойств добавьте раздел `trackedProperties` и свойства, которые требуется определить.

  Ниже приведен пример, демонстрирующий, как определение действия " **Инициализация переменной** " включает отслеживание свойств из входных данных действия, где входные данные являются массивом, а не записью.

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

  В этом примере показано несколько отслеживаний свойств:

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

В этом примере показано, как событие `ActionCompleted` включает атрибуты `clientTrackingId` и `trackedProperties`:

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

* [Создание запросов наблюдения и отслеживания](../logic-apps/create-monitoring-tracking-queries.md)
* [Мониторинг сообщений B2B с помощью журналов Azure Monitor](../logic-apps/monitor-b2b-messages-log-analytics.md)
