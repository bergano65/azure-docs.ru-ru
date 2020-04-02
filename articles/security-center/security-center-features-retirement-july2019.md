---
title: Выход на пенсию функций Центра безопасности (июль 2019 г.) Документы Майкрософт
description: В этой статье описаны особенности Центра безопасности, которые были удалены 31 июля 2019 года.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: 1f923db5c730f6dec15f4fc211ab7acbd009ac7f
ms.sourcegitcommit: b0ff9c9d760a0426fd1226b909ab943e13ade330
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80521419"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Выход на пенсию функций Центра безопасности (июль 2019)

> [!NOTE]
> В этом документе подробно описан список функций, которые были удалены из Центра безопасности Azure 31 июля 2019 года.
>
>

За шесть месяцев, предшествовавших июлю 2019 года, мы внесли ряд [улучшений](https://azure.microsoft.com/updates/?product=security-center) в Центр безопасности Azure.
С помощью этих улучшенных возможностей мы удалили некоторые избыточные функции и связанные с ними AA из Центра безопасности 31 июля 2019 года.

Большинство из этих отставных функций можно заменить другими функциями в Центре безопасности Azure или Analytics журналов Azure. Другие функции могут быть реализованы с помощью [Azure Sentinel (предварительный просмотр).](https://azure.microsoft.com/services/azure-sentinel/)

Особенности Центра безопасности в отставке включают в себя:

- [Панель мониторинга событий](#menu_events)
- [Вход в меню поиска](#menu_search)
- [Просмотр классической ссылки «Идентификация & доступ» по identity и доступу (предварительный просмотр)](#menu_classicidentity)
- [Кнопка карты событий безопасности на карте предупреждений безопасности (предварительный просмотр)](#menu_securityeventsmap)
- [Правила пользовательского оповещения (предварительный просмотр)](#menu_customalerts)
- [Исследуйте кнопку в предупреждениях безопасности защиты от угроз](#menu_investigate)
- [Подмножество решений безопасности](#menu_solutions)
- [Оторитете конфигурации безопасности для политик безопасности](#menu_securityconfigurations)
- [Панель мониторинга безопасности и аудита (первоначально используемая на портале OMS) для рабочих областей анализа журналов](#menu_securityomsdashboard)

В этой статье содержится подробная информация для каждой вышедших из эксплуатации функций и шагов, которые можно предпринять для реализации функций замены.

## <a name="events-dashboard"></a>Панель мониторинга событий<a name="menu_events"></a>

Центр безопасности использует агент Log Analytics для сбора различных конфигураций и событий, связанных с безопасностью, с ваших компьютеров. Он хранит эти события в рабочих пространствах. Панель [мониторинга событий](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) позволяет просматривать эти данные и дает точку входа в Log Analytics.

Мы удалили панель мониторинга событий, которая появилась при выборе рабочего пространства:

![Панель мониторинга событий][2]

### <a name="events-dashboard---the-new-experience"></a>Панель мониторинга событий - новый опыт

Мы рекомендуем использовать нативные возможности Azure Log Analytics для просмотра заметных событий на рабочих пространствах.

Если вы создали пользовательские заметные события в Центре безопасности, они будут доступны. В журнале Analytics перейдите в **Select workspace** > **Saved Searches.** Ваши данные не будут потеряны или изменены. Родные заметные события также доступны с того же экрана в журнале Analytics.

![Рабочее пространство сохранено поиск][3]

## <a name="search-menu-entry"></a>Вход в меню поиска<a name="menu_search"></a>

Центр безопасности Azure в настоящее время использует поиск журналов Azure Monitor для получения и анализа данных безопасности. Этот экран служит окном для страницы поиска Log Analytics и позволяет пользователям выполнять поисковые запросы в выбранном рабочем пространстве. Для получения дополнительной [Azure Security Center search](https://docs.microsoft.com/azure/security-center/security-center-search)информации см. Мы удалили это окно поиска:

![Страница "Поиск"][4]

### <a name="search-menu-entry---the-new-experience"></a>Поиск меню ввода - новый опыт

Мы рекомендуем использовать нативные возможности Azure Log Analytics для выполнения поисковых запросов на рабочих пространствах. Перейдите к аналитике журналов Azure и выберите **журналы.**

![Страница журналов журналов журналов журналов][5]

## <a name="classic-identity--access-preview"></a>Доступ к классическим & (Предварительный просмотр)<a name="menu_classicidentity"></a>

Опыт классического & доступа в Центре безопасности в настоящее время показывает панель мониторинга идентификационных данных и информации о доступе в журналAnalytics. Для просмотра этой панели мониторинга:

1. Выберите **просмотр классического доступа & идентичности.**

   ![Страница идентификации][6]

1. Просмотр **панели мониторинга identity & доступа.**

    ![Страница идентификации - выбор рабочего пространства][7]

1. Выберите рабочее пространство для открытия панели мониторинга **Identity & доступа** в журнале Analytics для просмотра идентификационных данных и доступа к информации в рабочем пространстве.

   ![Страница идентификации - панель мониторинга][8]

Мы удалили все три экрана, показанные в предыдущих шагах. Ваши данные остаются доступными в решении безопасности Log Analytics и не были изменены или удалены.

### <a name="classic-identity--access-preview---the-new-experience"></a>Классический доступ & идентичности (Preview) - новый опыт

Панель мониторинга Log Analytics показала информацию об одном рабочем пространстве. Однако возможности центра безопасности «Родные» обеспечивают видимость всех подписок и всех связанных с ними рабочих областей. Вы можете получить доступ к простой в использовании представления, что позволяет сосредоточиться на том, что важно с рекомендациями, ранжированными в соответствии с их Безопасный счет.

Все функции панели мониторинга **Identity & доступа** в журнале Analytics можно достичь, выбрав identity & доступ **(Preview)** в Центре безопасности.

![Identity page - классический опыт выхода на пенсию][9]

## <a name="security-events-map"></a>Карта событий безопасности<a name="menu_securityeventsmap"></a>

Центр безопасности предоставляет [карту предупреждений безопасности,](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) чтобы помочь определить угрозы безопасности. Кнопка **«Перейти к событиям безопасности»** на этой карте открывает панель мониторинга, которая позволяет просматривать необработанные события безопасности на выбранном рабочем пространстве.

Мы удалили кнопку **карты событий безопасности** и панель мониторинга для рабочего пространства.

![Карта оповещений о безопасности - кнопка][10]

При выборе кнопки **«Перейти к событиям безопасности»** она открывает (теперь вышедшую в отставку) панель мониторинга угрозы.

![Панель мониторинга аналитики угроз][11]

При выборе рабочего пространства для просмотра панели мониторинга интеллекта угроз вы открываете (теперь вышедшие на пенсию) карту предупреждений безопасности (предварительный просмотр) экрана в журнале Analytics.

![Карта предупреждений безопасности в журнале Analytics][12]

Существующие данные остаются доступными в решении безопасности Log Analytics и не были изменены или удалены.

### <a name="security-events-map---the-new-experience"></a>Карта событий безопасности - новый опыт

Мы рекомендуем вам использовать функциональность карты оповещений, встроенную в Центр **безопасности: карта предупреждений безопасности (Preview)**. Эта функциональность обеспечивает оптимизированный опыт и работает во всех подписках и связанных с ними рабочих областях. Это дает вам представление высокого уровня в среде и не сосредоточено на одном рабочем пространстве.

## <a name="custom-alert-rules-preview"></a>Правила пользовательского оповещения (Предварительный просмотр)<a name="menu_customalerts"></a>

Мы [удалили пользовательские оповещения опыт](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 июня 2019, потому что его основной инфраструктуры был удален. После даты выхода на пенсию пользовательские оповещения безопасности больше не генерируются.
Рекомендуем включить [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) и воссоздать там пользовательские оповещения. Кроме того, можно создавать оповещения с помощью журналов Azure Monitor.

Для создания пользовательских оповещений с помощью Azure Sentinel:

1. [Откройте Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) и выберите рабочее пространство, где хранятся пользовательские оповещения
1. Выберите **Аналитику** из меню
1. Следуйте инструкциям в следующем [учебнике](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) о том, как создавать пользовательские оповещения в Azure Sentinel

Если вы не заинтересованы в использовании Azure Sentinel, вы можете создавать оповещения с помощью журналов Azure Monitor. Для инструкций смотрите [создание, представление и управление оповещениями о журналах с помощью](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) предупреждений Azure Monitor и [Log в Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log)

![Настраиваемые оповещения][13]

Для получения дополнительной информации о выходе на пенсию пользовательских оповещений можно в [Центре безопасности Azure (Preview)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## <a name="security-alerts-investigation"></a>Расследование предупреждений о безопасности<a name="menu_investigate"></a>

[Функция «Расследование»](https://docs.microsoft.com/azure/security-center/security-center-investigation) в Центре безопасности помогает сортировке потенциального инцидента с безопасностью. Функция позволяет понять масштаб инцидента и отследить его первопричину. Мы удалили эту функцию из Центра безопасности, потому что она была заменена улучшенным опытом работы в [Azure Sentinel.](https://azure.microsoft.com/services/azure-sentinel/)

![Инцидент][14]

При выборе кнопки **«Следствие»** на экране **инцидента безопасности** вы открываете панель мониторинга расследований (Preview) в журнале Analytics. Мы удалили следственный пункт.

Существующие данные остаются доступными в решении безопасности Log Analytics и не были изменены или удалены.

![Панель мониторинга исследования в журнале Analytics][15]

### <a name="investigation---the-new-experience"></a>Исследование - новый опыт

Мы рекомендуем вам перейти на [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) для получения богатого опыта исследования. Azure Sentinel предоставляет мощные инструменты поиска и запроса для поиска угроз безопасности в источниках данных организации.

## <a name="subset-of-security-solutions"></a>Подмножество решений безопасности<a name="menu_solutions"></a>

Центр безопасности может включить [интегрированные решения безопасности в Azure.](https://docs.microsoft.com/azure/security-center/security-center-partner-integration) Мы удалили следующие партнерские решения из Центра безопасности. Эти решения включены в [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) вместе с рядом дополнительных источников данных.

- [Решения для брандмауэра и веб-приложений нового поколения](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Интеграция решений безопасности, поддерживающих общий формат событий (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Защита идентификационных данных AD Azure](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

После выхода на пенсию нельзя добавлять или изменять любой из типов решений, упомянутых в предыдущем списке, ни из uI, ни из API. Центр безопасности Azure больше не будет открывать новые экземпляры этих партнерских решений.

Если у вас есть подключенные решения, мы рекомендуем вам перейти на Azure Sentinel.

![Решения центров безопасности][16]

## <a name="edit-security-configurations-for-security-policies"></a>Оторитете конфигурации безопасности для политик безопасности<a name="menu_securityconfigurations"></a>

Центр безопасности Azure отслеживает конфигурации безопасности на основе [более 150 рекомендуемых правил](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) усиления защиты операционной системы. Эти правила относятся к брандмауэрам, аудиту, политикам паролей и многое другое. Если конфигурация компьютера уязвима к атакам, центр безопасности формирует рекомендации по безопасности. [Экран конфигурации настройки элемента безопасности](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) позволяет клиентам настроить конфигурацию безопасности ОС по умолчанию в Центре безопасности.

Мы удалили эту функцию предварительного просмотра. Чтобы сбросить конфигурации безопасности обратно к значениям по умолчанию после даты выхода на пенсию, сделайте это через API или Powershell, используя [следующие инструкции.](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20scripts/Reset%20security%20configurations%20customization)

![Оторитнастройка конфигураций безопасности][17]

### <a name="edit-security-configurations---the-new-experience"></a>Оторитете конфигурации безопасности - новый опыт

Мы намерены предоставить Центру безопасности поддержку [агента конфигурации гостей.](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) Такое обновление позволит создать гораздо более богатый набор функций, включая поддержку большего числа операционных систем и интеграцию политик Azure в гостях для конфигураций гостей. После включения этих изменений у вас также будет возможность управлять конфигурациями в масштабе и автоматически применять их к новым ресурсам.

## <a name="security-and-audit-dashboard-for-log-analytics-workspaces"></a>Панель мониторинга безопасности и аудита для рабочих областей анализа журналов<a name="menu_securityomsdashboard"></a>

Панель мониторинга безопасности и аудита первоначально использовалась на портале OMS. В журнале Log Analytics панель мониторинга предоставляет обзор важных событий и угроз безопасности в рабочей области, карту разведки угроз и оценку событий безопасности, сохраненных в рабочей области. Мы удалили панель мониторинга. Как мы уже рекомендовали в панели мониторинга, мы советуем вам перейти к Центру безопасности Azure.

![Панель мониторинга безопасности журнала Analytics][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Панель мониторинга безопасности и аудита - новый опыт

Мы советуем вам перейти на Центр безопасности Azure. Он обеспечивает один и тот же обзор безопасности в нескольких подписках и связанных с ними рабочих областях, а также более богатый набор функций.

В [репозитории GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) для Центра безопасности можно получить исходные запросы Log Analytics, заполняющие панель мониторинга и аудита.

## <a name="next-steps"></a>Дальнейшие действия

- Узнайте больше о [Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/).
- Узнайте больше о [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

<!--Image references - events-->
[1]: ./media/security-center-features-retirement-july2019/asc_events_dashboard.png
[2]: ./media/security-center-features-retirement-july2019/asc_events_dashboard_inner.png
[3]: ./media/security-center-features-retirement-july2019/workspace_saved_searches.png
<!--Image references - search-->
[4]: ./media/security-center-features-retirement-july2019/asc_search.png
[5]: ./media/security-center-features-retirement-july2019/workspace_logs.png
<!--Image references - classic identity and access-->
[6]: ./media/security-center-features-retirement-july2019/asc_identity.png
[7]: ./media/security-center-features-retirement-july2019/asc_identity_workspace_selection.png
[8]: ./media/security-center-features-retirement-july2019/loganalytics_dashboard_identity.png
[9]: ./media/security-center-features-retirement-july2019/asc_identity_nobuttonhighlight.png
<!--Image references - alerts map-->
[10]: ./media/security-center-features-retirement-july2019/asc_security_alerts_map.png
[11]: ./media/security-center-features-retirement-july2019/asc_threat_intellignece_dashboard.png
[12]: ./media/security-center-features-retirement-july2019/loganalytics_security_alerts_map.png
<!--Image references - custom alerts-->
[13]: ./media/security-center-features-retirement-july2019/asc_custom_alerts.png
<!--Image references - Investigation-->
[14]: ./media/security-center-features-retirement-july2019/asc-security-incident.png
[15]: ./media/security-center-features-retirement-july2019/loganalytics_investigation_dashboard.png
<!--Image references - Solutions-->
[16]: ./media/security-center-features-retirement-july2019/asc_security_solutions.png
<!--Image references - Edit security configurations-->
[17]: ./media/security-center-features-retirement-july2019/asc_edit_security_configurations.png
<!--Image references - Security dashboard in log analytics-->
[18]: ./media/security-center-features-retirement-july2019/loganalytics_security_dashboard.png
