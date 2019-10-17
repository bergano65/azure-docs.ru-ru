---
title: Выбытие средств центра безопасности (Июль 2019) | Документация Майкрософт
description: В этой статье описываются функции центра безопасности, которые были прекращены 31 июля 2019 г.
services: security-center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: article
ms.date: 09/10/2019
ms.author: memildin
ms.openlocfilehash: c4fe185f5d203b1b0eba306c80cded14ad82bc98
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438630"
---
# <a name="retirement-of-security-center-features-july-2019"></a>Выбытие средств центра безопасности (Июль 2019)

> [!NOTE]
> В этом документе описывается список функций, которые были удалены из центра безопасности Azure 31 июля 2019.
>
>

Мы внесли несколько [улучшений](https://azure.microsoft.com/updates/?product=security-center) в центр безопасности Azure за шесть месяцев до июля 2019.
С помощью этих улучшенных возможностей мы удалили некоторые избыточные функции и связанные API из центра безопасности 31 июля 2019.

Большинство из этих прекращенных функций можно заменить другими функциями в центре безопасности Azure или Azure Log Analytics. Другие функции можно реализовать с помощью [Azure Sentinel (Предварительная версия)](https://azure.microsoft.com/services/azure-sentinel/).

Устаревшие функции центра безопасности включают:

- [Панель мониторинга событий](#menu_events)
- [Запись меню "Поиск"](#menu_search)
- [Ссылка на классический идентификатор & доступом по удостоверениям и доступу (Предварительная версия)](#menu_classicidentity)
- [Кнопка "схема событий безопасности" на карте оповещений системы безопасности (Предварительная версия)](#menu_securityeventsmap)
- [Настраиваемые правила генерации оповещений (Предварительная версия)](#menu_customalerts)
- [Кнопка "исследовать" в оповещениях системы безопасности защиты от угроз](#menu_investigate)
- [Подмножество решений по обеспечению безопасности](#menu_solutions)
- [Изменение конфигураций безопасности для политик безопасности](#menu_securityconfigurations)
- [Панель мониторинга безопасности и аудита (изначально используется на портале OMS) для рабочих областей Log Analytics](#menu_securityomsdashboard)

В этой статье содержатся подробные сведения о каждой устаревшей функции и действия, которые можно предпринять для реализации функций замены.

## Панель мониторинга событий<a name="menu_events"></a>

Центр безопасности использует Microsoft Monitoring Agent для объединения различных конфигураций и событий, связанных с безопасностью, с компьютеров. Эти события сохраняются в рабочих областях. [Панель мониторинга событий](https://docs.microsoft.com/azure/security-center/security-center-events-dashboard) позволяет просматривать эти данные и дает точку входа для log Analytics.

Мы вышли из панели мониторинга события, которая отображалась при выборе рабочей области:

![Панель мониторинга событий][2]

### <a name="events-dashboard---the-new-experience"></a>Панель мониторинга событий — новый интерфейс

Мы рекомендуем использовать собственные возможности Azure Log Analytics для просмотра важных событий в рабочих областях.

Если вы создали пользовательские важные события в центре безопасности, они будут доступны. В Log Analytics перейдите в раздел **Выбор рабочей области** > **сохраненные поисковые запросы**. Ваши данные не будут потеряны или изменены. Собственные важные события также доступны на одном экране в Log Analytics.

![Сохраненные поиски в рабочей области][3]

## Запись меню "Поиск"<a name="menu_search"></a>

Центр безопасности Azure в настоящее время использует Azure Monitor Поиск журналов для извлечения и анализа данных безопасности. Этот экран служит в качестве окна Log Analytics странице поиска и позволяет пользователям выполнять поисковые запросы в выбранной рабочей области. Дополнительные сведения см. в разделе [Поиск центра безопасности Azure](https://docs.microsoft.com/azure/security-center/security-center-search). Мы вышли из этого окна поиска:

![Страница "Поиск"][4]

### <a name="search-menu-entry---the-new-experience"></a>Запись меню "Поиск" — новый интерфейс

Мы рекомендуем использовать собственные возможности Azure Log Analytics для выполнения поисковых запросов в рабочих областях. Перейдите в Log Analytics Azure и выберите **журналы**.

![Страница журналов Log Analytics][5]

## Классическая идентификация & доступом (Предварительная версия)<a name="menu_classicidentity"></a>

Классическая идентификация & доступа в центре безопасности в настоящее время отображает панель мониторинга удостоверений и сведений о доступе в Log Analytics. Чтобы просмотреть эту панель мониторинга, сделайте следующее:

1. Выберите **Просмотр классической идентификации & доступ**.

   ![Страница удостоверений][6]

1. Просмотрите **панель мониторинга доступа & удостоверений**.

    ![Страница удостоверений — выбор рабочей области][7]

1. Выберите рабочую область, чтобы открыть панель мониторинга **удостоверений & доступа** в log Analytics, чтобы просмотреть сведения об удостоверении и доступе в рабочей области.

   ![Страница удостоверений — панель мониторинга][8]

Мы вышли из эксплуатации все три экрана, показанные на предыдущих шагах. Данные останутся доступными в решении Log Analytics безопасности и не были изменены или удалены.

### <a name="classic-identity--access-preview---the-new-experience"></a>Классическая идентификация & доступом (Предварительная версия) — новый интерфейс

На панели мониторинга Log Analytics показана аналитика в одной рабочей области. Однако собственные возможности центра безопасности обеспечивают видимость всех подписок и всех связанных с ними рабочих областей. Вы можете получить доступ к удобному для использования представлению, которое позволяет сосредоточиться на том, что важно для рекомендаций, упорядоченных в соответствии с их безопасностью.

Все функции панели мониторинга **доступа & удостоверений** в log Analytics можно получить, выбрав **удостоверение & доступ (Предварительная версия)** в центре безопасности.

![Страница "Идентификация" — классическое прекращение работы][9]

## Схема событий безопасности<a name="menu_securityeventsmap"></a>

Центр безопасности предоставляет [карту предупреждений системы безопасности](https://docs.microsoft.com/azure/security-center/security-center-threat-intel) для выявления угроз безопасности. Кнопка **Перейти к сопоставлению событий безопасности** на этой карте открывает панель мониторинга, которая позволяет просматривать необработанные события безопасности в выбранной рабочей области.

Мы удалили кнопку **Переход к сопоставлению событий безопасности** и панель мониторинга для каждой рабочей области.

![Кнопка "схема оповещений системы безопасности"][10]

При нажатии кнопки **"переход к сопоставлению событий безопасности** " откроется панель мониторинга "аналитика угроз", в которой она была снята.

![панель мониторинга для анализа угроз;][11]

При выборе рабочей области для просмотра ее панели мониторинга "аналитика угроз" вы открыли (теперь она уже снята) экран "схема оповещений системы безопасности" (Предварительная версия) в Log Analytics.

![Оповещения системы безопасности сопоставлены в Log Analytics][12]

Существующие данные останутся доступными в решении Log Analytics безопасности и не были изменены или удалены.

### <a name="security-events-map---the-new-experience"></a>Схема событий безопасности — новый интерфейс

Мы рекомендуем использовать встроенную функцию карт оповещений в центре безопасности: **схема оповещений системы безопасности (Предварительная версия)** . Эта функция обеспечивает оптимизированный интерфейс и работает во всех подписках и связанных рабочих областях. Она обеспечивает высокоуровневое представление в среде и не ориентировано на отдельную рабочую область.

## Настраиваемые правила генерации оповещений (Предварительная версия)<a name="menu_customalerts"></a>

Мы отменяли [Пользовательский интерфейс оповещений](https://docs.microsoft.com/azure/security-center/security-center-custom-alert) 30 июня 2019, так как его базовая инфраструктура была снята с учета. После даты выбытия пользовательские оповещения системы безопасности больше не создаются.
Мы рекомендуем включить [метку Azure](https://azure.microsoft.com/services/azure-sentinel/) и повторно создать пользовательские оповещения. Кроме того, вы можете создавать оповещения с Azure Monitor оповещениями журналов.

Чтобы создать настраиваемые оповещения с помощью Azure Sentinel, сделайте следующее:

1. [Откройте Azure Sentinel](https://portal.azure.com/#create/Microsoft.ASI/preview) и выберите рабочую область, в которой хранятся настраиваемые оповещения.
1. В меню выберите пункт **аналитика** .
1. Следуйте инструкциям в следующем [руководстве](https://docs.microsoft.com/azure/sentinel/tutorial-detect-threats) по созданию настраиваемых оповещений в Azure Sentinel.

Если вы не хотите использовать метку Azure, вы можете создавать оповещения с Azure Monitor оповещениями журналов. Инструкции см. в разделе [Создание, просмотр и Управление оповещениями журнала с помощью Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-log) и [оповещений журнала в Azure Monitor](https://docs.microsoft.com/azure/azure-monitor/platform/alerts-unified-log).

![Настраиваемые оповещения][13]

Дополнительные сведения о прекращении использования пользовательских оповещений см. [в статье настраиваемые правила генерации оповещений в центре безопасности Azure (Предварительная версия)](https://docs.microsoft.com/azure/security-center/security-center-custom-alert).

## Исследование оповещений системы безопасности<a name="menu_investigate"></a>

[Функция расследования](https://docs.microsoft.com/azure/security-center/security-center-investigation) в центре безопасности помогает рассмотреть потенциальный инцидент безопасности. Эта функция позволяет понять область инцидента и оценить ее основную причину. Эта функция была удалена из центра безопасности, так как она была заменена более удобной в [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/).

![Инцидент][14]

Когда вы наберете кнопку **исследовать** на экране **инцидента безопасности** , вы открываете панель мониторинга для расследования (Предварительная версия) в log Analytics. Мы вышли из эксплуатации панель мониторинга для расследования.

Существующие данные останутся доступными в решении Log Analytics безопасности и не были изменены или удалены.

![Исследование панели мониторинга в Log Analytics][15]

### <a name="investigation---the-new-experience"></a>Исследование — новый интерфейс

Мы рекомендуем перейти на [метку Azure](https://azure.microsoft.com/services/azure-sentinel/) , чтобы получить широкие возможности для расследования. Azure Sentinel предоставляет мощные средства поиска и запросов, позволяющие находить угрозы безопасности в источниках данных Организации.

## Подмножество решений по обеспечению безопасности<a name="menu_solutions"></a>

Центр безопасности может включать [интегрированные решения безопасности в Azure](https://docs.microsoft.com/azure/security-center/security-center-partner-integration). Мы вышли из центра безопасности следующие решения партнеров. Эти решения включены в [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/) вместе с несколькими дополнительными источниками данных.

- [Брандмауэр следующего поколения и брандмауэры веб-приложений](https://docs.microsoft.com/azure/sentinel/connect-data-sources)
- [Интеграция решений безопасности, поддерживающих общий формат событий (CEF)](https://docs.microsoft.com/azure/sentinel/connect-common-event-format)
- [Microsoft Advanced Threat Analytics](https://docs.microsoft.com/azure/sentinel/connect-azure-atp)
- [Защита идентификации Azure AD](https://docs.microsoft.com/azure/sentinel/connect-azure-ad-identity-protection)

После выхода из системы вы не сможете добавить или изменить типы решений, упомянутые в предыдущем списке, из пользовательского интерфейса или API. Центр безопасности Azure больше не будет обнаруживать новые экземпляры этих решений партнеров.

Если у вас есть подключенные решения, мы рекомендуем перейти к Azure Sentinel.

![Решения для центров безопасности][16]

## Изменение конфигураций безопасности для политик безопасности<a name="menu_securityconfigurations"></a>

Центр безопасности Azure отслеживает конфигурации безопасности на основе [более 150 рекомендуемых правил](https://gallery.technet.microsoft.com/Azure-Security-Center-a789e335) усиления защиты операционной системы. Эти правила относятся к брандмауэрам, аудиту, политикам паролей и т. д. Если конфигурация компьютера уязвима к атакам, центр безопасности формирует рекомендации по безопасности. [Экран изменение конфигурации безопасности](https://docs.microsoft.com/azure/security-center/security-center-customize-os-security-config) позволяет пользователям настраивать конфигурацию безопасности ОС по умолчанию в центре безопасности.

Мы вышли из этой предварительной версии функции. Чтобы восстановить параметры безопасности по умолчанию после даты выбытия, сделайте это через API или PowerShell, выполнив [следующие инструкции](https://github.com/Azure/Azure-Security-Center/tree/master/Powershell%20samples/Reset%20security%20configurations%20customization) .

![Изменение конфигураций безопасности][17]

### <a name="edit-security-configurations---the-new-experience"></a>Изменение конфигураций безопасности — новый интерфейс

Мы планируем включить поддержку [агента гостевой конфигурации](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)в центре безопасности. Такое обновление позволит расширить набор функций, включая поддержку дополнительных операционных систем и интеграцию политик Azure в гостевой конфигурации для гостевых конфигураций. После включения этих изменений вы также сможете управлять конфигурациями в масштабе и автоматически применять их к новым ресурсам.

## Панель мониторинга "безопасность и аудит" для рабочих областей Log Analytics<a name="menu_securityomsdashboard"></a>

Панель мониторинга безопасность и аудит изначально использовалась на портале OMS. В Log Analytics панель мониторинга предоставляет общие сведения о важных событиях и угрозах безопасности, карте аналитики угроз и оценке событий безопасности, сохраненных в рабочей области. Панель мониторинга удалена. Как мы уже рекомендовали в пользовательском интерфейсе панели мониторинга, мы рекомендуем перейти на центр безопасности Azure.

![Панель мониторинга Log Analytics безопасности][18]

### <a name="security-and-audit-dashboard---the-new-experience"></a>Панель мониторинга "безопасность и аудит" — новый интерфейс

Мы рекомендуем переключиться в центр безопасности Azure. Он предоставляет те же обзор безопасности для нескольких подписок и связанных с ними рабочих областей, а также более широкий набор функций.

Вы можете получить исходные Log Analytics запросы, которые заполняют панель мониторинга безопасность и аудит в [репозитории GitHub](https://github.com/Azure/Azure-Security-Center/tree/master/Legacy%20Log%20Analytics%20dashboards) для центра безопасности.

## <a name="next-steps"></a>Дальнейшие действия

- См. дополнительные сведения о [Центре безопасности Azure](https://docs.microsoft.com/azure/security-center/).
- Дополнительные сведения об [Azure Sentinel](https://docs.microsoft.com/azure/sentinel).

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
