---
title: Просмотр и создание запросов для логических приложений в журналах Azure Monitor
description: Просмотр и создание запросов в журналах Azure Monitor для приложений логики Azure
services: logic-apps
ms.suite: integration
ms.reviewer: divswa, logicappspm
ms.topic: article
ms.date: 01/30/2020
ms.openlocfilehash: 46989ed2468469443d4e91a1834bc20b12c25a1e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76908076"
---
# <a name="view-and-create-queries-for-monitoring-and-tracking-in-azure-monitor-logs-for-azure-logic-apps"></a>Просмотр и создание запросов для мониторинга и отслеживания в журналах Azure Monitor для приложений логики Azure

Можно просмотреть основные запросы, которые дают результаты [журналов Azure Monitor,](../log-analytics/log-analytics-overview.md) и создать запросы, которые фильтруют результаты на основе ваших конкретных критериев. Например, можно найти сообщения с определенным контрольным номером обмена. Запросы используют [язык запроса Kusto,](https://aka.ms/LogAnalyticsLanguageReference)который можно отобразить, если вы хотите просмотреть различные результаты. Для получения дополнительной информации смотрите [запросы журнала Azure Monitor.](../azure-monitor/log-query/query-language.md)

## <a name="prerequisites"></a>Предварительные требования

* Рабочая область Log Analytics. См. дополнительные сведения о [создании рабочей области Log Analytics](../azure-monitor/learn/quick-create-workspace.md).

* Логическое приложение, настроенное с помощью журналов Azure Monitor и отправляет эту информацию в рабочее пространство Analytics журнала. [Узнайте, как настроить журналы Azure Monitor для приложения логики.](../logic-apps/monitor-logic-apps.md)

* Если вы используете учетную запись интеграции, убедитесь, что вы создали учетную запись с журналом Azure Monitor для отправки этой информации в рабочее пространство Analytics журнала. Узнайте, как [настроить журнал Azure Monitor для вашей учетной записи интеграции.](../logic-apps/monitor-b2b-messages-log-analytics.md)

## <a name="view-queries-behind-results"></a>Просмотр запросов за результатами

Для просмотра или отсвачения запроса, который производит результаты в резюме рабочего пространства, выполните следующие действия:

1. На любой странице результатов, внизу, выберите **See All**.

   ![Посмотреть все результаты](./media/create-monitoring-tracking-queries/logic-app-see-all.png)

   Страница журналов открывается и отображает запрос, стоящий за предыдущей страницей результатов.

   ![Страница журналов - представление запроса](./media/create-monitoring-tracking-queries/view-query-behind-results.png)

1. На странице **журналов** можно выбрать следующие параметры:

   * Для просмотра результатов запроса в виде таблицы под редактором запроса выберите **таблицу.**

   * Чтобы изменить запрос, обновите строку запроса и выберите **Run** для просмотра результатов в таблице.

## <a name="create-your-own-query"></a>Создайте свой собственный запрос

Чтобы найти или отфильтровать результаты на основе определенных свойств или значений, можно создать свой собственный запрос, начав с пустого запроса или использовав существующий запрос. Для получения дополнительной информации смотрите начало [с запросами журнала в Azure Monitor](../azure-monitor/log-query/get-started-queries.md).

1. На [портале Azure](https://portal.azure.com)найдите и выберите рабочее пространство для анализа журналов.

1. В меню рабочего пространства, под **общим и**свиной, выберите **журналы.**

1. Начните с пустого запроса или любых доступных существующих запросов.

   * Чтобы проверить, доступны ли какие-либо существующие запросы, на панели инструментов запроса выберите либо > **«Историю** **запросов»,** в которых отображаются запросы из предыдущих запусков запросов, или выберите **explorer запроса,** который показывает заранее построенные запросы.

     Например, решение Logic Apps B2B предоставляет следующие заранее построенные запросы:

     ![Начните с решения "Logic Apps B2B"](./media/create-monitoring-tracking-queries/b2b-prebuilt-queries.png)

   * Для начала с пустого запроса в редакторе запроса начните вводить [язык запроса Kusto](../azure-monitor/log-query/query-language.md) для вашего запроса.

     ![Начните с пустого запроса](./media/create-monitoring-tracking-queries/create-query-from-blank.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Схемы отслеживания AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)
* [Схемы отслеживания X12](../logic-apps/logic-apps-track-integration-account-x12-tracking-schema.md)
* [Настраиваемые схемы отслеживания](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md)