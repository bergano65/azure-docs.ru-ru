---
title: Мониторинг работоспособности входа в приложение для обеспечения устойчивости в Azure Active Directory
description: Создавайте запросы и уведомления, чтобы отслеживать работоспособность при входе в приложения.
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 01/10/2021
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: 40bfa27dba905cb2e9a363c7739f0a43e7c2afdf
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/10/2021
ms.locfileid: "100101376"
---
# <a name="monitoring-application-sign-in-health-for-resilience"></a>Мониторинг работоспособности входа в приложение для обеспечения устойчивости

Чтобы повысить устойчивость инфраструктуры, настройте мониторинг работоспособности входа приложения для критически важных приложений, чтобы получать оповещение при возникновении затронутых инцидентов. Чтобы помочь вам в этом, можно настроить оповещения на основе книги работоспособности входа. 

Эта книга позволяет администраторам отслеживать запросы проверки подлинности для приложений в клиенте. Он предоставляет следующие основные возможности:

* Настройте книгу для отслеживания всех или отдельных приложений с данными практически в реальном времени.

* Настройте оповещения, чтобы получать уведомления при изменении шаблонов проверки подлинности, чтобы можно было исследовать и предпринимать действия.

* Сравните тенденции за период, например неделю по неделям, который является значением по умолчанию для книги.

> [!NOTE]
> Чтобы просмотреть все доступные книги и необходимые условия для их использования, см. статью [использование Azure Monitor книг для отчетов](../reports-monitoring/howto-use-azure-monitor-workbooks.md).

Во время события, влияющего на событие, могут возникнуть две вещи:

* Число входов в приложение может удалить стремительно, так как пользователи не могут войти в систему.

* Число неудачных попыток входа может увеличиться. 

В этой статье описано, как настроить книгу "работоспособность при входе", чтобы отслеживать нарушения входа пользователей в систему.

## <a name="prerequisites"></a>Предварительные требования 

* Клиент Azure AD.

* Пользователь с ролью глобального администратора или администратора безопасности для клиента Azure AD.

* Log Analytics рабочей области в подписке Azure для отправки журналов в журналы Azure Monitor. 

   * Узнайте, как [создать рабочую область log Analytics](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)

* Журналы Azure AD, интегрированные с журналами Azure Monitor

   * Узнайте, как [интегрировать журналы входа Azure AD с Azure Monitor потоком.](../reports-monitoring/howto-integrate-activity-logs-with-log-analytics.md)

 

## <a name="configure-the-app-sign-in-health-workbook"></a>Настройка книги работоспособности входа в приложение 

Чтобы получить доступ к книгам, откройте **портал Azure**, выберите **Azure Active Directory**, а затем выберите **книги**.

Вы увидите книги под сведениями об использовании, условном доступе и устранении неполадок. В разделе "использование" появится книга "работоспособность входа в приложение".

После использования книги она может появиться в разделе недавно измененные книги.

![Снимок экрана, показывающий коллекцию книг в портал Azure.](./media/monitor-sign-in-health-for-resilience/sign-in-health-workbook.png)


Книга работоспособности входа в приложение позволяет визуализировать, что происходит с вашими входами. 

По умолчанию в книге представлены два графика. Эти графы сравнивают, что происходит с вашими приложениями сейчас, и один и тот же период в неделю назад. Синие линии являются текущими, а оранжевые — предыдущей неделей.

![Снимок экрана, показывающий графы работоспособности входа.](./media/monitor-sign-in-health-for-resilience/sign-in-health-graphs.png)

**Первый граф — это почасовое использование (число успешных пользователей)**. Сравнение текущего числа успешных пользователей с обычным периодом использования позволяет выявить удаление, которое может потребовать изучения. Сброс скорости использования может помочь выявить проблемы с производительностью и использованием, которые не могут быть выполнены в результате сбоя. Например, если пользователи не могут подключиться к приложению, чтобы попытаться войти в систему, не будет никаких сбоев, а только с использованием перетаскивания. Пример запроса для этих данных можно найти в следующем разделе.

Второй график — это Почасовая частота сбоев. Пиковая частота сбоев может указывать на проблему с механизмами проверки подлинности. Частота сбоев может измеряться только в том случае, если пользователи могут попытаться пройти проверку подлинности. Если пользователи не могут получить доступ для выполнения попытки, ошибки не будут отображаться.

Вы можете настроить оповещение, уведомляющее определенную группу, если частота использования или сбоев превышает заданное пороговое значение. Пример запроса для этих данных можно найти в следующем разделе.

 ## <a name="configure-the-query-and-alerts"></a>Настройка запроса и оповещений

Правила генерации оповещений создаются в Azure Monitor и могут автоматически выполнять сохраненные запросы или поиск по журналу с регулярными интервалами.

Используйте следующие инструкции для создания оповещений по электронной почте на основе запросов, отраженных на диаграммах. Примеры сценариев ниже отправляют уведомление по электронной почте, когда

* Успешное использование падает на 90% за один час назад, как показано на графике почасового использования в предыдущем разделе. 

* Частота сбоев увеличивается на 90% за один час назад, как показано на диаграмме почасового сбоя в предыдущем разделе. 

 Чтобы настроить базовый запрос и настроить оповещения, выполните следующие действия. Пример запроса будет использоваться в качестве базиса для вашей конфигурации. Описание структуры запроса отображается в конце этого раздела.

Дополнительные сведения о создании, просмотре и управлении оповещениями журнала с помощью Azure Monitor см. в статье [Управление оповещениями журнала](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log).

 
1. В книге выберите **изменить**, а затем щелкните **значок запроса** над правой стороны диаграммы.   

   [![Снимок экрана с командой "изменить книгу".](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)](./media/monitor-sign-in-health-for-resilience/edit-workbook.png)

   Откроется журнал запросов.

  [![Снимок экрана, показывающий журнал запросов.](./media/monitor-sign-in-health-for-resilience/query-log.png)](/media/monitor-sign-in-health-for-resilience/query-log.png)
‎

2. Скопируйте один из следующих примеров скриптов для нового запроса Kusto.

**Kusto запрос на удаление из использования**

```Kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1h)

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize users = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1h) - totimespan(2d))..(now() - totimespan(2d)))

| project TimeGenerated, AppDisplayName, UserPrincipalName

//| where AppDisplayName contains "Office 365 Exchange Online"

| summarize usersPriorWeek = dcount(UserPrincipalName) by bin(TimeGenerated, 1hr)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join

(

 lastWeek

)

on rn

| project TimeGenerated, users, usersPriorWeek, difference = abs(users - usersPriorWeek), max = max_of(users, usersPriorWeek)

| where (difference * 2.0) / max > 0.9

```

 

**Kusto запрос на увеличение частоты сбоев**


```kusto

let thisWeek = SigninLogs

| where TimeGenerated > ago(1 h)

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRate = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

let lastWeek = SigninLogs

| where TimeGenerated between((ago(1 h) - totimespan(2d))..(ago(1h) - totimespan(2d)))

| project TimeGenerated, UserPrincipalName, AppDisplayName, status = case(Status.errorCode == "0", "success", "failure")

| where AppDisplayName == **APP NAME**

| summarize success = countif(status == "success"), failure = countif(status == "failure") by bin(TimeGenerated, 1h)

| project TimeGenerated, failureRatePriorWeek = (failure * 1.0) / ((failure + success) * 1.0)

| sort by TimeGenerated desc

| serialize rn = row_number();

thisWeek

| join (lastWeek) on rn

| project TimeGenerated, failureRate, failureRatePriorWeek

| where abs(failureRate – failureRatePriorWeek) > **THRESHOLD VALUE**

```

3. Вставьте запрос в окно и выберите Run ( **выполнить**). Убедитесь, что отображается сообщение завершено, показанное на рисунке ниже, и результаты ниже этого сообщения.

   [![Снимок экрана, показывающий результаты выполнения запроса.](./media/monitor-sign-in-health-for-resilience/run-query.png)](./media/monitor-sign-in-health-for-resilience/run-query.png)

4. Выделите запрос и выберите + **новое правило генерации оповещений**. 
 
   [![Снимок экрана, показывающий новый экран правила генерации оповещений.](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/new-alert-rule.png)


5. Настройте условия оповещения. В разделе условие выберите ссылку, **Если среднее значение пользовательского поиска по журналам больше, чем число, заданное логическим значением**. В области Настройка логики сигнала прокрутите до логики оповещений.

   [![Снимок экрана, на котором показан экран настройки оповещений.](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)](./media/monitor-sign-in-health-for-resilience/configure-alerts.png)
 
   * **Пороговое значение**: 0. Это значение будет оповещать о любых результатах.

   * **Период оценки (в минутах)**: 60. Это значение отображается в течение часа.

   * **Частота (в минутах)**: 60. Это значение устанавливает период оценки один раз в час за предыдущий час.

   * Нажмите кнопку **Готово**.

6. В разделе **действия** настройте следующие параметры.  

    [![Снимок экрана, показывающий страницу создания правила генерации оповещений.](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)](./media/monitor-sign-in-health-for-resilience/create-alert-rule.png)

   * В разделе **действия** выберите **пункт выбрать группу действий** и добавьте группу, для которой необходимо получать уведомления об оповещениях.

   * В разделе **Настройка действий** выберите **оповещения по электронной почте**.

   * Добавьте **строку темы**.

7. В разделе **сведения о правиле оповещений** настройте следующие параметры.

   * Добавьте описательное имя и описание.

   * Выберите **группу ресурсов** , в которую нужно добавить оповещение.

   * Выберите **уровень серьезности** предупреждения по умолчанию.

   * Установите флажок **включить правило генерации оповещений при создании** , если хотите немедленно выполнить его, или выберите **Отключить оповещения**.

8. Выберите **Создать правило генерации оповещений**.

9. Нажмите кнопку **сохранить**, введите имя запроса, **Сохраните как запрос с категорией оповещений**. Затем нажмите кнопку **сохранить** еще раз.  

   [![Снимок экрана, на котором отображается кнопка "сохранить запрос".](./media/monitor-sign-in-health-for-resilience/save-query.png)](./media/monitor-sign-in-health-for-resilience/save-query.png)



### <a name="refine-your-queries-and-alerts"></a>Уточнение запросов и оповещений
Измените запросы и оповещения для максимальной эффективности.

* Обязательно протестируйте оповещения.

* Измените чувствительность и частоту оповещения, чтобы получать важные уведомления. Администраторы могут стать десенситизед оповещениями, если они получат слишком много и не пропустили что-то важное. 

* Убедитесь, что в список разрешенных отправителей Добавлено сообщение электронной почты, из которого оповещения поступают в почтовые клиенты администратора. В противном случае вы можете пропустить уведомления из-за фильтра нежелательной почты в почтовом клиенте. 

* Запрос "оповещения" в Azure Monitor может содержать только результаты за последние 48 часов. [Это текущее ограничение по проекту](https://github.com/MicrosoftDocs/azure-docs/issues/22637).

## <a name="create-processes-to-manage-alerts"></a>Создание процессов для управления оповещениями

После настройки запроса и оповещений создайте бизнес-процессы для управления оповещениями.

* Кто будет отслеживать книгу и когда?
* Кто будет изучать предупреждение при создании оповещения?

* Каковы потребности в обмене данными? Кто будет создавать связь и кто получит их?

* Если происходит сбой, какие бизнес-процессы необходимо активировать?

## <a name="next-steps"></a>Дальнейшие действия

[Дополнительные сведения о книгах](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-use-azure-monitor-workbooks)

 

 

 
