---
title: Определения файлов cookie — Azure Active Directory B2C | Документация Майкрософт
description: Предоставляет определения для файлов cookie, используемые в Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: ac422a00a919903063c96ac096882036b99a63e3
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59786750"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Определения файлов cookie для Azure Active Directory B2C

В следующей таблице перечислены файлы cookie, использованные в Azure Active Directory B2C.

| ИМЯ | Домен | Окончание срока действия | Назначение |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Содержит данных о членстве пользователя на клиентах. Клиентов, когда пользователь является членом объекта и уровней членства (администратора или пользователя). |
| x-ms-cpim-slice | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется, чтобы перенаправлять запросы в соответствующий рабочий экземпляр. |
| x-ms-cpim-trans | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для отслеживания транзакций (количество запросов проверки подлинности в Azure AD B2C) и текущей транзакции. |
| x-ms-cpim-sso:{Id} | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для обработки сеанса единого входа. |
| x-ms-cpim-cache:{id}_n | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md), успешной проверки подлинности | Используется для обработки состояния запроса. |
| x-ms-cpim-csrf | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Подделкой межсайтовых токен, используемый для защиты CRSF. |
| x-ms-cpim-dc | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для маршрутизации сети в Azure AD B2C. |
| x-ms-cpim-ctx | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Context |
| x-ms-cpim-rp | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для хранения данных о членстве для клиента поставщика ресурса. |
| x-ms-cpim-rc | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для хранения файла cookie ретрансляции. |

