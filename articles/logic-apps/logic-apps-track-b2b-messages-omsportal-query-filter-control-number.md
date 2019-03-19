---
title: Создать запросы отслеживания сообщений B2B в Azure Monitor журналы — Azure Logic Apps | Документация Майкрософт
description: Сведения о создании запросов, которые отслеживают сообщения AS2, X12 и EDIFACT в Azure Log Analytics для Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: divyaswarnkar
ms.author: divswa
ms.reviewer: jonfan, estfan, LADocs
ms.topic: article
ms.date: 10/19/2018
ms.openlocfilehash: d4a94e75de34bbafd3bc8f1c1a0d1a6817245e5f
ms.sourcegitcommit: cdf0e37450044f65c33e07aeb6d115819a2bb822
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/01/2019
ms.locfileid: "57194917"
---
# <a name="create-tracking-queries-for-b2b-messages-in-azure-monitor-logs-for-azure-logic-apps"></a>Создание запросов на отслеживание сообщений B2B в журналах Azure Monitor для Azure Logic Apps

Чтобы найти AS2, X12 или EDIFACT сообщения, которые отслеживаются с помощью [Azure Monitor регистрирует](../log-analytics/log-analytics-overview.md), можно создавать запросы, применяющие фильтры на основе заданных критериев. Например, можно найти сообщения с определенным контрольным номером обмена.

> [!NOTE]
> На этой странице ранее описывались шаги по выполнению таких задач с помощью консоли Microsoft Operations Management Suite (OMS), которая [выводится из эксплуатации в январе 2019 г.](../azure-monitor/platform/oms-portal-transition.md) Теперь эти шаги выполняются с помощью Azure Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>Технические условия

* Приложение логики, настроенное на ведение журнала диагностики. Узнайте подробнее о [создании приложения логики](quickstart-create-first-logic-app-workflow.md) и [настройке ведения журнала для такого приложения логики](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics).

* Учетная запись интеграции, настроенная для мониторинга и ведения журнала. Узнайте подробнее о [создании учетной записи интеграции](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) и [настройке мониторинга и ведения журнала для этой учетной записи](../logic-apps/logic-apps-monitor-b2b-message.md).

* Если у вас ее еще [опубликуйте диагностические данные в Azure Monitor журналы](../logic-apps/logic-apps-track-b2b-messages-omsportal.md) и [настройте отслеживание сообщений в журналах Azure Monitor](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).

## <a name="create-queries-with-filters"></a>Создание запросов с фильтрами

Чтобы найти сообщения на основе определенных свойств или значений, можно создать запросы, в которых используются фильтры. 

1. На [портале Azure](https://portal.azure.com) выберите **Все службы**. В поле поиска введите "log analytics" и выберите **Log Analytics**.

   ![Выбор Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. В разделе **Log Analytics** найдите и выберите рабочую область Log Analytics. 

   ![Выбор рабочей области Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. В меню рабочей области в разделе **Общие** выберите **Журналы (классические)** или **Журналы**. 

   В этом примере показано, как использовать классическое представление журналов. 
   Если в разделе **Максимально эффективная работа с Log Analytics** выбрать **Просмотр журналов**, в меню **Поиск и анализ журналов** вы найдете пункт **Logs (classic view)** (Журналы, классическое представление). 

   ![Классическое представление журналов](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/view-classic-logs.png)

1. В поле редактирования запроса введите первые буквы имени поля, которое вы хотите найти. При вводе текста редактор запросов отображает возможные совпадения и доступные операции. После создания запроса щелкните **Выполнить** или нажмите клавишу ВВОД.

   В этом примере выполняется поиск строки **LogicAppB2B**. 
   Дополнительные сведения о [способах поиска данных в журналах Azure Monitor](../log-analytics/log-analytics-log-searches.md).

   ![Начните вводить строку запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/create-query.png)

1. Чтобы изменить диапазон времени, который вы хотите просмотреть, на левой панели выберите нужный диапазон в списке или перетащите ползунок. 

   ![Изменение диапазона времени](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/change-timeframe.png)

1. Чтобы добавить фильтр к запросу, щелкните **Добавить**. 

   ![Добавление фильтра к запросу](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/add-filter.png)

1. В разделе **Добавить фильтры** введите имя фильтра, который нужно найти. Если фильтр найден, выберите его. На левой панели снова щелкните **Добавить**.

   К примеру, здесь показан другой запрос, который выполняет поиск событий по условию **Type=="AzureDiagnostics"** и находит результаты на основе контрольного номера обмена путем выбора фильтра **event_record_messageProperties_interchangeControlNumber_s**.

   ![Выбор значения фильтра](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filter-example.png)

   Когда вы щелкнете **Добавить**, запрос будет обновлен на основе выбранного события и значения фильтрации. 
   Предыдущие результаты теперь также отфильтрованы. 

   К примеру, этот запрос выполняет поиск по условию **Type=="AzureDiagnostics"** и находит результаты на основе контрольного номера обмена с помощью фильтра **event_record_messageProperties_interchangeControlNumber_s**.

   ![Отфильтрованные результаты](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/filtered-results.png)

<a name="save-oms-query"></a>

## <a name="save-query"></a>Сохранение запроса

Чтобы сохранить запрос в представлении **Журналы (классические)**, выполните следующие шаги:

1. Для запроса на странице **Журналы (классические)** выберите **Аналитика**. 

   ![Выбор раздела "Аналитика"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-analytics.png)

1. На панели инструментов запроса щелкните **Сохранить**.

   ![Нажмите кнопку Save (Сохранить)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/save-query.png)

1. Укажите сведения о запросе, например присвойте ему имя, выберите **Запрос** и укажите имя категории. Когда все будет готово, нажмите **Сохранить**.

   ![Нажмите кнопку Save (Сохранить)](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/query-details.png)

1. Чтобы просмотреть сохраненные запросы, вернитесь на страницу запросов. На панели инструментов запроса щелкните **Сохраненные условия поиска**.

   ![Выбор раздела "Сохраненные условия поиска"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. В разделе **Сохраненные условия поиска** выберите запрос, чтобы просмотреть его результаты. 

   ![Выбор запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png)

   Чтобы обновить запрос и найти другие результаты, измените запрос.

## <a name="find-and-run-saved-queries"></a>Поиск и выполнение сохраненных запросов

1. На [портале Azure](https://portal.azure.com) выберите **Все службы**. В поле поиска введите "log analytics" и выберите **Log Analytics**.

   ![Выбор Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/find-log-analytics.png)

1. В разделе **Log Analytics** найдите и выберите рабочую область Log Analytics. 

   ![Выбор рабочей области Log Analytics](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/select-log-analytics-workspace.png)

1. В меню рабочей области в разделе **Общие** выберите **Журналы (классические)** или **Журналы**. 

   В этом примере показано, как использовать классическое представление журналов. 

1. Когда откроется страница запроса, на панели инструментов запроса щелкните **Сохраненные условия поиска**.

   ![Выбор раздела "Сохраненные условия поиска"](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/choose-saved-searches.png)

1. В разделе **Сохраненные условия поиска** выберите запрос, чтобы просмотреть его результаты. 

   ![Выбор запроса](media/logic-apps-track-b2b-messages-omsportal-query-filter-control-number/saved-query-results.png) 

   Запрос будет выполнен автоматически. Если по какой-либо причине этого не произошло, в редакторе запросов щелкните **Выполнить**.

## <a name="next-steps"></a>Дальнейшие действия

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схемы отслеживания X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)