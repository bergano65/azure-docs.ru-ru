---
title: Определения файлов cookie
titleSuffix: Azure AD B2C
description: Предоставляет определения для файлов cookie, используемых в Azure Active Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 01/23/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: b984b75b3a12606aa0d82c7e7b399d5dce59df33
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78189520"
---
# <a name="cookies-definitions-for-azure-ad-b2c"></a>Определения файлов cookie для Azure AD B2C

В следующих разделах приводится информация о файлах cookie, используемых в Azure Active Directory B2C (Azure AD B2C).

## <a name="samesite"></a>SameSite

Служба Microsoft Azure AD B2C совместима с конфигурациями браузеров SameSite, включая поддержку `SameSite=None` атрибута. `Secure`

Чтобы защитить доступ к сайтам, веб-браузеры введут новую безопасную по умолчанию модель, которая предполагает, что все файлы cookie должны быть защищены от внешнего доступа, если не указано иное. Браузер Chrome является первым для реализации этого изменения, начиная с [Chrome 80 в феврале 2020 .](https://www.chromium.org/updates/same-site) Для получения дополнительной информации о подготовке к изменениям в Chrome, [см. Безопасные настройки cookie](https://blog.chromium.org/2019/10/developers-get-ready-for-new.html) на блоге Chromium.

Разработчики должны использовать новые настройки cookie, `SameSite=None`чтобы обозначить файлы cookie для кросс-сайта доступа. При `SameSite=None` присутствуют атрибуты, `Secure` необходимо использовать дополнительный атрибут, чтобы до подключения к веб-сайтам можно было получить доступ только по соединениям HTTPS. Проверяйте и проверяйте все приложения, включая приложения, которые используют Azure AD B2C.

Дополнительные сведения см. в разделе:

* [Обработка изменений свойства SameSite в файлах cookie в браузере Chrome](../active-directory/develop/howto-handle-samesite-cookie-changes-chrome-browser.md)
* [Влияние на веб-сайты клиентов и службы и продукты Майкрософт в версии Chrome 80 или позже](https://support.microsoft.com/help/4522904/potential-disruption-to-customer-websites-in-latest-chrome)

## <a name="cookies"></a>Файлы cookie

В следующей таблице перечислены файлы cookie, используемые в Azure AD B2C.

| name | Домен | Окончание срока действия | Назначение |
| ----------- | ------ | -------------------------- | --------- |
| `x-ms-cpim-admin` | main.b2cadmin.ext.azure.com | Конец [сеанса браузера](session-behavior.md) | Хранит данные о членстве пользователей между арендаторами. Арендаторы, в число участников и уровня членства (Админ или Пользователь). |
| `x-ms-cpim-slice` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Используется для маршрутизатения запросов в соответствующий производственный экземпляр. |
| `x-ms-cpim-trans` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Используется для отслеживания транзакций (количество запросов на аутентификацию в Azure AD B2C) и текущей транзакции. |
| `x-ms-cpim-sso:{Id}` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Используется для поддержания сеанса SSO. |
| `x-ms-cpim-cache:{id}_n` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера,](session-behavior.md)успешная аутентификация | Используется для поддержания состояния запроса. |
| `x-ms-cpim-csrf` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Кросс-сайт Запрос Подделка маркер, используемый для защиты CRSF. |
| `x-ms-cpim-dc` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Используется для разгрома сети Azure AD B2C. |
| `x-ms-cpim-ctx` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Контекст |
| `x-ms-cpim-rp` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Используется для хранения данных о членстве для арендатора ресурсо-провайдера. |
| `x-ms-cpim-rc` | b2clogin.com, login.microsoftonline.com, фирменный домен | Конец [сеанса браузера](session-behavior.md) | Используется для хранения ретранслятора cookie. |
