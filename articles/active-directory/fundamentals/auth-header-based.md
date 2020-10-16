---
title: Проверка подлинности на основе заголовка с помощью Azure Active Directory
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
ms.openlocfilehash: 4f364e4e14dd1b7c60cb81f06051d9dedd94396a
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92114456"
---
# <a name="header-based-authentication-with-azure-active-directory"></a>Проверка подлинности на основе заголовка с помощью Azure Active Directory

Устаревшие приложения обычно используют проверку подлинности на основе заголовка. В этом сценарии пользователь (или инициатор сообщений) выполняет проверку подлинности на промежуточном решении идентификации. Промежуточное решение проверяет подлинность пользователя и распространяет требуемые заголовки протокола HTTP в целевую веб-службу. Azure Active Directory (AD) поддерживает этот шаблон через службу прокси приложения, а также интеграцию с другими решениями сетевого контроллера. 

В нашем решении прокси приложения предоставляет удаленный доступ к приложению, выполняет проверку подлинности пользователя и передает заголовки, необходимые для приложения. 

## <a name="use-when"></a>Используется, если

Удаленным пользователям необходимо безопасно выполнять единый вход в локальные приложения, для которых требуется проверка подлинности на основе заголовков.

![Проверка подлинности на основе заголовка образа архитектуры](./media/authentication-patterns/header-based-auth.png)

## <a name="components-of-system"></a>Компоненты системы

* **Пользователь**: обращается к устаревшим приложениям, обслуживаемым прокси приложения.

* **Веб-браузер**. компонент, с которым взаимодействует пользователь, чтобы получить доступ к внешнему URL-адресу приложения.

* **Azure AD**: проверяет подлинность пользователя. 

* **Служба прокси приложения**. действует как обратный прокси-сервер для отправки запроса от пользователя к локальному приложению. Он находится в Azure AD и может также применять политики условного доступа.

* **Соединитель прокси приложения**. установлен локально на серверах Windows для обеспечения подключения к приложениям. Он использует только исходящие подключения. Возвращает ответ на Azure AD.

* **Устаревшие приложения**: приложения, которые получают запросы пользователей от прокси приложения. Устаревшее приложение получает необходимые заголовки HTTP для настройки сеанса и возврата ответа. 

## <a name="implement-header-based-authentication-with-azure-ad"></a>Реализация аутентификации на основе заголовка с помощью Azure AD

* [Добавление локального приложения для удаленного доступа через прокси приложения в Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application)  

* [Аутентификация на основе заголовка для единого входа с использованием прокси приложения и PingAccess](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-single-sign-on-with-ping-access) 

* [Защита устаревших приложений с помощью контроллеров и сетей доставки приложений](https://docs.microsoft.com/azure/active-directory/manage-apps/secure-hybrid-access)
