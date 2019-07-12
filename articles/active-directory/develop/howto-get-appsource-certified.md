---
title: Получение сертификата AppSource для Azure Active Directory | Документация Майкрософт
description: Сведения о получении сертификата AppSource приложения для Azure Active Directory.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: 21206407-49f8-4c0b-84d1-c25e17cd4183
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 08/21/2018
ms.author: ryanwi
ms.reviewer: andret
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cc42ab8a8cfb0d182c69bd0940e23cffdb2be0af
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67807237"
---
# <a name="how-to-get-appsource-certified-for-azure-active-directory"></a>Как получить сертификат AppSource для Azure Active Directory

С помощью [Microsoft AppSource](https://appsource.microsoft.com/) бизнес-пользователи могут обнаруживать бизнес-приложения SaaS (автономные приложения SaaS и дополнения к имеющимся продуктам SaaS Microsoft), использовать их и управлять ими.

Чтобы отобразить список автономных приложений SaaS на AppSource, приложение должно принять единый вход из рабочих учетных записей любой компании или организации с Azure Active Directory (Azure AD). В процессе входа необходимо использовать протоколы [OpenID Connect](v1-protocols-openid-connect-code.md) или [OAuth 2.0](v1-protocols-oauth-code.md). Для сертификации AppSource интеграции SAML не принимается.

## <a name="guides-and-code-samples"></a>Руководства и примеры кода

Если вы хотите узнать о том, как интегрировать приложения с Azure AD с помощью Open ID Connect, следуйте инструкциям и используйте примеры кода из раздела (v1-overview.md#get-started "Начало работы") статьи [Azure Active Directory для разработчиков].

## <a name="multi-tenant-applications"></a>Мультитенантные приложения

Приложение, принимающее вход пользователя из любой компании или организации с Azure AD, не требуя отдельного экземпляра, конфигурации или развертывания, называется *мультитенантным*. AppSource рекомендует реализовать поддержку мультитенантности в приложениях, чтобы добавить бесплатную пробную версию возможности входа *одним щелчком*.

Чтобы включить мультитенантность в приложении, сделайте следующее.
1. При введении регистрационной информации приложения на [портале Azure](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/RegisteredApps) задайте для свойства `Multi-Tenanted` значение `Yes`. По умолчанию все приложения, созданные на портале Azure, настроены как *[приложения с одним клиентом](#single-tenant-applications)* .
1. Обновите код для отправки запросов в конечную точку `common`. Чтобы сделать это, обновите конечную точку из `https://login.microsoftonline.com/{yourtenant}` в `https://login.microsoftonline.com/common*`.
1. Для некоторых платформ, например ASP .NET, необходимо также обновить код, чтобы принимать несколько издателей.

Дополнительные сведения о мультитенантности см. в статье [Как реализовать вход любого пользователя Azure Active Directory с помощью шаблона мультитенантного приложения](howto-convert-app-to-be-multi-tenant.md).

### <a name="single-tenant-applications"></a>Приложения с одним клиентом

*Приложения с одним клиентом* являются приложениями, которые принимают вход пользователей только из определенного экземпляра Azure AD. Внешние пользователи (включая рабочие или учебные учетные записи из других организаций или личные учетные записи) могут войти в приложение с одним клиентом после добавления каждого пользователя в качестве гостевой учетной записи в экземпляр Azure AD, в котором регистрируется приложение. 

В Azure AD пользователей можно добавлять в качестве гостевых учетных записей через [службу совместной работы Azure AD B2B](../b2b/what-is-b2b.md). Это можно сделать [программным образом](../../active-directory-b2c/code-samples.md). При использовании B2B пользователи могут создать портал самообслуживания, для входа на которой не требуется приглашение. Дополнительные сведения см. в статье [Самостоятельная регистрация на портале для службы совместной работы Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal).

В приложениях с одним клиентом можно включить возможность *Свяжитесь со мной*. Однако если требуется включить возможность входа одним щелчком или пробную версию возможностей, рекомендуемые AppSource, то включите в приложении мультитенантность.

## <a name="appsource-trial-experiences"></a>Пробные версии возможностей AppSource

### <a name="free-trial-customer-led-trial-experience"></a>Бесплатная пробная версия возможностей (клиентская)

Клиентская пробная версия возможностей рекомендуется AppSource, так как она позволяет воспользоваться доступом к приложению в один щелчок. В следующем примере показано, как выглядит этот интерфейс:

<table >
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step1.png" width="85%" alt-text="Shows Free trial for customer-led trial experience"/><ul><li>Пользователь находит ваше приложение на веб-сайте AppSource.</li><li>Затем выбирает "Бесплатная пробная версия".</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step2.png" width="85%" alt-text="Shows how user is redirected to a URL in your web site"/><ul><li>AppSource перенаправляет пользователя по URL-адресу вашего веб-сайта.</li><li>На вашем веб-сайте автоматически запускается <i>единый вход</i> (при загрузке страницы).</li></ul></td>
    <td valign="top" width="33%">3.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step3.png" width="85%" alt-text="Shows the Microsoft sign-in page"/><ul><li>Пользователь перенаправляется на страницу входа Майкрософт.</li><li>Пользователь указывает учетные данные для входа.</li></ul></td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step4.png" width="85%" alt-text="Example: Consent page for an application"/><ul><li>Пользователь дает согласие для приложения.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Вход выполняется и пользователь перенаправляется на ваш веб-сайт.</li><li>Пользователь запускает бесплатную пробную версию.</li></ul></td>
    <td></td>
</tr>
</table>

### <a name="contact-me-partner-led-trial-experience"></a>Функция "Связаться со мной" (партнерская пробная версия)

Вы можете использовать партнерскую пробную версию для операций, выполняемых вручную, или длительных операций для подготовки пользователя или компании, например, когда приложению необходимо подготовить виртуальные машины, экземпляры базы данных или операции, которые занимают много времени. В этом случае, когда пользователь нажмет кнопку **Request Trial** (Запросить пробную версию) и заполнит форму, AppSource отправит вам его контактные данные. При получении этих сведений вы подготавливаете среду и отправляете пользователю инструкции по получению доступа к пробной версии.<br/><br/>

<table valign="top">
<tr>
    <td valign="top" width="33%">1.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step1.png" width="85%" alt-text="Shows Contact me for partner-led trial experience"/><ul><li>Пользователь находит ваше приложение на веб-сайте AppSource.</li><li>Выбирает параметр "Связаться со мной".</li></ul></td>
    <td valign="top" width="33%">2.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step2.png" width="85%" alt-text="Shows an example form with contact info"/><ul><li>Заполняет форму контактными данными.</li></ul></td>
     <td valign="top" width="33%">3.<br/><br/>
        <table bgcolor="#f7f7f7">
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/UserContact.png" width="55%" alt-text="Shows placeholder for user information"/></td>
            <td>Вы получаете сведения о пользователе.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/SetupEnv.png" width="55%" alt-text="Shows placeholder for setup environment info"/></td>
            <td>Настраиваете среду.</td>
        </tr>
        <tr>
            <td><img src="media/active-directory-devhowto-appsource-certified/ContactCustomer.png" width="55%" alt-text="Shows placeholder for trial info"/></td>
            <td>Связываетесь с пользователем по поводу информации о пробной версии.</td>
        </tr>
        </table><br/><br/>
        <ul><li>Вы получаете сведения пользователя и настраиваете пробный экземпляр.</li><li>Вы отправляете пользователю гиперссылку на получение доступа к приложению.</li></ul>
    </td>
</tr>
<tr>
    <td valign="top" width="33%">4.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step3.png" width="85%" alt-text="Shows the application sign-in screen"/><ul><li>Пользователь получает доступ к приложению и завершает процесс единого входа.</li></ul></td>
    <td valign="top" width="33%">5.<br/><img src="media/active-directory-devhowto-appsource-certified/partner-led-trial-step4.png" width="85%" alt-text="Shows an example consent page for an application"/><ul><li>Пользователь дает согласие для приложения.</li></ul></td>
    <td valign="top" width="33%">6.<br/><img src="media/active-directory-devhowto-appsource-certified/customer-led-trial-step5.png" width="85%" alt-text="Shows the experience the user sees when redirected back to your site"/><ul><li>Вход выполняется и пользователь перенаправляется на ваш веб-сайт.</li><li>Пользователь запускает бесплатную пробную версию.</li></ul></td>
   
</tr>
</table>

### <a name="more-information"></a>Дополнительные сведения

Дополнительные сведения о пробной версии AppSource см. [в этом видео](https://aka.ms/trialexperienceforwebapps). 

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о разработке приложений, поддерживающих вход с помощью Azure AD, см. в статье [Сценарии проверки подлинности в Azure AD](https://docs.microsoft.com/azure/active-directory/develop/authentication-scenarios).
- Сведения о выводе списка приложений SaaS в AppSource см. на [странице сведений для партнеров AppSource](https://appsource.microsoft.com/partners).

## <a name="get-support"></a>Получение поддержки

Для интеграции Azure AD мы используем [Stack Overflow](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource). Кроме того, участники сообщества могут предоставлять поддержку.

Мы настоятельно рекомендуем сначала задавать вопросы на сайте Stack Overflow и просмотреть имеющиеся проблемы, чтобы узнать, не задавал ли кто-то аналогичные вопросы раньше. Пометьте вопросы и комментарии тегами [`[azure-active-directory]` и `[appsource]`](https://stackoverflow.com/questions/tagged/azure-active-directory+appsource).

Оставляйте свои замечания и пожелания в разделе ниже. Они помогают нам улучшать содержимое веб-сайта.

<!--Reference style links -->
[AAD-Auth-Scenarios]:authentication-scenarios.md
[AAD-Auth-Scenarios-Browser-To-WebApp]:authentication-scenarios.md#web-browser-to-web-application
[AAD-Dev-Guide]: v1-overview.md
[AAD-Howto-Multitenant-Overview]: howto-convert-app-to-be-multi-tenant.md
[AAD-QuickStart-Web-Apps]: v1-overview.md#get-started

<!--Image references-->
