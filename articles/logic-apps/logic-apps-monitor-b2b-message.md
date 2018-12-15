---
title: Мониторинг сообщений B2B с помощью Log Analytics в Azure Logic Apps | Документация Майкрософт
description: Сведения о мониторинге сообщений AS2, X12 и EDIFACT для учетных записей интеграции и Azure Logic Apps и настройке ведения журнала диагностики в Azure Log Analytics
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/23/2018
ms.openlocfilehash: d7a32abe52e6f1109becc18c84b19d0ccd69719c
ms.sourcegitcommit: 85d94b423518ee7ec7f071f4f256f84c64039a9d
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/14/2018
ms.locfileid: "53385471"
---
# <a name="monitor-b2b-messages-with-azure-log-analytics-in-azure-logic-apps"></a>Мониторинг сообщений B2B с помощью Azure Log Analytics в Azure Logic Apps

Когда вы настроите взаимодействие B2B между торговыми партнерами в учетной записи интеграции, эти партнеры смогут обмениваться сообщениями друг с другом. Чтобы проверить, осуществляется ли это взаимодействие ожидаемым образом, можно использовать мониторинг сообщений AS2, X12 и EDIFACT, а также настроить ведение журналов диагностики для учетной записи интеграции с помощью [Azure Log Analytics](../log-analytics/log-analytics-overview.md). Эта служба выполняет мониторинг облачной и локальной сред, помогает обеспечить их доступность и производительность, а также собирает сведения о среде выполнения и событиях для отладки. Также вы можете [использовать полученные данные в других службах](#extend-diagnostic-data), таких как служба хранилища Azure и Центры событий Azure.

> [!NOTE]
> Эта страница может содержать упоминания консоли Microsoft Operations Management Suite (OMS), которая [будет считаться устаревшей с января 2019 г.](../azure-monitor/platform/oms-portal-transition.md), но везде, где это возможно, устаревшие шаги заменяются новыми для Azure Log Analytics. 

## <a name="prerequisites"></a>Предварительные требования

* Приложение логики, настроенное на ведение журнала диагностики. Узнайте подробнее о [создании приложения логики](quickstart-create-first-logic-app-workflow.md) и [настройке ведения журнала для такого приложения логики](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Помимо описанных выше требований, вам нужна рабочая область Log Analytics для отслеживания и мониторинга обмена данными в сценариях B2B через Log Analytics. См. дополнительные сведения о [создании рабочей области Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Учетная запись интеграции, связанная с приложением логики. Узнайте, [как создать учетную запись интеграции и связать ее с приложениями логики](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md).

## <a name="turn-on-diagnostics-logging"></a>Включение ведения журналов диагностики

Вы можете включить ведение журнала либо непосредственно в учетной записи интеграции, либо в [службе Azure Monitor](#azure-monitor-service). Служба Azure Monitor предоставляет основные функции мониторинга с помощью данных на уровне инфраструктуры. Узнайте подробнее о службе [Azure Monitor](../azure-monitor/overview.md).

### <a name="turn-on-logging-from-integration-account"></a>Включение ведения журнала из учетной записи интеграции

1. На [портале Azure](https://portal.azure.com) найдите и выберите свою учетную запись интеграции. В разделе **Мониторинг** выберите **Параметры диагностики**.

   ![Поиск и выбор учетной записи интеграции — выбор пункта "Параметры диагностики"](media/logic-apps-monitor-b2b-message/find-integration-account.png)

1. Теперь найдите и выберите учетную запись интеграции. В списках фильтрации выберите значения, которые применимы к используемой учетной записи интеграции.
Когда все будет готово, щелкните **Добавить параметр диагностики**.

   | Свойство | Значение | ОПИСАНИЕ | 
   |----------|-------|-------------|
   | **Подписка** | <*Azure-subscription-name*> | Подписка Azure, которая связана с используемой учетной записью интеграции. | 
   | **Группа ресурсов** | <*имя_группы_ресурсов_Azure*> | Группа ресурсов Azure для используемой учетной записи интеграции. | 
   | **Тип ресурса** | **Учетные записи интеграции** | Тип ресурса Azure, для которого нужно включить ведение журнала. | 
   | **Ресурс** | <*integration-account-name*> | Имя ресурса Azure, для которого нужно включить ведение журнала | 
   ||||  

   Например: 

   ![Настройка системы диагностики для учетной записи интеграции](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Укажите имя для нового параметра диагностики, выберите рабочую область Log Analytics и данные для записи в журнал.

   1. Установите флажок **Отправить в Log Analytics**. 

   1. В разделе **Log Analytics** выберите **Настроить**. 

   1. В разделе **Рабочие области OMS** выберите рабочую область Log Analytics, которую вы намерены использовать для ведения журнала. 

      > [!NOTE]
      > Рабочие области OMS теперь заменяются рабочими областями Log Analytics. 

   1. В разделе **Журнал** выберите категорию **IntegrationAccountTrackingEvents** и щелкните **Сохранить**.

   Например:  

   ![Настройка Log Analytics для отправки диагностических данных в журнал](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Теперь [настройте отслеживание сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

<a name="azure-monitor-service"></a>

### <a name="turn-on-logging-through-azure-monitor"></a>Включение ведения журналов с помощью Azure Monitor

1. На [портале Azure](https://portal.azure.com) в главном меню выберите **Монитор**. В разделе **Параметры** выберите **Параметры диагностики**. 

   ![Выбор элементов "Монитор" > "Параметры диагностики" > учетная запись интеграции](media/logic-apps-monitor-b2b-message/monitor-diagnostics-settings.png)

1. Теперь найдите и выберите учетную запись интеграции. В списках фильтрации выберите значения, которые применимы к используемой учетной записи интеграции.
Когда все будет готово, щелкните **Добавить параметр диагностики**.

   | Свойство | Значение | ОПИСАНИЕ | 
   |----------|-------|-------------|
   | **Подписка** | <*Azure-subscription-name*> | Подписка Azure, которая связана с используемой учетной записью интеграции. | 
   | **Группа ресурсов** | <*имя_группы_ресурсов_Azure*> | Группа ресурсов Azure для используемой учетной записи интеграции. | 
   | **Тип ресурса** | **Учетные записи интеграции** | Тип ресурса Azure, для которого нужно включить ведение журнала. | 
   | **Ресурс** | <*integration-account-name*> | Имя ресурса Azure, для которого нужно включить ведение журнала | 
   ||||  

   Например: 

   ![Настройка системы диагностики для учетной записи интеграции](media/logic-apps-monitor-b2b-message/turn-on-diagnostics-integration-account.png)

1. Укажите имя для нового параметра диагностики, выберите рабочую область Log Analytics и данные для записи в журнал.

   1. Установите флажок **Отправить в Log Analytics**. 

   1. В разделе **Log Analytics** выберите **Настроить**. 

   1. В разделе **Рабочие области OMS** выберите рабочую область Log Analytics, которую вы намерены использовать для ведения журнала. 

      > [!NOTE]
      > Рабочие области OMS теперь заменяются рабочими областями Log Analytics. 

   1. В разделе **Журнал** выберите категорию **IntegrationAccountTrackingEvents** и щелкните **Сохранить**.

   Например:  

   ![Настройка Log Analytics для отправки диагностических данных в журнал](media/logic-apps-monitor-b2b-message/send-diagnostics-data-log-analytics-workspace.png)

1. Теперь [настройте отслеживание сообщений B2B в Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="use-diagnostic-data-with-other-services"></a>Использование диагностических данных в других службах

Помимо Azure Log Analytics, можно расширить возможности использования диагностических данных приложения логики в других службах Azure, например: 

* [Архивация журналов диагностики Azure в службе хранилища Azure](../azure-monitor/platform/archive-diagnostic-logs.md)
* [Потоковая передача журналов диагностики в Центры событий Azure](../monitoring-and-diagnostics/monitoring-stream-diagnostic-logs-to-event-hubs.md) 

После этого можно организовать мониторинг в режиме реального времени с помощью данных телеметрии и аналитики из других служб, таких как [Azure Stream Analytics](../stream-analytics/stream-analytics-introduction.md) и [Power BI](../azure-monitor/platform/powerbi.md). Например: 

* [Потоковая передача данных из Центров событий в Stream Analytics](../stream-analytics/stream-analytics-define-inputs.md)
* [Анализ потоковой передачи данных с помощью Stream Analytics и создание панели мониторинга в Power BI для анализа данных в режиме реального времени](../stream-analytics/stream-analytics-power-bi-dashboard.md)

В зависимости от нужных возможностей, сначала необходимо [создать учетную запись хранения Azure](../storage/common/storage-create-storage-account.md) или [создать концентратор событий Azure](../event-hubs/event-hubs-create.md). Затем вы сможете выбрать назначения для отправки данных диагностики.
Сроки хранения применяются только в том случае, если вы решили использовать учетную запись хранилища.

![Отправка данных в учетную запись хранения Azure или в концентратор событий](./media/logic-apps-monitor-b2b-message/diagnostics-storage-event-hub-log-analytics.png)

## <a name="supported-tracking-schemas"></a>Поддерживаемые схемы отслеживания

Azure поддерживает приведенные ниже типы схем отслеживания. Все схемы, за исключением настраиваемой, являются фиксированными.

* [Схема отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схема отслеживания X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемая схема отслеживания](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)

## <a name="next-steps"></a>Дополнительная информация

* [Отслеживание сообщений B2B в Azure Log Analytics](../logic-apps/logic-apps-track-b2b-messages-omsportal.md "Отслеживание сообщений B2B в Azure Log Analytics")
* [Обзор пакета интеграции Enterprise](../logic-apps/logic-apps-enterprise-integration-overview.md "Обзор пакета интеграции Enterprise")

