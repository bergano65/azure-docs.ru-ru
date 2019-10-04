---
title: Часто задаваемые вопросы об Azure AD Application Proxy | Документация Майкрософт
description: Ознакомьтесь с ответами на часто задаваемые вопросы об использовании AD Application Proxy Azure для публикации внутренних локальных приложений для удаленных пользователей.
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
ms.openlocfilehash: 9743f25d89bed4e54b3deed815d1cf29030caff6
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/04/2019
ms.locfileid: "71955475"
---
# <a name="active-directory-azure-ad-application-proxy-frequently-asked-questions"></a>Часто задаваемые вопросы о прокси приложения Active Directory (Azure AD)

На этой странице приведены ответы на часто задаваемые вопросы о Azure Active Directory прокси приложения (Azure AD).

## <a name="enabling-azure-ad-application-proxy"></a>Включение Azure AD Application Proxy

### <a name="what-license-is-required-to-use-azure-ad-application-proxy"></a>Какая лицензия необходима для использования AD Application Proxy Azure?

Чтобы использовать Azure AD Application Proxy, необходимо иметь лицензию Azure AD Premium P1 или P2. Дополнительные сведения о лицензировании см. в разделе [цены на Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/)

### <a name="why-is-the-enable-application-proxy-button-grayed-out"></a>Почему кнопка "включить прокси приложения" неактивна?

Убедитесь, что у вас есть по крайней мере лицензия Azure AD Premium P1 или P2 и установлен соединитель AD Application Proxy Azure. После успешной установки первого соединителя служба AD Application Proxy Azure будет включена автоматически.

## <a name="connector-configuration"></a>Конфигурация соединителя

### <a name="can-application-proxy-connector-services-run-in-a-different-user-context-than-the-default"></a>Могут ли службы соединителя прокси приложения запускаться в контексте пользователя, отличном от контекста по умолчанию?

Нет, этот сценарий не поддерживается. Параметры по умолчанию:

- Соединитель прокси приложения Microsoft AAD — Вапксвк — сетевая служба
- Средство обновления соединителей Microsoft AAD Application Proxy-Вапкупдатерсвк-NT Authority\System

### <a name="my-back-end-application-is-hosted-on-multiple-web-servers-and-requires-user-session-persistence-stickiness-how-can-i-achieve-session-persistence"></a>Мое серверное приложение размещается на нескольких веб-серверах и требует сохранения пользовательского сеанса («закрепления»). Как можно добиться сохранения сеанса? 

Рекомендации см. в статье [Высокая доступность и балансировка нагрузки соединителей прокси приложения и приложений](application-proxy-high-availability-load-balancing.md).

### <a name="can-i-place-a-forward-proxy-device-between-the-connector-servers-and-the-back-end-application-server"></a>Можно ли разместить устройство с прямым прокси-сервером между серверами соединителей и сервером серверных приложений?

Нет, этот сценарий не поддерживается. Только соединители и службы обновления можно настроить для использования прокси-сервера пересылки для исходящего трафика в Azure. См. раздел [Работа с существующими локальными прокси-серверами](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-connectors-with-proxy-servers) .

### <a name="is-ssl-termination-sslhhtps-inspection-or-acceleration-on-traffic-from-the-connector-servers-to-azure-supported"></a>Поддерживается ли завершение SSL (SSL/ХХТПС-проверка или ускорение) трафика от серверов соединителей в Azure?

Соединитель прокси приложения выполняет проверку подлинности на основе сертификата в Azure. Прерывание SSL (проверка или ускорение SSL/ХХТПС) прерывает этот метод проверки подлинности и не поддерживается. Трафик из соединителя в Azure должен обходить все устройства, выполняющие завершение SSL.  

### <a name="should-i-create-a-dedicated-account-to-register-the-connector-with-azure-ad-application-proxy"></a>Следует ли создать выделенную учетную запись для регистрации соединителя в Azure AD Application Proxy?

Нет причин. Будет работать любая учетная запись глобального администратора или администратора приложения. Учетные данные, указанные во время установки, не используются после процесса регистрации. Вместо этого к соединителю выдается сертификат, который используется для проверки подлинности с этого момента.

### <a name="how-can-i-monitor-the-performance-of-the-azure-ad-application-proxy-connector"></a>Как можно отслеживать производительность соединителя Azure AD Application Proxy?

Существуют счетчики системного монитора, которые устанавливаются вместе с соединителем. Для просмотра:  

1. Нажмите кнопку **Пуск**, введите "PerfMon" и нажмите клавишу ВВОД.
2. Выберите **системный монитор** и щелкните зеленый значок **+** .
3. Добавьте счетчики **соединителя прокси приложения Microsoft AAD** , которые необходимо отслеживать.

### <a name="does-the-azure-ad-application-proxy-connector-have-to-be-on-the-same-subnet-as-the-resource"></a>Должен ли соединитель Azure AD Application Proxy находиться в той же подсети, что и ресурс?

Соединитель не обязательно должен находиться в той же подсети. Однако ему требуется разрешение имен (DNS, файл Hosts) для ресурса и необходимое сетевое подключение (маршрутизация к ресурсу, порты, открытые на ресурсе и т. д.). Рекомендации см. в разделе [рекомендации по топологии сети при использовании Azure Active Directory Application proxy](application-proxy-network-topology.md).

## <a name="application-configuration"></a>Конфигурация приложений

### <a name="what-is-the-length-of-the-default-and-long-back-end-timeout-can-the-timeout-be-extended"></a>Какова длина значения по умолчанию и времени ожидания серверной части "Long"? Можно ли увеличить время ожидания?

Длина по умолчанию составляет 85 секунд. Значение параметра "Long" составляет 180 секунд. Ограничение времени ожидания не может быть расширено.

### <a name="how-do-i-change-the-landing-page-my-application-loads"></a>Разделы справки изменить целевую страницу, с которой загружается приложение?

На странице регистрации приложений можно изменить URL-адрес домашней страницы на требуемый внешний URL-адрес целевой странице. Указанная страница будет загружаться при запуске приложения из папки "Мои приложения" или с портала Office 365. Инструкции по настройке см. в разделе [Настройка пользовательской домашней страницы для опубликованных приложений с помощью Azure AD application proxy](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-home-page)

### <a name="can-only-iis-based-applications-be-published-what-about-web-applications-running-on-non-windows-web-servers-does-the-connector-have-to-be-installed-on-a-server-with-iis-installed"></a>Можно ли публиковать только приложения на основе IIS? Как насчет веб-приложений, работающих на веб-серверах, отличных от Windows? Нужно ли устанавливать соединитель на сервере с установленными службами IIS?

Нет, для опубликованных приложений не требуется IIS. Можно публиковать веб-приложения, работающие на серверах, отличных от Windows Server. Однако использование предварительной проверки подлинности на сервере, отличном от Windows Server, может оказаться невозможным в зависимости от того, поддерживает ли веб-сервер согласование (проверка подлинности Kerberos). Службы IIS не требуются на сервере, на котором установлен соединитель.

## <a name="integrated-windows-authentication"></a>Интегрированная проверка подлинности Windows

### <a name="when-should-i-use-the-principalsallowedtodelegatetoaccount-method-when-setting-up-kerberos-constrained-delegation-kcd"></a>Когда следует использовать метод PrincipalsAllowedToDelegateToAccount при настройке ограниченного делегирования Kerberos (KCD)?

Метод PrincipalsAllowedToDelegateToAccount используется, когда серверы соединителей находятся в разных доменах из учетной записи службы веб-приложений. Он требует использования ограниченного делегирования на основе ресурсов.
Если серверы соединителей и учетная запись службы веб-приложения находятся в одном домене, можно использовать Active Directory пользователи и компьютеры для настройки параметров делегирования на каждой учетной записи компьютера соединителя, что позволяет им делегировать целевому имени участника-службы.

Если серверы соединителей и учетная запись службы веб-приложения находятся в разных доменах, используется делегирование на основе ресурсов. Разрешения на делегирование настраиваются на целевом веб-сервере и в учетной записи службы веб-приложений. Этот метод ограниченного делегирования является относительно новым. Этот метод появился в Windows Server 2012, который поддерживает делегирование между доменами, позволяя владельцу ресурса (веб-службе) управлять тем, какие компьютеры и учетные записи служб могут быть делегированы. Нет пользовательского интерфейса для помощи с этой конфигурацией, поэтому вам потребуется использовать PowerShell.
Дополнительные сведения см. в техническом документе [об ограниченном делегировании Kerberos с помощью прокси приложения](http://aka.ms/kcdpaper).

## <a name="pass-through-authentication"></a>Сквозная аутентификация

### <a name="can-i-use-conditional-access-policies-for-applications-published-with-pass-through-authentication"></a>Можно ли использовать политики условного доступа для приложений, опубликованных с помощью сквозной проверки подлинности?

Политики условного доступа применяются только для пользователей, которые прошли предварительную проверку подлинности в Azure AD. Сквозная проверка подлинности не активирует аутентификацию Azure AD, поэтому применение политик условного доступа невозможно. При использовании сквозной проверки подлинности политики MFA должны быть реализованы на локальном сервере, если это возможно, или путем включения предварительной проверки подлинности в Azure AD Application Proxy.

### <a name="can-i-publish-a-web-application-with-client-certificate-authentication-requirement"></a>Можно ли опубликовать веб-приложение с требованием проверки подлинности сертификата клиента?

Нет, этот сценарий не поддерживается, так как прокси приложения будет завершать трафик TLS.  

## <a name="remote-desktop-gateway-publishing"></a>Публикация шлюза удаленный рабочий стол

### <a name="how-can-i-publish-remote-desktop-gateway-over-azure-ad-application-proxy"></a>Как опубликовать удаленный рабочий стол шлюза в Azure AD Application Proxy?

См. статью [публикация удаленный рабочий стол с помощью AD application proxy Azure](application-proxy-integrate-with-remote-desktop-services.md).

### <a name="can-i-use-kerberos-constrained-delegation-in-the-remote-desktop-gateway-publishing-scenario"></a>Можно ли использовать ограниченное делегирование Kerberos в сценарии публикации шлюза удаленный рабочий стол?

Нет, этот сценарий не поддерживается.  

### <a name="my-users-dont-use-internet-explorer-11-and-the-pre-authentication-scenario-doesnt-work-for-them-is-this-expected"></a>Пользователи не используют Internet Explorer 11, и сценарий предварительной проверки подлинности не подходит для них. Ожидается ли это?

Да, это ожидаемое значение. Для сценария предварительной проверки подлинности требуется элемент управления ActiveX, который не поддерживается сторонними браузерами.

### <a name="is-the-remote-desktop-web-client-supported"></a>Поддерживается ли веб-клиент удаленный рабочий стол?

Нет, в настоящее время этот сценарий не поддерживается. Ознакомьтесь с нашим форумом по обратной связи [UserVoice](https://aka.ms/aadapuservoice) , чтобы получить обновления для этой функции.

### <a name="after-i-configured-the-pre-authentication-scenario-i-realized-that-the-user-has-to-authenticate-twice-first-on-the-azure-ad-sign-in-form-and-then-on-the-rdweb-sign-in-form-is-this-expected-how-can-i-reduce-this-to-one-sign-in"></a>После настройки сценария предварительной проверки подлинности я понял, что пользователь должен пройти проверку подлинности дважды: сначала на форме входа в Azure AD, а затем на форме входа RDWeb. Ожидается ли это? Как можно сократить это до одного входа?

Да, это ожидаемое значение. Если компьютер пользователя подключен к Azure AD, он автоматически входит в Azure AD. Пользователь должен предоставить свои учетные данные только в форме входа RDWeb.

## <a name="sharepoint-publishing"></a>Публикация SharePoint

### <a name="how-can-i-publish-sharepoint-over-azure-ad-application-proxy"></a>Как опубликовать SharePoint через Azure AD Application Proxy?

См. статью [Включение удаленного доступа к SharePoint с помощью AD application proxy Azure](application-proxy-integrate-with-sharepoint-server.md).

## <a name="active-directory-federation-services-ad-fs-publishing"></a>Публикация службы федерации Active Directory (AD FS) (AD FS) 

### <a name="can-i-use-azure-ad-application-proxy-as-ad-fs-proxy-like-web-application-proxy"></a>Можно ли использовать AD Application Proxy Azure в качестве прокси-сервера AD FS (например, прокси)?

Нет. Azure AD Application Proxy предназначен для работы с Azure AD и не выполняет требования, выступающие в роли прокси-сервера AD FS.

## <a name="websocket"></a>WebSocket

### <a name="does-websocket-support-work-for-applications-other-than-qliksense"></a>Поддерживает ли WebSocket работу для приложений, отличных от QlikSense?

В настоящее время поддержка протокола WebSocket по-прежнему доступна в общедоступной предварительной версии, и она может не работать для других приложений. Некоторые клиенты успешно выполнили смешанный успешный протокол WebSocket с другими приложениями. При тестировании таких сценариев мы будем рады услышать результаты. Отправьте нам свой отзыв по адресу aadapfeedback@microsoft.com.

## <a name="link-translation"></a>Преобразование ссылок

### <a name="does-using-link-translation-affect-performance"></a>Влияет ли преобразование ссылок на производительность?

Да. Преобразование ссылок влияет на производительность. Служба прокси приложения просматривает приложение на наличие жестко закодированных ссылок и заменяет их соответствующими опубликованными внешними URL-адресами перед их представлением пользователю. 

Для лучшей производительности рекомендуется использовать идентичные внутренние и внешние URL-адреса, настроив [Личные домены](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-custom-domain). Если использование пользовательских доменов невозможно, можно улучшить производительность преобразования ссылок с помощью расширения для безопасного входа в мои приложения или браузера Microsoft ребра на мобильных устройствах. См. раздел [Перенаправление жестко закодированных ссылок для приложений, опубликованных с помощью Azure AD application proxy](application-proxy-configure-hard-coded-link-translation.md).

## <a name="wildcards"></a>Подстановочные знаки

### <a name="how-do-i-use-wildcards-to-publish-two-applications-with-the-same-custom-domain-name-but-with-different-protocols-one-for-http-and-one-for-https"></a>Разделы справки использовать подстановочные знаки для публикации двух приложений с одним и тем же именем личного домена, но с разными протоколами, одним для HTTP и одним для HTTPS?

Этот сценарий не поддерживается напрямую. Параметры для этого сценария:

1. Опубликуйте URL-адреса HTTP и HTTPS как отдельные приложения с подстановочным знаком, но присвойте каждому из них другой личный домен. Эта конфигурация будет работать, так как у них разные внешние URL-адреса.

2. Опубликуйте URL-адрес HTTPS с помощью приложения с подстановочными знаками. Опубликуйте приложения HTTP отдельно с помощью этих командлетов PowerShell для прокси приложения:
   - [Управление приложениями прокси приложения](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_application_management)
   - [Управление соединителем прокси приложения](https://docs.microsoft.com/powershell/module/azuread/?view=azureadps-2.0#application_proxy_connector_management)
