---
title: Понимание и решение проблем CORS прокси приложения Azure AD
description: Дает представление о CORS в прокси приложения Azure AD и как определить и решить проблемы, связанные с CORS.
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
ms.openlocfilehash: 2b6adcf4231aa44a4f28d277e963efa16de8af81
ms.sourcegitcommit: d89032fee8571a683d6584ea87997519f6b5abeb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/30/2019
ms.locfileid: "66399342"
---
# <a name="understand-and-solve-azure-active-directory-application-proxy-cors-issues"></a>Понимание и решение проблем с Azure Active Directory CORS прокси-сервера для приложения

[Кросс-совместного использования ресурсов (CORS)](http://www.w3.org/TR/cors/) иногда может сопровождаться рядом проблем для приложения и интерфейсы API, опубликованного через прокси приложения Azure Active Directory. В этой статье рассматриваются проблемы CORS прокси приложения Azure AD и решения.

Безопасность обозревателя обычно препятствует веб-странице отправку запросов AJAX к другому домену. Это ограничение называется *политика одного источника*и предотвращает чтение конфиденциальных данных с другого сайта вредоносный сайт. Тем не менее иногда вам может потребоваться разрешить другие сайты вызова веб-API. CORS — это стандарт консорциума W3C, который дает возможность серверу смягчить ограничения политики одного источника и разрешить некоторые запросы независимо от источника, а другие — отклонять.

## <a name="understand-and-identify-cors-issues"></a>Проанализировать и определить проблемы CORS

Два URL-адреса иметь того же происхождения, если они имеют одинаковые схемы, узлов и порты ([RFC 6454](https://tools.ietf.org/html/rfc6454)), такие как:

-   http:\//contoso.com/foo.html
-   http:\//contoso.com/bar.html

Следующие URL-адреса имеют различное происхождение по сравнению с предыдущим два:

-   http:\//contoso.net - другого домена
-   http:\//contoso.com:9000/foo.html - другой порт
-   https:\//contoso.com/foo.html - другую схему
-   http:\//www.contoso.com/foo.html - другой поддомен

Политика одного источника будет запрещать доступ к ресурсам из других источников, если они используют заголовки control надлежащих прав доступа. Если заголовки CORS отсутствует или неверен, независимо от источника запросы завершаются сбоем. 

CORS проблемы можно определить с помощью средства отладки браузера:

1. Откройте браузер и перейдите к веб-приложения.
1. Нажмите клавишу **F12** откроется в консоли отладки.
1. Попробуйте воспроизвести транзакции и просмотрите сообщение консоли. Нарушение CORS порождает ошибку консоли о происхождении.

На следующем снимке экрана выбрав **попробовать** кнопка была сообщение об ошибке CORS, https:\//corswebclient-contoso.msappproxy.net не найден в заголовке Access-Control-Allow-Origin.

![Проблема CORS](./media/application-proxy-understand-cors-issues/image3.png)

## <a name="cors-challenges-with-application-proxy"></a>Проблемы CORS с помощью прокси приложения

В следующем примере показан типичный сценарий CORS прокси приложения Azure AD. Внутренний сервер узлы **CORSWebService** контроллера веб-API и **CORSWebClient** , который вызывает **CORSWebService**. Имеется запрос AJAX из **CORSWebClient** для **CORSWebService**.

![Запрос одного источника на предприятии](./media/application-proxy-understand-cors-issues/image1.png)

Приложение CORSWebClient работает при размещении его в локальной, но либо сбой загрузки или при публикации через прокси приложения Azure AD. Если вы опубликовали приложения CORSWebClient и CORSWebService отдельно как разные приложения через прокси приложения, два приложения размещаются в разных доменах. Запрос AJAX из CORSWebClient CORSWebService запрос независимо от источника, и она выходит из строя.

![Запрос CORS прокси приложения](./media/application-proxy-understand-cors-issues/image2.png)

## <a name="solutions-for-application-proxy-cors-issues"></a>Решения для проблем, CORS прокси приложения

Можно разрешить выше проблема CORS в одном из следующих способов.

### <a name="option-1-set-up-a-custom-domain"></a>Вариант 1. Настройка личного домена

Использовать прокси приложения Azure AD [личного домена](https://docs.microsoft.com/azure/active-directory/active-directory-application-proxy-custom-domains) для публикации с того же происхождения, без необходимости вносить изменения в приложение происхождения кода или заголовки. 

### <a name="option-2-publish-the-parent-directory"></a>Вариант 2. Опубликовать родительский каталог

Опубликуйте родительский каталог создаваемого оба приложения. Это решение является особенно при наличии только двух приложений на веб-сервере. Вместо публикации каждого приложения отдельно, вы можете опубликовать общий родительский каталог, что приводит к того же происхождения.

Ниже приведены примеры портале страницу CORSWebClient приложения прокси приложения Azure AD.  Когда **внутренний URL-адрес** присваивается *contoso.com/CORSWebClient*, приложение не может сделать успешно обработанных запросов *contoso.com/CORSWebService* каталог, так как они независимо от источника. 

![Публикация приложения по отдельности](./media/application-proxy-understand-cors-issues/image4.png)

Вместо этого задайте **внутренний URL-адрес** опубликовать родительский каталог, который включает в себя *CORSWebClient* и *CORSWebService* каталоги:

![Родительский каталог публикации](./media/application-proxy-understand-cors-issues/image5.png)

Полученный URL-адреса приложения эффективно решить проблему CORS:

- https:\//corswebclient-contoso.msappproxy.net/CORSWebService
- https:\//corswebclient-contoso.msappproxy.net/CORSWebClient

### <a name="option-3-update-http-headers"></a>Вариант 3. Обновление заголовков HTTP

Добавление заголовка ответа HTTP веб-службу для проверки источника запроса. Для веб-сайтов, работают в Internet Information Services (IIS) используйте диспетчер служб IIS для изменения заголовка.

![Добавление пользовательского заголовка ответного сообщения в диспетчере служб IIS](./media/application-proxy-understand-cors-issues/image6.png)

Это изменение не требует изменения кода. Вы можете проверить в трассировки Fiddler:

**POST Добавление заголовка**\
HTTP/1.1 200 OK\
Cache-Control: нет-cache\
Директивы pragma: нет-cache\
Content-Type: text/plain; charset=utf-8\
Срок действия:-1\
Различаются: Принять Encoding\
Сервер:  Microsoft-IIS/8.5 Microsoft-HTTPAPI/2.0\
**Access-Control-Allow-Origin: https://corswebclient-contoso.msappproxy.net** \
X-AspNet-Version: 4.0.30319\
X-Powered-By: ASP.NET\
Content-Length: 17

### <a name="option-4-modify-the-app"></a>Вариант 4. Измените приложение

Вы можете изменить приложения для поддержки CORS путем добавления заголовка Access-Control-Allow-Origin, с соответствующими значениями. Способ добавления заголовка зависит от языка кода приложения. Изменение кода рекомендуется бы, поскольку она требует наиболее трудоемкий.

### <a name="option-5-extend-the-lifetime-of-the-access-token"></a>Способ 5: Увеличения времени существования маркера доступа

Некоторые проблемы CORS не может быть разрешена, например, когда приложение перенаправляет *login.microsoftonline.com* для проверки подлинности, и срока действия маркера доступа. CORS, вызовите затем происходит сбой. Обходной путь для этого сценария используется для расширения времени существования маркера доступа, чтобы предотвратить истечение срока действия во время сеанса пользователя. Дополнительные сведения о том, как это сделать, см. в разделе [Настройка времени жизни маркеров в Azure AD](../develop/active-directory-configurable-token-lifetimes.md).

## <a name="see-also"></a>См. также
- [Учебник. Добавление локального приложения для удаленного доступа через прокси приложения в Azure Active Directory](application-proxy-add-on-premises-application.md) 
- [Планирование развертывания прокси приложения Azure AD](application-proxy-deployment-plan.md) 
- [Удаленный доступ к локальным приложениям через прокси приложения Azure Active Directory](application-proxy.md) 
