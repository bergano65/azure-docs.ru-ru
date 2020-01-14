---
title: Определения файлов cookie — Azure Active Directory B2C | Документация Майкрософт
description: Содержит определения для файлов cookie, используемых в Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 66de4559ed006735f53ff993cce29370428b9998
ms.sourcegitcommit: 014e916305e0225512f040543366711e466a9495
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/14/2020
ms.locfileid: "75930896"
---
# <a name="cookies-definitions-for-azure-active-directory-b2c"></a>Определения файлов cookie для Azure Active Directory B2C

В следующей таблице перечислены файлы cookie, используемые в Azure Active Directory B2C.

| Имя | Домен | Окончание срока действия | Цель |
| ----------- | ------ | -------------------------- | --------- |
| x-MS-cpim-Admin | main.b2cadmin.ext.azure.com | Конец [сеанса браузера](session-behavior.md) | Хранит данные о членстве пользователей в клиентах. Клиенты, членом которых является пользователь и уровень членства (администратор или пользователь). |
| x-MS-cpim-Slice | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для маршрутизации запросов к соответствующему рабочему экземпляру. |
| x-MS-cpim-транзакции | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для отслеживания транзакций (число запросов проверки подлинности Azure AD B2C) и текущей транзакции. |
| x-ms-cpim-sso:{Id} | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для обслуживания сеанса единого входа. |
| x-ms-cpim-cache:{id}_n | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Окончание [сеанса браузера](session-behavior.md), успешная проверка подлинности | Используется для обслуживания состояния запроса. |
| x-ms-cpim-csrf | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Токен подделки межсайтовых запросов, используемый для защиты КРСФ. |
| x-ms-cpim-dc | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для Azure AD B2C сетевой маршрутизации. |
| x-MS-cpim-CTX | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Контекст |
| x-ms-cpim-rp | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для хранения данных о членстве в клиенте поставщика ресурсов. |
| x-ms-cpim-rc | login.microsoftonline.com, b2clogin.com, домен с фирменной символикой | Конец [сеанса браузера](session-behavior.md) | Используется для хранения файла cookie ретранслятора. |
