---
title: Проверка подлинности на основе пароля с помощью Azure Active Directory
description: Руководство по архитектуре при достижении этого шаблона проверки подлинности
services: active-directory
author: BarbaraSelden
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: fundamentals
ms.topic: conceptual
ms.date: 10/10/2020
ms.author: baselden
ms.reviewer: ajburnle
ms.custom: it-pro, seodec18
ms.collection: M365-identity-device-management
ms.openlocfilehash: f05bf8f5f7ec4907c2cd61ff48e3438dfa1e097a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114361"
---
# <a name="password-based-authentication-with-azure-active-directory"></a>Проверка подлинности на основе пароля с помощью Azure Active Directory

Единый Sign-On на основе пароля (SSO) использует существующий процесс проверки подлинности для приложения. При включении единого входа на основе пароля Azure Active Directory (Azure AD) собирает, шифрует и безопасно сохраняет учетные данные пользователя в каталоге. Azure AD предоставляет приложению имя пользователя и пароль, когда пользователь пытается войти в систему.

Выберите единый вход на основе пароля, когда приложение выполняет проверку подлинности с помощью имени пользователя и пароля вместо маркеров доступа и заголовков. ЕДИНЫЙ вход на основе пароля поддерживает любое облачное приложение, в котором есть страница входа на основе HTML. 

## <a name="use-when"></a>Используется, если

Необходимо защитить с помощью предварительной проверки подлинности и предоставить единый вход через хранилище паролей для веб-приложений.

![Схема архитектуры](./media/authentication-patterns/password-based-sso-auth.png)


## <a name="components-of-system"></a>Компоненты системы

* **Пользователь**: доступ к созданному на основе приложения приложению из моих приложений или путем непосредственного посещения сайта. 

* **Веб-браузер**. компонент, с которым взаимодействует пользователь, чтобы получить доступ к внешнему URL-адресу приложения. Пользователь обращается к приложению на основе форм через расширение MyApps. 

* **Расширение MyApps**: определяет настроенное приложение единого входа на основе пароля и предоставляет учетные данные для входа в форму. Расширение MyApps устанавливается в веб-браузере. 

* **Azure AD**: проверяет подлинность пользователя.

## <a name="implement-password-based-sso-with-azure-ad"></a>Реализация единого входа на основе пароля с помощью Azure AD

* [Что такое единый вход на основе пароля](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) 

* [Настройка единого входа на основе пароля для облачных приложений ](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-password-single-sign-on-non-gallery-applications)

* [Настройка единого входа на основе пароля для локальных приложений с помощью прокси приложения](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-password-vaulting)

 
