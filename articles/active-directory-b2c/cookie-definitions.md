---
title: Определения файлов cookie
titleSuffix: Azure AD B2C
description: Содержит определения для файлов cookie, используемых в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 399b63cab2594610260997f8e5ecef9c3c05318f
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/24/2020
ms.locfileid: "76712826"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Определения файлов cookie для Azure AD B2C

В следующих разделах содержатся сведения о файлах cookie, используемых в Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

Служба Microsoft Azure AD B2C совместима с конфигурациями браузера SameSite, включая поддержку `SameSite=None` с атрибутом `Secure`.

Чтобы защитить доступ к сайтам, в веб-браузерах будет введена новая модель безопасности по умолчанию, которая предполагает, что все файлы cookie должны быть защищены от внешнего доступа, если не указано иное. Браузер Chrome впервые реализует это изменение, начиная с [Chrome 80 в феврале 2020](https://www.chromium.org/updates/same-site). Дополнительные сведения о подготовке к изменению в Chrome см. в разделе разработчики: Подготовьтесь к [созданию нового SameSite = None; Защита параметров файлов cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) в блоге Chromium.

Чтобы назначить файлы cookie для межсайтового доступа, разработчики должны использовать новый параметр "файл cookie" `SameSite=None`. При наличии атрибута `SameSite=None` необходимо использовать дополнительный атрибут `Secure`, чтобы межсайтовые файлы cookie могли быть доступны только через HTTPS-соединения. Проверка и тестирование всех приложений, в том числе приложений, использующих Azure AD B2C.

Дополнительные сведения см. [в разделе воздействие на веб-сайты клиентов, службы и продукты Майкрософт в Chrome версии 80 или более поздней](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome).

## <a name="cookies"></a>Файлы cookie

В следующей таблице перечислены файлы cookie, используемые в Azure AD B2C.

| Имя | Домен | Окончание срока действия | Цель |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Конец [сеанса браузера](session-behavior.md) | Хранит данные о членстве пользователей в клиентах. Клиенты, членом которых является пользователь и уровень членства (администратор или пользователь). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для маршрутизации запросов к соответствующему рабочему экземпляру. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для отслеживания транзакций (число запросов проверки подлинности Azure AD B2C) и текущей транзакции. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для обслуживания сеанса единого входа. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Окончание [сеанса браузера](session-behavior.md), успешная проверка подлинности | Используется для обслуживания состояния запроса. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Токен подделки межсайтовых запросов, используемый для защиты КРСФ. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для Azure AD B2C сетевой маршрутизации. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Контекст |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для хранения данных о членстве в клиенте поставщика ресурсов. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для хранения файла cookie ретранслятора. |
