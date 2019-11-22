---
title: Добавление возможности входа в веб-приложение ASP.NET с помощью учетной записи Майкрософт | Документация Майкрософт
description: Узнайте, как реализовать возможность входа Майкрософт в решении ASP.NET с традиционным браузерным приложением, используя стандарт OpenID Connect.
services: active-directory
documentationcenter: dev-center-name
author: andretms
manager: CelesteDG
editor: ''
ms.assetid: 820acdb7-d316-4c3b-8de9-79df48ba3b06
ms.service: active-directory
ms.subservice: develop
ms.custom: aaddev
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2019
ms.author: ryanwi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 240b64bcaa242e39140f39faff31418f96b34193
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149920"
---
# <a name="quickstart-add-sign-in-with-microsoft-to-an-aspnet-web-app"></a>Краткое руководство. Добавление возможности входа в веб-приложение ASP.NET с помощью учетной записи Майкрософт

[Платформа удостоверений Майкрософт](v2-overview.md) — это решение на основе платформы для разработчиков Azure Active Directory (Azure AD). С ее помощью разработчики могут создавать приложения, которые обеспечивают вход с использованием любых удостоверений Майкрософт и получение маркеров для вызова таких программных API-интерфейсов Майкрософт, как Microsoft Graph или других программных интерфейсов, созданных разработчиками.

[Библиотека аутентификации Майкрософт (MSAL)](msal-overview.md) позволяет разработчикам получать маркеры из конечной точки платформы удостоверений Майкрософт, чтобы получить доступ к защищенным веб-API. Библиотека проверки подлинности Active Directory (ADAL) интегрируется с конечной точкой Azure AD для разработчиков (версия 1.0), а MSAL интегрируется с конечной точкой платформы удостоверений Майкрософт (версия 2.0).

## <a name="next-steps"></a>Дополнительная информация

С новыми веб-приложениями мы рекомендуем использовать платформу удостоверений Майкрософт (версия 2.0) и MSAL для получения маркеров и доступа к защищенным веб-API: См. [Краткое руководство. Добавление возможности входа в веб-приложение ASP.NET с помощью учетной записи Майкрософт](quickstart-v2-aspnet-webapp.md).
