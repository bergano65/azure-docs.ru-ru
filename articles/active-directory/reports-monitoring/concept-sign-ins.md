---
title: Отчеты о действиях входа на портале Azure Active Directory | Документация Майкрософт
description: Общие сведения об отчетах о действиях входа на портале Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 4b18127b-d1d0-4bdc-8f9c-6a4c991c5f75
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 10/28/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e7b0c379783af2f9131d487f45c0f4e2009e258
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/20/2019
ms.locfileid: "74232146"
---
# <a name="sign-in-activity-reports-in-the-azure-active-directory-portal"></a>Отчеты о действиях входа на портале Azure Active Directory

Архитектура создания отчетов в Azure Active Directory (Azure AD) состоит из следующих компонентов:

- **Действие** 
    - **События входа**. Информация об использовании управляемых приложений и действиях входа пользователей.
    - **Audit logs** - [Audit logs](concept-audit-logs.md) provide system activity information about users and group management, managed applications, and directory activities.
- **Безопасность** 
    - **Risky sign-ins** - A [risky sign-in](concept-risky-sign-ins.md) is an indicator for a sign-in attempt by someone who isn't the legitimate owner of a user account.
    - **Пользователи, находящиеся в группе риска**. [Пользователи, находящиеся в группе риска](concept-user-at-risk.md) означает, что конфиденциальность учетной записи пользователя, возможно, нарушена.

This article gives you an overview of the sign-ins report.

## <a name="prerequisites"></a>Технические условия

### <a name="who-can-access-the-data"></a>Кто может получить доступ к данным?

* Users in the Security Administrator, Security Reader, Global Reader, and Report Reader roles
* Глобальные администраторы.
* Любой пользователь (не администратор) может получить доступ к своим данным о действиях входа. 

### <a name="what-azure-ad-license-do-you-need-to-access-sign-in-activity"></a>Какие лицензии Azure AD требуются для доступа к действию входа?

* Для просмотра отчета обо всех действиях входа с клиентом должна быть связана лицензия Azure AD Premium. See [Getting started with Azure Active Directory Premium](../fundamentals/active-directory-get-started-premium.md) to upgrade your Azure Active Directory edition. It will take a couple of days for the data to show up in the reports after you upgrade to a premium license with no data activities before the upgrade.

## <a name="sign-ins-report"></a>Отчет о входе

Отчет о событиях входа пользователя содержит ответы на следующие вопросы:

* Что такое шаблон входа пользователя?
* Сколько пользователей входили в течение недели?
* Каков статус их входа?

Start with [Azure portal](https://portal.azure.com). To access the sign-ins report select **Sign-ins**, continue to the **Monitoring.** It may take up to two hours for some sign-in records to show up in the portal.

![Sign-in activity](./media/concept-sign-ins/reporting-azure-sign-in-screen.png "Действия при входе")

> [!IMPORTANT]
> В отчете о событиях входа отображаются только **интерактивные** события входа, то есть когда пользователь выполняет вход вручную, указывая имя пользователя и пароль. В этом отчете не отображаются неинтерактивные события входа, такие как аутентификация между службами. 

Журнал событий входа содержит представление списка по умолчанию, в котором отображаются:

- дата входа;
- пользователь;
- The application the user has signed in to
- состояние входа;
- состояние обнаружения риска;
- состояние требования многофакторной проверки подлинности (MFA).

![Sign-in activity](./media/concept-sign-ins/sign-in-activity.png "Действия при входе")

Представление списка можно настроить, щелкнув **Столбцы** на панели инструментов.

![Sign-in activity](./media/concept-sign-ins/19.png "Действия при входе")

Displays additional fields or remove fields that are already displayed.

![Sign-in activity](./media/concept-sign-ins/02.png "Действия при входе")

Чтобы получить более подробную информацию, выберите элемент в списке.

![Sign-in activity](./media/concept-sign-ins/basic-sign-in.png "Действия при входе")

> [!NOTE]
> Customers can now troubleshoot Conditional Access policies through all sign-in reports. By clicking on the **Conditional Access** tab for a sign-in record, customers can review the Conditional Access status and dive into the details of the policies that applied to the sign-in and the result for each policy.
> Дополнительные сведения см. в разделе [Часто задаваемые вопросы об отчетах Azure Active Directory](reports-faq.md#conditional-access).



## <a name="filter-sign-in-activities"></a>Фильтрация действий входа

First, narrowing down the reported data to a level that works for you. Second, filter sign-ins data using date field as default filter. Azure AD provides you with a broad range of additional filters you can set.

![Sign-in activity](./media/concept-sign-ins/04.png "Действия при входе")

Фильтр по **пользователю** позволяет указать интересующее вас имя пользователя или имя участника-пользователя (UPN).

Фильтр по **приложению** позволяет указать имя интересующего вас приложения.

В фильтре по **состоянию входа** можно выбрать:

- Все
- Успешно
- Сбой

Фильтр **условного доступа** позволяет выбрать состояние политики ЦС для входа:

- Все
- Неприменимо
- Успешно
- Сбой

Фильтр **дат** позволяет определить интервал времени для возвращаемых данных.  
Возможные значения:

- One month
- 7 дней
- 24 часа
- Настраиваемый интервал

При выборе пользовательского интервала времени можно настроить время начала и окончания.

Если вы добавите дополнительные поля в представление входа, они автоматически добавляются в список фильтров. Например, добавив в свой список поле **клиентское приложение**, вы также получите еще один параметр фильтра, который позволит вам установить следующие фильтры:  
![Sign-in activity](./media/concept-sign-ins/12.png "Действия при входе")

- **"Обзор"**  
    This filter shows all events where sign-in attempts were attempted using browser flows.
- **Exchange ActiveSync (supported)**  
    This filter shows all sign-in attempts where the Exchange ActiveSync (EAS) protocol has been attempted from supported platforms like iOS, Android, and Windows Phone.
- **Exchange ActiveSync (unsupported)**  
    This filter shows all sign-in attempts where the EAS protocol has been attempted from unsupported platforms like, Linux distros.
- **Mobile Apps and Desktop clients** The filter shows all sign-in attempts that were not using browser flows. For example, mobile apps from any platform using any protocol or from Desktop client apps like Office on Windows or MacOS.
  
- **Other clients**
    - **IMAP**  
        A legacy mail client using IMAP to retrieve email.
    - **MAPI**  
        Office 2013, where ADAL is enabled and it is using MAPI.
    - **Old Office clients**  
        Office 2013 in its default configuration where ADAL is not enabled and it is using MAPI, or Office 2016 where ADAL has been disabled.
    - **POP**  
        A legacy mail client using POP3 to retrieve email.
    - **SMTP**  
        A legacy mail client using SMTP to send email.

## <a name="download-sign-in-activities"></a>Скачивание данных о действиях входа

Click the **Download** option to create a CSV or JSON file of the most recent 250,000 records. Start with [download the sign-ins data](quickstart-download-sign-in-report.md) if you want to work with it outside the Azure portal.  

![Загрузить](./media/concept-sign-ins/71.png "Download (Скачать)")

> [!IMPORTANT]
> На число записей, которые можно скачать, влияют особенности [политики хранения отчетов Azure Active Directory](reference-reports-data-retention.md).  


## <a name="sign-ins-data-shortcuts"></a>Точки для получения данных о входе

Azure AD and the Azure portal both provide you with additional entry points to sign-ins data:

- Обзор защиты удостоверений
- Пользователи
- Группы
- корпоративные приложения.

### <a name="users-sign-ins-data-in-identity-security-protection"></a>Данные событий входа пользователей в разделе защиты удостоверений

The user sign-in graph in the **Identity security protection** overview page shows weekly aggregations of sign-ins. The default for the time period is 30 days.

![Sign-in activity](./media/concept-sign-ins/06.png "Действия при входе")

Щелкнув день на графике входов, вы увидите обзор действий входа за этот день.

Каждая строка в списке действий входа содержит такие сведения:

* Кто выполнил вход?
* Какое приложение было целью входа?
* Каково состояние входа в систему?
* Каково состояние MFA входа в систему?

Щелкнув элемент, вы получите более подробную информацию об операции входа:

- Идентификатор пользователя.
- Пользователь
- Имя пользователя
- Идентификатор приложения
- Приложение
- Клиент
- Location
- IP-адрес
- Дата
- Требуется многофакторная идентификация
- состояние входа.

> [!NOTE]
> IP-адреса выдаются таким образом, что определенная связь между IP-адресом и физическим расположением компьютера с этим адресом отсутствует. Сопоставление IP-адресов осложняется тем, что поставщики мобильной связи и виртуальные частные сети выдают IP-адреса из центральных пулов, которые нередко находятся очень далеко от места, где фактически используется клиентское устройство. В настоящее время в отчетах Azure AD преобразование IP-адреса в физическое расположение не гарантируется и осуществляется на основе трассировок, данных реестра, обратных просмотров и других сведений.

На странице **Пользователи** можно получить полный обзор всех входов пользователей, щелкнув **Вход в систему** в разделе **Действия**.

![Sign-in activity](./media/concept-sign-ins/08.png "Действия при входе")

## <a name="usage-of-managed-applications"></a>Использование управляемых приложений

Представление данных входа, ориентированное на приложения, позволяет ответить на такие вопросы:

* Кто использует мои приложения?
* What are the top three applications in your organization?
* How is my newest application doing?

The entry point to this data is the top three applications in your organization. The data is contained within the last 30 days report in the **Overview** section under **Enterprise applications**.

![Sign-in activity](./media/concept-sign-ins/10.png "Действия при входе")

The app-usage graphs weekly aggregations of sign-ins for your top three applications in a given time period. который по умолчанию составляет 30 дней.

![Sign-in activity](./media/concept-sign-ins/graph-chart.png "Действия при входе")

Если нужно, вы можете переместить фокус на определенное приложение.

![Отчеты](./media/concept-sign-ins/single-app-usage-graph.png "Отчеты")

Щелкнув день на графике использования приложений, вы увидите подробный список действий входа.

С помощью параметра **Входов** можно полностью отобразить все события входа в ваши приложения.

## <a name="office-365-activity-logs"></a>Журналы действий Office 365

You can view Office 365 activity logs from the [Microsoft 365 admin center](https://docs.microsoft.com/office365/admin/admin-overview/about-the-admin-center). Consider the point  that, Office 365 activity and Azure AD activity logs share a significant number of the directory resources. Only the Microsoft 365 admin center provides a full view of the Office 365 activity logs. 

You can also access the Office 365 activity logs programmatically by using the [Office 365 Management APIs](https://docs.microsoft.com/office/office-365-management-api/office-365-management-apis-overview).

## <a name="next-steps"></a>Дальнейшие действия

* [Коды ошибок в отчете о действиях входа](reference-sign-ins-error-codes.md)
* [Политики хранения отчетов Azure Active Directory](reference-reports-data-retention.md)
* [Задержки в отчетах Azure Active Directory](reference-reports-latencies.md)

