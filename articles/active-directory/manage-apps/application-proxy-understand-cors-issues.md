---
title: Понять и решить проблемы прокси-приложений Azure AD CorS
description: Обеспечивает понимание CORS в прокси-приложении Azure AD, а также способы выявления и решения проблем CORS.
services: active-directory
author: jeevanbisht
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: celested
ms.reviewer: japere
ms.openlocfilehash: c49535ad11139ac5145d4f283374bf9cc6d71f52
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "72025788"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Понять и решить проблемы прокси-приложений Azure Active Directory CORS

[Совместное использование ресурсов кросс-происхождения (CORS)](https://www.w3.org/TR/cors/) иногда может представлять проблемы для приложений и AI, которые вы публикуете через Azure Active Directory Application Proxy. В этой статье рассматриваются проблемы и решения Azure AD Application Proxy CORS.

Безопасность браузера обычно не позволяет веб-странице делать запросы AJAX на другой домен. Это ограничение называется *политикой того же происхождения*и не позволяет вредоносному сайту считывать конфиденциальные данные с другого сайта. Тем не менее, иногда вы можете позволить другим сайтам вызвать ваш веб-API. CORS является стандартом W3C, который позволяет серверу ослабить политику того же происхождения и разрешить некоторые запросы кросс-происхождения, отклоняя другие.

## <a name="understand-and-identify-cors-issues"></a>Понимание и выявление проблем CORS

Два URL-адреса имеют одинаковое происхождение, если они имеют одинаковые схемы, хосты и порты[(RFC 6454),](https://tools.ietf.org/html/rfc6454)такие как:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Следующие URL-адреса имеют различное происхождение, чем предыдущие два:

-   http:\//contoso.net - Различные домены
-   http:\//contoso.com:9000/foo.html - Различные порты
-   https:\//contoso.com/foo.html - По-разному схема
-   http:\//www.contoso.com/foo.html - Различные поддомены

Политика одного и того же происхождения не позволяет приложениям получать доступ к ресурсам из других истоков, если они не используют правильные заголовки управления доступом. Если заголовки CORS отсутствуют или неверны, запросы на перекрестное происхождение не сдаются. 

Вы можете определить проблемы CORS с помощью инструментов отладки браузера:

1. Запустите браузер и просмотрите в веб-приложении.
1. Нажмите **F12,** чтобы поднять отладку консоли.
1. Попробуйте воспроизвести транзакцию и просмотрите сообщение консоли. Нарушение CORS создает ошибку консоли о происхождении.

На следующем скриншоте, выбрав кнопку **Try It** вызвало\/сообщение об ошибке CORS, что https: /corswebclient-contoso.msappproxy.net не было найдено в заголовке Access-Control-Allow-Origin.

![Проблема CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>CorS проблемы с приложением прокси

В следующем примере показан типичный сценарий прокси-приложения Azure AD Proxy CORS. Внутренний сервер содержит контроллер веб-API **CORSWebService** и **CORSWebClient,** который вызывает **CORSWebService.** Есть запрос AJAX от **CORSWebClient** к **CORSWebService**.

![Запрос на одно именно ении](./media/application-proxy-understand-cors-issues/image1.png)

Приложение CORSWebClient работает при размещении его на месте, но либо не загружается, либо ошибки при публикации через Azure AD Application Proxy. Если вы опубликовали приложения CORSWebClient и CORSWebService отдельно как различные приложения через Application Proxy, эти два приложения размещаются в разных доменах. Запрос AJAX от CORSWebClient к CORSWebService является запросом кросс-происхождения, и он не удается.

![Запрос прокси-сервера приложения CORS](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Решения для проблем прокси-сервера приложений CORS

Предыдущую проблему CORS можно решить любым из нескольких способов.

### <a name="option-1-set-up-a-custom-domain"></a>Вариант 1: Настройка пользовательского домена

Используйте [пользовательский домен](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) Azure AD Application Proxy для публикации из того же происхождения без внесения каких-либо изменений в происхождение приложений, код или заголовки. 

### <a name="option-2-publish-the-parent-directory"></a>Вариант 2: Опубликовать родительский каталог

Опубликовать каталог учредителей обоих приложений. Это решение работает особенно хорошо, если у вас есть только два приложения на веб-сервере. Вместо публикации каждого приложения отдельно, вы можете опубликовать общий родительский каталог, что приводит к тому же происхождению.

Ниже приведены следующие примеры страницы прокси-приложения Azure AD для приложения CORSWebClient.  Когда **внутренний URL** установлен на *contoso.com/CORSWebClient,* приложение не может сделать успешные запросы в *contoso.com/CORSWebService* каталог, потому что они кросс-происхождения. 

![Публикация приложения по отдельности](./media/application-proxy-understand-cors-issues/image4.png)

Вместо этого установите **внутренний URL** для публикации родительского каталога, который включает в себя как *каталоги CORSWebClient,* так и *CORSWebService:*

![Публикация каталога родителей](./media/application-proxy-understand-cors-issues/image5.png)

Полученные URL-адреса приложения эффективно решают проблему CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Вариант 3: Обновление заголовков HTTP

Добавьте пользовательский заголовок ответа HTTP в веб-службе, чтобы соответствовать запросу на происхождение. Для веб-сайтов, работающих в информационных службах Интернета (IIS), используйте IIS Manager для изменения заголовка:

![Добавление заголовка пользовательского ответа в IIS Manager](./media/application-proxy-understand-cors-issues/image6.png)

Эта модификация не требует каких-либо изменений кода. Вы можете проверить его в следах Скрипача:

**Опубликовать добавление заголовка**\
HTTP/1.1 200 ОКЗ
Управление кэшом: без кэша
Прагма: без кэша
Тип содержимого: текст/простой; charset'utf-8
Срок действия: -1 "
Вари: Принять-кодирование
Сервер: Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0
**Контроль доступа-Разрешить-Происхождение: https//corswebclient-contoso.msappproxy.net\:**\
X-AspNet-Версия: 4.0.30319
X-Powered-By: ASP.NET
Содержание длины: 17

### <a name="option-4-modify-the-app"></a>Вариант 4: Изменить приложение

Вы можете изменить приложение для поддержки CORS, добавив заголовок Access-Control-Allow-Origin с соответствующими значениями. Способ добавления заголовка зависит от языка кода приложения. Изменение кода является наименее рекомендуемым вариантом, поскольку он требует больших усилий.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Вариант 5: Продлить срок службы токена доступа

Некоторые проблемы CORS не могут быть решены, например, когда приложение перенаправляет на *login.microsoftonline.com* на аутентификации, и срок действия маркера доступа истекает. Затем вызов CORS завершается неудачей. Для этого сценария необходимо продлить срок действия токена доступа, чтобы предотвратить его истечение во время сеанса пользователя. Для получения дополнительной информации о том, как это сделать, смотрите [Configurable токенов в Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>См. также
- [Учебник: Добавьте приложение для удаленного доступа через приложение Proxy в Active Directory Azure](application-proxy-add-on-premises-application.md) 
- [Планирование развертывания прокси-приложений Azure AD](application-proxy-deployment-plan.md) 
- [Удаленный доступ к корпоративным приложениям через прокси-сервер Azure Active Directory](application-proxy.md) 
