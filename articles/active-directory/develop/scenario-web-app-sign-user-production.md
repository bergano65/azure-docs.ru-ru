---
title: Переместить веб-приложение, которое выполняет вход пользователей в рабочую среду | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как создать веб-приложение, которое входит в систему пользователей (переходит в рабочую среду).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/17/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: c7abad31c9936729b8d9c19ed2efcb841ac103ca
ms.sourcegitcommit: 5cdd0b378d6377b98af71ec8e886098a504f7c33
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/25/2021
ms.locfileid: "98756235"
---
# <a name="web-app-that-signs-in-users-move-to-production"></a>Веб-приложение, которое входит в систему пользователей: перейти в рабочую среду

Теперь, когда вы узнали, как получить маркер для вызова веб-API, Узнайте, как переместить его в рабочую среду.

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="troubleshooting"></a>Устранение неполадок

> [!NOTE]
> Когда пользователи впервые входят в веб-приложение, им необходимо предоставить согласие. Однако в некоторых организациях пользователи могут видеть примерно следующее сообщение:
>
> *Приложению AppName нужны разрешения на доступ к ресурсам в вашей организации, которые может предоставить только администратор. Попросите администратора предоставить разрешение для этого приложения, прежде чем его можно будет использовать.*
>
> Это связано с тем, что администратор клиента **отключил** возможность пользователей принять согласие. В этом случае вам нужно обратиться к администраторам клиента, чтобы они выводили согласие администратора для областей, необходимых для приложения.

## <a name="same-site"></a>Тот же сайт

Убедитесь, что вы понимаете возможные проблемы с новыми версиями браузера Chrome: [как управлять изменениями файлов cookie SameSite в браузере Chrome](howto-handle-samesite-cookie-changes-chrome-browser.md).

Пакет Microsoft. Identity. Web NuGet обрабатывает наиболее распространенные проблемы SameSite.

## <a name="deep-dive-aspnet-core-web-app-tutorial"></a>Глубокое углубление: учебник по веб-приложениям ASP.NET Core

Узнайте о других способах входа пользователей с помощью этого ASP.NET Core учебнике: 

[Разрешите веб-приложениям выполнять вход пользователей и вызывать API с помощью платформы удостоверений Майкрософт для разработчиков](https://github.com/Azure-Samples/ms-identity-aspnetcore-webapp-tutorial)

В этом последовательном руководстве есть готовый к использованию код для веб-приложения, включая добавление учетных записей в:

- Ваша организация
- Несколько организаций
- Рабочие или учебные учетные записи или личные учетные записи Майкрософт
- [Azure AD B2C](../../active-directory-b2c/overview.md)
- Национальные облака

## <a name="sample-code-java-web-app"></a>Пример кода: веб-приложение Java

Дополнительные сведения о веб-приложении Java из этого примера на сайте GitHub: 

[Веб-приложение Java, которое входит в систему пользователей с платформой идентификации Майкрософт и вызывает Microsoft Graph](https://github.com/Azure-Samples/ms-identity-java-webapp)

## <a name="next-steps"></a>Next Steps

После того как веб-приложение подписывает пользователей, оно может вызывать веб-API от имени вошедших в систему пользователей. Вызов веб-API из веб-приложения является объектом следующего сценария: [веб-приложение, вызывающее веб-API](scenario-web-app-call-api-overview.md).