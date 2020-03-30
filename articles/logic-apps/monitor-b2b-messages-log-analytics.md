---
title: Мониторинг B2B-сообщений с помощью Azure Monitor
description: Сообщения AS2, X12 и EDIFACT путем настройки журналов Azure Monitor и сбора диагностических данных для приложений Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: e9ba5a516293eb72a715dc9d0df7db4d5a4ea3c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76907985"
---
# <a name="set-up-azure-monitor-logs-and-collect-diagnostics-data-for-b2b-messages-in-azure-logic-apps"></a>Настройка журналов Azure Monitor и сбор диагностических данных для B2B-сообщений в приложениях логики Azure

После настройки B2B-сообщения между торговыми партнерами на вашем интеграционном счете эти партнеры могут обмениваться сообщениями с помощью таких протоколов, как AS2, X12 и EDIFACT. Чтобы проверить, работает ли это сообщение так, как вы ожидаете, можно настроить [журналы Azure Monitor](../azure-monitor/platform/data-platform-logs.md) для учетной записи интеграции. [Azure Monitor](../azure-monitor/overview.md) помогает отслеживать облачные и штатные среды, чтобы было легче поддерживать их доступность и производительность. Используя журналы Azure Monitor, можно записывать и хранить данные о данных и событиях времени выполнения, такие как события запуска, события выполнения и события действия в [рабочей области log Analytics.](../azure-monitor/platform/resource-logs-collect-workspace.md) Для сообщений журнал также собирает информацию, такую как:

* число и состояние сообщений;
* состояние подтверждений;
* Корреляции между сообщениями и подтверждениями
* подробное описание ошибок при сбоях;

Azure Monitor позволяет создавать [запросы журналов,](../azure-monitor/log-query/log-query-overview.md) чтобы помочь вам найти и просмотреть эту информацию. Вы также можете [использовать эти диагностические данные с другими службами Azure,](../logic-apps/monitor-logic-apps-log-analytics.md#extend-data)такими как Azure Storage и Azure Event Hubs.

Чтобы настроить журнал для учетной записи интеграции, [установите решение Logic Apps B2B](#install-b2b-solution) на портале Azure. Это решение предоставляет агрегированную информацию для событий B2B-сообщений. Затем, чтобы включить журнал и создание запросов для этой информации, навязайте [журналы Azure Monitor.](#set-up-resource-logs)

В этой статье показано, как включить журнал Azure Monitor для вашей учетной записи интеграции.

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область Log Analytics. См. дополнительные сведения о [создании рабочей области Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Логическое приложение, настроенное с помощью журналов Azure Monitor и отправляет эту информацию в рабочее пространство Analytics журнала. [Узнайте, как настроить журналы Azure Monitor для приложения логики.](../logic-apps/monitor-logic-apps.md)

* Учетная запись интеграции, связанная с приложением логики. [Узнайте, как связать учетную запись интеграции с логическим приложением.](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md)

<a name="install-b2b-solution"></a>

## <a name="install-logic-apps-b2b-solution"></a>Установка решения Logic Apps B2B

Прежде чем журналы Azure Monitor смогут отслеживать B2B-сообщения для вашего приложения логики, добавьте решение **Logic Apps B2B** в рабочее пространство Analytics.

1. В поле поиска [портала Azure](https://portal.azure.com)введите, `log analytics workspaces`а затем выберите **рабочие области log Analytics.**

   ![Выберите рабочие области «Аналитика журнала»](./media/monitor-b2b-messages-log-analytics/find-select-log-analytics-workspaces.png)

1. В **рабочих областях анализа журналов**выберите рабочее пространство.

   ![Выбор рабочей области Log Analytics](./media/monitor-b2b-messages-log-analytics/select-log-analytics-workspace.png)

1. На панели Обзора, под **Начало работы с журналом Analytics** > **Настройка мониторинга решений,** выберите Просмотр **решений**.

   ![На панели обзора выберите "Посмотреть решения"](./media/monitor-b2b-messages-log-analytics/log-analytics-workspace.png)

1. На панели обзора **выберите Добавить**.

   ![На панели обзора добавьте новое решение](./media/monitor-b2b-messages-log-analytics/add-logic-apps-management-solution.png)

1. После открытия **Marketplace** в поле поиска `logic apps b2b`введите и выберите **Логические приложения B2B.**

   ![Из Marketplace выберите "Управление логическими приложениями"](./media/monitor-b2b-messages-log-analytics/select-logic-apps-b2b-solution.png)

1. На панели описания решения выберите **Создать**.

   ![Выберите решение "Создать" для добавления решения "Logic Apps B2B"](./media/monitor-b2b-messages-log-analytics/create-logic-apps-b2b-solution.png)

1. Просмотрите и подтвердите рабочее пространство Log Analytics, где требуется установить решение, и выберите **Создать** снова.

   ![Выберите "Создать" для "Логические приложения B2B"](./media/monitor-b2b-messages-log-analytics/confirm-log-analytics-workspace.png)

   После развертывания Azure в группе ресурсов Azure, содержащей рабочее пространство журнала Analytics, решение отображается в сводном стекол рабочего пространства рабочего места. При обработке B2B-сообщений количество сообщений на этом панели обновляется.

   ![Панель сводкового пространства рабочего пространства](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

<a name="set-up-resource-logs"></a>

## <a name="set-up-azure-monitor-logs"></a>настройка журналов Azure Monitor;

Вы можете включить журнал Azure Monitor непосредственно из учетной записи интеграции.

1. На [портале Azure](https://portal.azure.com) найдите и выберите свою учетную запись интеграции.

   ![Поиск и выбор учетной записи интеграции](./media/monitor-b2b-messages-log-analytics/find-integration-account.png)

1. В меню интеграционного аккаунта под **мониторингом**выберите **настройки диагностики.** Выберите **Диагностическую настройку добавления.**

   ![В рамках "Мониторинга" выберите "Настройки диагностики"](./media/monitor-b2b-messages-log-analytics/monitor-diagnostics-settings.png)

1. Чтобы создать настройку, выполните следующие действия:

   1. Укажите имя для настройки.

   1. Установите флажок **Отправить в Log Analytics**.

   1. Для **подписки**выберите подписку Azure, связанную с рабочим пространством аналитики журналов.

   1. Для **рабочего пространства аналитики журнала**выберите рабочее пространство, которое вы хотите использовать.

   1. Под **журналом**выберите категорию **IntegrationAccountTrackingEvents,** которая определяет категорию событий, которую вы хотите записать.

   1. Когда все будет готово, нажмите кнопку **Сохранить**.

   Пример: 

   ![Настройка журналов Azure Monitor для сбора диагностических данных](./media/monitor-b2b-messages-log-analytics/send-diagnostics-data-log-analytics-workspace.png)

<a name="view-message-status"></a>

## <a name="view-message-status"></a>Просмотр состояния сообщения

После запуска приложения логики можно просматривать состояние и данные об этих сообщениях в рабочем пространстве Log Analytics.

1. В поле поиска [портала Azure](https://portal.azure.com) найдите и откройте рабочее пространство Log Analytics.

1. В меню рабочего пространства выберите **резюме** > **Логики Приложений B2B**рабочего пространства.

   ![Панель сводкового пространства рабочего пространства](./media/monitor-b2b-messages-log-analytics/b2b-overview-messages-summary.png)

   > [!NOTE]
   > Если плитка Logic Apps B2B не сразу показывает результаты после запуска, попробуйте выбрать **Refresh** или подождите некоторое время, прежде чем пытаться снова.

   По умолчанию на плитке **Logic Apps B2B** отображаются данные за один день. Чтобы изменить область данных на другой интервал, выберите элемент управления областью в верхней части страницы:

   ![Изменение интервала](./media/monitor-b2b-messages-log-analytics/change-summary-interval.png)

1. После появления панели мониторинга состояния сообщений можно просмотреть дополнительные сведения о сообщениях определенного типа, данные о которых отображаются за один день. Выберите плитку для **AS2,** **X12**или **EDIFACT.**

   ![Просмотр статусов для сообщений](./media/monitor-b2b-messages-log-analytics/workspace-summary-b2b-messages.png)

   Отобразится список сообщений в соответствии с выбранной плиткой. Ниже представлен пример списка сообщений AS2.

   ![Статусы и детали для сообщений AS2](./media/monitor-b2b-messages-log-analytics/as2-message-results-list.png)

   Для получения дополнительных сведений о свойствах для каждого типа сообщений см. указанные ниже описания свойств сообщения:

   * [Свойства сообщения AS2](#as2-message-properties)
   * [Свойства сообщения X12](#x12-message-properties)
   * [Свойства сообщения EDIFACT](#EDIFACT-message-properties)

<!--
1. To view or export the inputs and outputs for specific messages, select those messages, and select **Download**. When you're prompted, save the .zip file to your local computer, and then extract that file.

   The extracted folder includes a folder for each selected message. If you set up acknowledgements, the message folder also includes files with acknowledgement details. Each message folder has at least these files:
   
   * Human-readable files with the input payload and output payload details
   * Encoded files with the inputs and outputs

   For each message type, you can find the folder and file name formats here:

   * [AS2 folder and file name formats](#as2-folder-file-names)
   * [X12 folder and file name formats](#x12-folder-file-names)
   * [EDIFACT folder and file name formats](#edifact-folder-file-names)

   ![Download message files](./media/monitor-b2b-messages-log-analytics/download-messages.png)

1. To view all actions that have the same run ID, on the **Log Search** page, select a message from the message list.

   You can sort these actions by column, or search for specific results.

   * To search results with prebuilt queries, select **Favorites**.

   * Learn [how to build queries by adding filters](../logic-apps/create-monitoring-tracking-queries.md). Or learn more about [how to find data with log searches in Azure Monitor logs](../log-analytics/log-analytics-log-searches.md).

   * To change query in the search box, update the query with the columns and values that you want to use as filters.
-->

<a name="message-list-property-descriptions"></a>

## <a name="property-descriptions-and-name-formats-for-as2-x12-and-edifact-messages"></a>Описания свойств и форматы имен для сообщений AS2, X12 и EDIFACT

Для каждого типа сообщения ниже приведены описания свойств и форматы имен для загруженных файлов сообщений.

<a name="as2-message-properties"></a>

### <a name="as2-message-property-descriptions"></a>Описания свойств сообщения AS2

Ниже приведены описания свойств для каждого сообщения AS2.

| Свойство | Описание |
|----------|-------------|
| **Отправителя** | Гостевой партнер, указанный в **параметрах получения**, или главный партнер, указанный в **параметрах отправки**, для соглашения AS2 |
| **Приемник** | Главный партнер, указанный в **параметрах получения**, или гостевой партнер, указанный в **параметрах отправки**, для соглашения AS2 |
| **Логика App** | Приложение логики, в котором настроены действия AS2 |
| **Состояние** | Состояние сообщения AS2 <br>Success: получено или отправлено корректное сообщение AS2, MDN не настроено. <br>Success: получено или отправлено корректное сообщение AS2, MDN настроено и получено либо отправлено. <br>Failed: получено некорректное сообщение AS2. MDN не настроено. <br>Pending: получено или отправлено корректное сообщение AS2. MDN настроено и ожидается. |
| **Ack** | Состояние сообщения MDN <br>Accepted: получено или отправлено положительное MDN. <br>Pending: ожидается получение или отправка MDN. <br>Rejected: получено или отправлено отрицательное MDN. <br>Not Required: в соглашении не настроено MDN. |
| **Направление** | Направление сообщения AS2 |
| **Tracking ID** | Идентификатор для корреляции всех триггеров и действий в приложении логики |
| **Код сообщения (ID)** | Идентификатор сообщения AS2, полученный из заголовков сообщения AS2 |
| **Timestamp** | Время обработки сообщения действием AS2 |
|||

<!--
<a name="as2-folder-file-names"></a>

### AS2 name formats for downloaded message files

Here are the name formats for each downloaded AS2 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_AS2\_[correlation-ID]\_[message-ID]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_AS2\_[correlation-ID]\_outputs.txt |
|||
-->

<a name="x12-message-properties"></a>

### <a name="x12-message-property-descriptions"></a>Описания свойств сообщения X12

Ниже приведены описания свойств для каждого сообщения X12.

| Свойство | Описание |
|----------|-------------|
| **Отправителя** | Гостевой партнер, указанный в **параметрах получения**, или главный партнер, указанный в **параметрах отправки**, для соглашения X12 |
| **Приемник** | Главный партнер, указанный в **параметрах получения**, или гостевой партнер, указанный в **параметрах отправки**, для соглашения X12 |
| **Логика App** | Приложение логики, в котором настроены действия X12 |
| **Состояние** | Состояние сообщения X12 <br>Success: получено или отправлено корректное сообщение X12. Функциональное подтверждение не настроено. <br>Success: получено или отправлено корректное сообщение X12. Функциональное подтверждение настроено и либо получено, либо отправлено. <br>Failed: получено или отправлено некорректное сообщение X12. <br>Pending: получено или отправлено корректное сообщение X12. Функциональное подтверждение настроено и ожидается его получение. |
| **Ack** | Состояние функционального подтверждения (997). <br>Accepted: получено или отправлено положительное функциональное подтверждение. <br>Rejected: получено или отправлено отрицательное функциональное подтверждение. <br>Pending: ожидается функциональное подтверждение, но оно еще не получено. <br>Pending: функциональное подтверждение создано, но его не удалось отправить в партнер. <br>Not Required: функциональное подтверждение не настроено. |
| **Направление** | Направление сообщения X12 |
| **Tracking ID** | Идентификатор для корреляции всех триггеров и действий в приложении логики |
| **Тип сообщения** | Тип сообщения EDI X12 |
| **ICN** | Контрольный номер обмена для сообщения X12 |
| **TSCN** | Контрольный номер набора транзакций для сообщения X12 |
| **Timestamp** | Время обработки сообщения действием X12 |
|||

<!--
<a name="x12-folder-file-names"></a>

### X12 name formats for downloaded message files

Here are the name formats for each downloaded X12 message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_X12\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_X12\_[interchange-control-number]\_outputs.txt |
|||
-->

<a name="EDIFACT-message-properties"></a>

### <a name="edifact-message-property-descriptions"></a>Описания свойств сообщения EDIFACT

Ниже приведены описания свойств для каждого сообщения EDIFACT.

| Свойство | Описание |
|----------|-------------|
| **Отправителя** | Гостевой партнер, указанный в **параметрах получения**, или главный партнер, указанный в **параметрах отправки**, для соглашения EDIFACT |
| **Приемник** | Главный партнер, указанный в **параметрах получения**, или гостевой партнер, указанный в **параметрах отправки**, для соглашения EDIFACT |
| **Логика App** | Приложение логики, в котором настроены действия EDIFACT |
| **Состояние** | Состояние сообщения EDIFACT <br>Success: получено или отправлено корректное сообщение EDIFACT. Функциональное подтверждение не настроено. <br>Success: получено или отправлено корректное сообщение EDIFACT. Функциональное подтверждение настроено и либо получено, либо отправлено. <br>Failed: получено или отправлено некорректное сообщение EDIFACT <br>Pending: получено или отправлено корректное сообщение EDIFACT. Функциональное подтверждение настроено и ожидается его получение. |
| **Ack** | Функциональный статус Ack (CONTRL) <br>Accepted: получено или отправлено положительное функциональное подтверждение. <br>Rejected: получено или отправлено отрицательное функциональное подтверждение. <br>Pending: ожидается функциональное подтверждение, но оно еще не получено. <br>Pending: функциональное подтверждение создано, но его не удалось отправить в партнер. <br>Not Required: функциональное подтверждение не настроено. |
| **Направление** | Направление сообщения EDIFACT |
| **Tracking ID** | Идентификатор для корреляции всех триггеров и действий в приложении логики |
| **Тип сообщения** | Тип сообщения EDIFACT |
| **ICN** | Контрольный номер обмена сообщениями EDIFACT |
| **TSCN** | Контрольный номер набора транзакций для сообщения EDIFACT |
| **Timestamp** | Время обработки сообщения действием EDIFACT |
|||

<!--
<a name="edifact-folder-file-names"></a>

### EDIFACT name formats for downloaded message files

Here are the name formats for each downloaded EDIFACT message folder and files.

| Folder or file | Name format |
|----------------|-------------|
| Message folder | [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_[global-control-number]\_[transaction-set-control-number]\_[timestamp] |
| Input, output, and if set up, acknowledgement files | **Input payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_input_payload.txt </p>**Output payload**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_output\_payload.txt </p></p>**Inputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_inputs.txt </p></p>**Outputs**: [sender]\_[receiver]\_EDIFACT\_[interchange-control-number]\_outputs.txt |
|||
-->

## <a name="next-steps"></a>Дальнейшие действия

* [Создание, мониторинг и отслеживание запросов](../logic-apps/create-monitoring-tracking-queries.md)