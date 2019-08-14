---
title: Анализ журналов действий Azure Active Directory с помощью журналов Azure Monitor | Документация Майкрософт
description: Узнайте, как анализировать журналы действий Azure Active Directory с помощью журналов Azure Monitor
services: active-directory
documentationcenter: ''
author: cawrites
manager: daveba
editor: ''
ms.assetid: 4535ae65-8591-41ba-9a7d-b7f00c574426
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: chadam
ms.reviewer: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 77eb03089d956d0fb32ef0463b3d1cdb49ff0dbb
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989814"
---
# <a name="analyze-azure-ad-activity-logs-with-azure-monitor-logs"></a>Анализ журналов действий Azure AD с помощью журналов Azure Monitor

После [интеграции журналов действий Azure AD с журналами Azure Monitor](howto-integrate-activity-logs-with-log-analytics.md) вы можете использовать возможности журналов Azure Monitor, чтобы получить полезные сведения о своей среде. Вы также можете установить [представления анализа журналов для журналов действий Azure AD ](howto-install-use-log-analytics-views.md), чтобы получить доступ к предварительно созданным отчетам о событиях аудита и входа в вашу среду.

В этой статье вы узнаете, как анализировать журналы действий Azure AD в рабочей области Log Analytics. 

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../../includes/azure-monitor-log-analytics-rebrand.md)]

## <a name="prerequisites"></a>предварительные требования 

Для работы вам потребуется следующее:

* Рабочая область Log Analytics в подписке Azure. Узнайте, [как создать рабочую область Log Analytics](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Сначала настройте передачу [журналов действий Azure AD в рабочую область Log Analytics](howto-integrate-activity-logs-with-log-analytics.md).

## <a name="navigate-to-the-log-analytics-workspace"></a>Переход к рабочей области Log Analytics

1. Войдите на [портале Azure](https://portal.azure.com). 

2. Выберите **Azure Active Directory**, а затем выберите **Журналы** из раздела **Мониторинг**, чтобы открыть рабочую область Log Analytics. Рабочая область откроется с запросом по умолчанию.

    ![Запрос по умолчанию](./media/howto-analyze-activity-logs-log-analytics/defaultquery.png)


## <a name="view-the-schema-for-azure-ad-activity-logs"></a>Просмотр схемы для журналов действий Azure AD

Журналы отправляются в таблицы **AuditLogs** и **SigninLogs** в рабочей области. Чтобы просмотреть схему для этих таблиц, выполните следующие действия.

1. В представлении запроса по умолчанию в предыдущем разделе выберите **Схему** и разверните рабочую область. 

2. Разверните раздел **Управление журналами**, а затем разверните **AuditLogs** или **SignInLogs**, чтобы просмотреть схему журнала.
    ![AuditLogs](./media/howto-analyze-activity-logs-log-analytics/auditlogschema.png)![SigninLogs](./media/howto-analyze-activity-logs-log-analytics/signinlogschema.png)

## <a name="query-the-azure-ad-activity-logs"></a>Запрос журналов действий Azure AD

Теперь, когда у вас есть журналы в вашей рабочей области, вы можете выполнять запросы к ним. Например, чтобы получить основные приложения, используемые на прошлой неделе, замените запрос по умолчанию следующим и выберите **Запустить**

```
SigninLogs 
| where CreatedDateTime >= ago(7d)
| summarize signInCount = count() by AppDisplayName 
| sort by signInCount desc 
```

Чтобы получить события аудита top за последнюю неделю, используйте следующий запрос.

```
AuditLogs 
| where TimeGenerated >= ago(7d)
| summarize auditCount = count() by OperationName 
| sort by auditCount desc 
```
## <a name="alert-on-azure-ad-activity-log-data"></a>Оповещение о данных журнала действий Azure AD

Можно также настроить оповещения по вашему запросу. Например, чтобы настроить оповещение, если на прошлой неделе было использовано более 10 приложений, выполните следующие действия.

1. В рабочей области выберите **Задание оповещения**, чтобы открыть страницу **Создание правила**.

    ![задание оповещения;](./media/howto-analyze-activity-logs-log-analytics/setalert.png)

2. Выберите **Критерии оповещений** по умолчанию, созданные в оповещении, и обновите **Пороговое значение** в метрике по умолчанию до 10.

    ![Критерии оповещений](./media/howto-analyze-activity-logs-log-analytics/alertcriteria.png)

3. Введите имя и описание оповещения и выберите уровень серьезности. В нашем примере мы можем задать значение**Информационный**.

4. Выберите **группу действий**, которая будет получать оповещения при возникновении сигнала. Вы можете уведомлять свою команду по электронной почте или с помощью текстового сообщения, или вы можете автоматизировать действие с помощью веб-перехватчика, Функций Azure или приложений логики. Дополнительные сведения см. в статье [Создание групп действий и управление ими на портале Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

5. После настройки оповещения, выберите **Создание оповещения**, чтобы включить его. 

## <a name="install-and-use-pre-built-views-for-azure-ad-activity-logs"></a>Установка и использование готовых представлений для журналов действий Azure AD

Вы также можете загрузить готовые представления анализа журналов для журналов действий Azure AD. Представления содержат несколько отчетов, касающихся общих сценариев, связанных с событиями аудита и регистрации. Вы также можете создать оповещение о любых данных, представленных в отчетах, выполнив действия, описанные в предыдущем разделе.

* **Azure AD Account Provisioning Events** (События подготовки учетных записей Azure AD). В этом представлении отображаются отчеты, связанные с аудитом действий подготовки, в том числе количество новых подготовленных пользователей и сбоев при подготовке, число обновленных пользователей и сбоев обновления, а также число отозванных пользователей и соответствующих сбоев.    
* **Sign-ins Events** (События входа в систему). В этом представлении отображаются наиболее важные отчеты, связанные с мониторингом действий входа в систему, например, число входов по приложениям, пользователям, устройствам, а также сводное представление для отслеживания числа входов со временем.
* **Users Performing Consent** (Пользователи, дающие согласие). В этом представлении отображаются отчеты, связанные с согласием пользователя, в том числе число согласий, предоставленных пользователем, число входов в систему пользователей, предоставивших согласие, а также число входов в систему всех приложений с доступом по согласию. 

См. сведения об [установке и использовании предоставлений анализа журналов для журналов действий Azure AD](howto-install-use-log-analytics-views.md). 


## <a name="next-steps"></a>Следующие шаги

* [Начало работы с запросами журналов Azure Monitor](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-queries)
* [Создание групп действий и управление ими на портале Azure](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups)
* [Установка и использование представлений Log Analytics для Azure Active Directory](howto-install-use-log-analytics-views.md)
