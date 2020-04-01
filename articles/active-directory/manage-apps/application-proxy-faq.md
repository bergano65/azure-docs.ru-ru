---
title: Прокси-приложение Azure AD Proxy часто задавало вопросы Документы Майкрософт
description: Узнайте ответы на часто задаваемые вопросы (Часто задаваемые вопросы) об использовании приложения Azure AD Proxy для публикации внутренних приложений для удаленных пользователей.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: 31587777ba22dd8b4cebf81f0ff98979bb30fade
ms.sourcegitcommit: 27bbda320225c2c2a43ac370b604432679a6a7c0
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/31/2020
ms.locfileid: "80410715"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Прокси-сервер приложения Active Directory (Azure AD) часто задавал вопросы

Эта страница часто отвечает на часто задаваемые вопросы о прокси-сервере приложения Azure Active Directory (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Включение прокси приложения Azure AD

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Какая лицензия необходима для использования Azure AD Application Proxy?

Чтобы использовать прокси-приложение Azure AD, необходимо иметь лицензию Azure AD Premium P1 или P2. Для получения дополнительной информации о лицензировании [см.](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Почему кнопка "Включить приложение Прокси серый?

Убедитесь, что у вас есть по крайней мере лицензия Azure AD Premium P1 или P2 и установлен прокси-коннектор приложения Azure AD. После успешной установки первого разъема служба Azure AD Application Proxy будет включена автоматически.

## <a name="connector-configuration"></a>Конфигурация соединителя

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Могут ли службы Application Proxy Connector работать в другом контексте пользователя, чем по умолчанию?

Нет, этот сценарий не поддерживается. Параметры по умолчанию:

- Microsoft AAD приложение Прокси-коннектор - WAPCSvc - Сетевая служба
- Microsoft AAD Приложение Прокси-коннектор Обновление - WAPCUpdaterSvc - NT Органа

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Мое приложение бэк-энда размещается на нескольких веб-серверах и требует сохранения сеанса пользователя (липкость). Как я могу достичь настойчивости сеанса? 

Для получения рекомендаций [см.](application-proxy-high-availability-load-balancing.md)

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Могу ли я разместить передний прокси-устройство между сервером разъема (ы) и сервером приложения обратного конца?

Нет, этот сценарий не поддерживается. Только службы разъема и обновления могут быть настроены для использования переднего прокси для исходящего трафика в Azure. Смотрите [Работа с существующими предприимчивая прокси-серверами](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers)

### <a name="is-tls-termination-tlshttps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Поддерживается ли прекращение TLS (проверка TLS/HTTPS или ускорение) на трафике с серверов разъема на Azure?

Соединение прокси-приложений выполняет проверку подлинности на основе сертификатов в Azure. Прекращение TLS (tLS/HTTPS инспекция или ускорение) нарушает этот метод проверки подлинности и не поддерживается. Трафик от разъема до Azure должен обходить любые устройства, выполняющие TLS Termination.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Должен ли я создать специальную учетную запись для регистрации разъема с помощью Azure AD Application Proxy?

Нет причин. Любая глобальная учетная запись администратора или администратора приложений будет работать. Учетные данные, введенные во время установки, не используются после процесса регистрации. Вместо этого сертификат выдается разъему, который используется для проверки подлинности с этого момента.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Как я могу контролировать производительность разъема прокси-сервера Azure AD?

Есть счетчики монитора производительности, которые установлены вместе с разъемом. Чтобы просмотреть эти элементы:  

1. Выберите **Начало,** введите "Perfmon" и нажмите ENTER.
2. Выберите **монитор производительности** и нажмите зеленый **+** значок.
3. Добавьте счетчики **прокси-соединения приложений Microsoft AAD,** которые вы хотите контролировать.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Должен ли разъем приложения Azure AD Proxy находиться в той же подсети, что и ресурс?

Разъем не требуется, чтобы быть на той же подсети. Тем не менее, для этого необходимо разрешение имени (DNS, файл хостов) к ресурсу и необходимое подключение к сети (переориентация на ресурс, открытые порты на ресурсе и т.д.). Для получения рекомендаций см. [соображения топологии сети при использовании прокси-приложения Azure Active Directory Proxy.](application-proxy-network-topology.md)

## <a name="application-configuration"></a>Конфигурация приложений

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Какова длина по умолчанию и "длинный" бэк-энд тайм-аут? Можно ли продлить тайм-аут?

Длина по умолчанию составляет 85 секунд. "Длинная" настройка составляет 180 секунд. Ограничение тайм-аута не может быть продлено.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Как изменить целевую страницу загрузки приложения?

Со страницы регистрации приложений можно изменить URL-адрес главной страницы на нужный внешний URL целевой страницы. Указанная страница будет загружена при запуске приложения с my Apps или портала Office 365. Для шагов конфигурации смотрите [на специальной домашней странице для опубликованных приложений с помощью прокси-приложения Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Могут ли быть опубликованы только приложения на базе IIS? Как насчет веб-приложений, работающих на веб-серверах, не относящих Windows? Должен ли разъем быть установлен на сервере с установленным IIS?

Нет, нет требования IIS для приложений, которые публикуются. Вы можете публиковать веб-приложения, работающие на серверах, кроме Windows Server. Тем не менее, вы не сможете использовать предварительную аутентификацию с сервером, не относящимся к Windows, в зависимости от того, поддерживает ли веб-сервер «Переговоры» (проверка подлинности Kerberos). IIS не требуется на сервере, где установлен разъем.

## <a name="integrated-windows-authentication"></a>Встроенная проверка подлинности Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Когда следует использовать метод PrincipalsAllowedToDelegateToAccount при настройке ограниченной делегации Kerberos (KCD)?

Метод PrincipalsAllowedToDelegateToAccount используется, когда серверы разъема находятся в другом домене от учетной записи службы веб-приложений. Она требует использования ограниченной делегации на основе ресурсов.
Если серверы разъема и учетная запись службы веб-приложений находятся в одном домене, можно использовать Активные пользователи каталогов и компьютеры для настройки параметров делегирования на каждой из учетных записей разъемной машины, что позволяет им делегировать целевой SPN.

Если серверы разъема и учетная запись службы веб-приложений находятся в разных доменах, используется делегация на основе ресурсов. Разрешения делегации настраиваются на целевой веб-сервер и учетную запись службы веб-приложений. Этот метод ограниченной делегирования является относительно новым. Метод был введен в Windows Server 2012, который поддерживает делегацию кросс-домена, позволяя владельцу ресурса (веб-сервиса) контролировать, какие учетные записи машины и службы могут делегировать ему. Нет никакого использования используется для использования в этой конфигурации, поэтому вам необходимо использовать PowerShell.
Для получения дополнительной информации, см. [Understanding Kerberos Constrained Delegation with Application Proxy](https://aka.ms/kcdpaper)

## <a name="pass-through-authentication"></a>Сквозная аутентификация

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Могу ли я использовать политики условного доступа для приложений, опубликованных с помощью сквозной аутентификации?

Политики условного доступа применяются только для успешно проверенных пользователей в Azure AD. Сквозная аутентификация не вызывает аутентификацию Azure AD, поэтому политики условного доступа не могут быть приведены в исполнение. При сквозной аутентификации политики MFA должны быть реализованы на сервере, если это возможно, или путем включения предварительной аутентификации с помощью Azure AD Application Proxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Могу ли я опубликовать веб-приложение с требованием проверки подлинности сертификата клиента?

Нет, этот сценарий не поддерживается, поскольку прокси-сервер приложения прекратит трафик TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Удаленное издание шлюза для рабочего стола

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Как опубликовать шлюз удаленного рабочего стола через прокси-приложение Azure AD?

Обратитесь к [публикации удаленного рабочего стола с прокси-приложением Azure AD.](application-proxy-integrate-with-remote-desktop-services.md)

### <a name="can-i-use-kerberos-constrained-delegation-single-sign-on---windows-integrated-authentication-in-the-remote-desktop-gateway-publishing-scenario"></a>Могу ли я использовать ограниченную делегацию Kerberos (Single Sign-On - Интегрированная аутентификация Windows) в сценарии публикации удаленного рабочего стола?

Нет, этот сценарий не поддерживается.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Мои пользователи не используют Internet Explorer 11, и сценарий предварительной проверки не работает для них. Так и должно быть?

Да, это ожидаемо. Сценарий предварительной проверки требует управления ActiveX, который не поддерживается в сторонних браузерах.

### <a name="is-the-remote-desktop-web-client-html5-supported"></a>Поддерживается ли веб-клиент удаленного рабочего стола (HTML5)?

Нет, этот сценарий в настоящее время не поддерживается. Следите за обновлениями на этом форуме [UserVoice.](https://aka.ms/aadapuservoice)

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>После настройки сценария предварительной аутентификации я понял, что пользователь должен проверить подлинность дважды: сначала в форме регистрации Azure AD, а затем в форме входной формы RDWeb. Так и должно быть? Как я могу уменьшить это до одного ввоза?

Да, это ожидаемо. Если компьютер пользователя присоединился к Azure AD, пользователь автоматически переходит в Azure AD. Пользователь должен предоставить свои учетные данные только в форме регистрации RDWeb.

## <a name="sharepoint-publishing"></a>Публикация SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Как опубликовать SharePoint через прокси-сервер Azure AD?

Обратитесь к [включить удаленный доступ к SharePoint с Azure AD приложение прокси](application-proxy-integrate-with-sharepoint-server.md).

### <a name="can-i-use-the-sharepoint-mobile-app-ios-android-to-access-a-published-sharepoint-server"></a>Могу ли я использовать мобильное приложение SharePoint (iOS/ Android) для доступа к опубликованному серверу SharePoint?

[Мобильное приложение SharePoint](https://docs.microsoft.com/sharepoint/administration/supporting-the-sharepoint-mobile-apps-online-and-on-premises) не поддерживает предпроверку подлинности Active Directory Azure.

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Публикация Active Directory Federation Services (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Могу ли я использовать прокси-сервер Azure AD Application в качестве прокси-сервера AD FS (например, веб-прокси-сервера)?

Нет. Прокси-сервер Azure AD при приложении Azure AD предназначен для работы с Azure AD и не выполняет требования, предъявляемые к прокси-серверу AD FS.

## <a name="websocket"></a>Websocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Работает ли поддержка WebSocket для приложений, не относясь до zlikSense?

В настоящее время поддержка протокола WebSocket все еще находится в открытом доступе и может не работать для других приложений. Некоторые клиенты имели смешанный успех, используя протокол WebSocket с другими приложениями. Если вы тестируете такие сценарии, мы хотели бы услышать ваши результаты. Пожалуйста, пришлите aadapfeedback@microsoft.comнам свои отзывы по адресу .

Функции (Eventlogs, PowerShell и Remote Desktop Services) в Центре управления Windows (WAC) или удаленном веб-клиенте рабочего стола (HTML5) в настоящее время не работают через прокси-сервер Azure AD Application.

## <a name="link-translation"></a>Перевод ссылки

### <a name="does-using-link-translation-affect-performance"></a>Влияет ли использование перевода Link на производительность?

Да. Перевод ссылок влияет на производительность. Служба Приложения Proxy сканирует приложение для жестко закодированных ссылок и заменяет их соответствующими, опубликованными внешними URL-адресами, прежде чем представить их пользователю. 

Для лучшей производительности мы рекомендуем использовать идентичные внутренние и внешние URL-адреса путем настройки [пользовательских доменов.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain) Если использование пользовательских доменов невозможно, вы можете улучшить производительность перевода ссылок, используя мой безопасный знак приложений в расширении или Microsoft Edge Browser на мобильном телефоне. Смотрите [перенаправить жесткие ссылки для приложений, опубликованных с Azure AD Application Proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Подстановочные знаки

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Как использовать подстановочные знаки для публикации двух приложений с одним и тем же пользовательским доменным именем, но с разными протоколами, одним для HTTP и одним для HTTPS?

Этот сценарий не поддерживается напрямую. Ваши варианты для этого сценария:

1. Публикуйте URL-адреса HTTP и HTTPS как отдельные приложения с подстановочным знаком, но присваивайте каждому из них различный пользовательский домен. Эта конфигурация будет работать, поскольку они имеют различные внешние URLS.

2. Опубликовать URL HTTPS через приложение подстановочных знаков. Опубликовать приложения HTTP отдельно, используя эти приложения Прокси PowerShell cmdlets:
   - [Управление приложениями прокси](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Управление подключением прокси-приложений](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
