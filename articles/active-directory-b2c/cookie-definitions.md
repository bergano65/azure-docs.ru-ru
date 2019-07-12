---
title: Определения файлов cookie — Azure Active Directory B2C | Документация Майкрософт
description: Предоставляет определения для файлов cookie, используемые в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: af3244a32e9d02a1ba5053da85547bf614053127
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67587416"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Определения файлов cookie для Azure Active Directory B2C

В следующей таблице перечислены файлы cookie, использованные в Azure Active Directory B2C.

| Имя | Домен | Окончание срока действия | Цель |
| ----------- | ------ | -------------------------- | --------- |
| x-ms-cpim-admin | main.b2cadmin.ext.azure.com | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Содержит данных о членстве пользователя на клиентах. Клиентов, когда пользователь является членом объекта и уровней членства (администратора или пользователя). |
| x-ms-cpim-slice | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется, чтобы перенаправлять запросы в соответствующий рабочий экземпляр. |
| x-ms-cpim-trans | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для отслеживания транзакций (количество запросов проверки подлинности в Azure AD B2C) и текущей транзакции. |
| x-ms-cpim-sso:{Id} | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для обработки сеанса единого входа. |
| x-ms-cpim-cache:{id}_n | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md), успешной проверки подлинности | Используется для обработки состояния запроса. |
| x-ms-cpim-csrf | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Подделкой межсайтовых токен, используемый для защиты CRSF. |
| x-ms-cpim-dc | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для маршрутизации сети в Azure AD B2C. |
| x-ms-cpim-ctx | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Контекст |
| x-ms-cpim-rp | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для хранения данных о членстве для клиента поставщика ресурса. |
| x-ms-cpim-rc | Login.microsoftonline.com, b2clogin.com, фирменный домен | Окончание [сеанс браузера](active-directory-b2c-token-session-sso.md) | Используется для хранения файла cookie ретрансляции. |

