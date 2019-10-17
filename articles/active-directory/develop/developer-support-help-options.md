---
title: Возможности получения поддержки и справки для разработчиков приложений Azure AD | Документация Майкрософт
description: Узнайте, как получить справку и поддержку по вопросам и проблемам разработки, возникающим при создании приложения, которое интегрируется с удостоверениями Майкрософт (Azure Active Directory и учетной записью Майкрософт)
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/23/2019
ms.author: ryanwi
ms.reviewer: jmprieur, saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a160f78f69ed559c576147e9758c837d5ae9b77e
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72429979"
---
# <a name="support-and-help-options-for-developers"></a>Возможности получения поддержки и справки для разработчиков

Если вы только приступаете к интеграции с Azure Active Directory (Azure AD), удостоверениями Майкрософт или API Microsoft Graph либо реализуете в приложении новую возможность, бывают случаи, когда вам как разработчику требуется помощь сообщества или техническая поддержка. Эта статья поможет вам составить представление о том, как получить такую поддержку, в том числе:

> [!div class="checklist"]
> * Как узнать, есть ли ответ на ваш вопрос в сообществе, или найти документацию по возможности, которую вы намерены реализовать.
> * В некоторых случаях для решения конкретной проблемы вам могут потребоваться наши средства технической поддержки.
> * Если вам не удается найти нужный ответ, вы можете задать вопрос на сайте *Stack Overflow*.
> * Если возникла проблема с одной из наших библиотек аутентификации, сообщите об этом на сайте *GitHub*.
> * И, наконец, если вам требуется консультация, подайте запрос в службу поддержки.

## <a name="search"></a>служба поиска.

Если у вас есть вопрос по разработке, вы можете найти ответ на него в нашей документации, [примерах на сайте GitHub](https://github.com/azure-samples) или на сайте [Stack Overflow](https://www.stackoverflow.com).

### <a name="scoped-search"></a>Поиск в заданных областях

Чтобы получить результаты быстрее, задайте области поиска, указав Stack Overflow, нашу документацию и примеры кода в удобной для вас поисковой системе, используя следующий запрос:

```
{Your Search Terms} (site:stackoverflow.com OR site:docs.microsoft.com OR site:github.com/azure-samples OR site:cloudidentity.com OR site:developer.microsoft.com/graph)
```

*{Your Search Terms}*  — это ключевые слова для поиска.

## <a name="use-the-development-support-tools"></a>Использование средств технической поддержки для разработки

| Средство  | Описание  |
|---------|---------|
| [jwt.ms](https://jwt.ms) | Вставьте идентификатор или маркер доступа, чтобы декодировать имена и значения утверждений. |
| [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)| Средство для выполнения запросов и получения ответов по API Microsoft Graph. |

## <a name="post-a-question-to-stack-overflow"></a>Публикация вопроса на сайте Stack Overflow

Stack Overflow — это идеальное место для вопросов, связанных с разработкой. Здесь члены сообщества разработчиков и команды Майкрософт постараются помочь вам решить проблемы.

Если вы не нашли ответ на свой вопрос через поиск, опубликуйте его на сайте Stack Overflow. При этом используйте один из указанных ниже тегов. Это поможет сообществу своевременно определить ваш вопрос и ответить на него.

|Компонент/область  | Теги |
|---------|---------|
| Библиотека ADAL | [[adal]](https://stackoverflow.com/questions/tagged/adal) |
| Библиотека MSAL     | [[msal]](https://stackoverflow.com/questions/tagged/msal) |
| ПО промежуточного слоя OWIN  | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |
| [Azure B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)  | [[azure-ad-b2b]](https://stackoverflow.com/questions/tagged/azure-ad-b2b) |
| [Azure B2C](https://azure.microsoft.com/services/active-directory-b2c/)  | [[azure-ad-b2c]](https://stackoverflow.com/questions/tagged/azure-ad-b2c) |
| [API Microsoft Graph](https://developer.microsoft.com/graph/) | [[microsoft-graph]](https://stackoverflow.com/questions/tagged/microsoft-graph) |
| Любая другая область, которая относится к аутентификации или авторизации | [[azure-active-directory]](https://stackoverflow.com/questions/tagged/azure-active-directory) |

Указанные ниже публикации на Stack Overflow содержат советы о том, как формулировать вопросы и добавлять исходный код. Следуя этим рекомендациям, вы повысите вероятность того, что участники сообщества быстро отреагируют на ваш вопрос.

* [How do I ask a good question](https://stackoverflow.com/help/how-to-ask) (Как правильно задать вопрос).
* [How to create a Minimal, Complete, and Verifiable example](https://stackoverflow.com/help/mcve) (Как создать минимальный, полный и доступный для проверки пример)

## <a name="create-a-github-issue"></a>Сообщение о проблеме на GitHub

Если возникла ошибка или проблема, связанная с нашими библиотеками, сообщите о ней на сайте GitHub. Так как в наших библиотеках используется открытый код, вы также можете отправить запрос на вытягивание.

Список библиотек и их репозиториев GitHub см. в следующих статьях:

* Библиотеки и репозитории GitHub [ADAL](active-directory-authentication-libraries.md).
* [MSAL.NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) [MSAL. js](https://github.com/AzureAD/microsoft-authentication-library-for-js/blob/dev/lib/msal-angularjs/README.md), [MSAL. Android](https://github.com/AzureAD/microsoft-authentication-library-for-android)и [MSAL. obj _C](https://github.com/AzureAD/microsoft-authentication-library-for-objc) библиотеки и репозитории GitHub

## <a name="open-a-support-request"></a>Подача запроса в службу поддержки

Если вам требуется консультация, подайте запрос в службу поддержки. Для клиентов Azure предусмотрено несколько вариантов поддержки. Сравнить планы можно на [этой странице](https://azure.microsoft.com/support/plans/). Клиенты Azure также могут получить поддержку уровня Developer. Сведения о приобретении планов для поддержки уровня Developer см. на [этой странице](https://azure.microsoft.com/support/plans/developer/).

* Если у вас уже есть план поддержки Azure, [подайте запрос в службу поддержки здесь](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

* Если вы не являетесь клиентом Azure, можно подать запрос в корпорацию Майкрософт на странице [коммерческой поддержки](https://support.microsoft.com/en-us/gp/contactus81?Audience=Commercial).

Также можно получить поддержку или задать вопрос при помощи [виртуального агента](https://support.microsoft.com/contactus/?ws=support).
