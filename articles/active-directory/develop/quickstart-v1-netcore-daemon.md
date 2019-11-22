---
title: Получение маркера и вызов API Microsoft Graph с удостоверением консольного приложения (версия 1.0) | Документация Майкрософт
description: Из этой статьи вы узнаете, как создать управляющую программу .NET, которая интегрируется с Azure AD и вызывает API, защищенные Azure AD, по протоколу OAuth 2.0.
services: active-directory
documentationcenter: .net
author: rwike77
manager: CelesteDG
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 07/17/2019
ms.author: jmprieur
ms.reviewer: ryanwi
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: d79d448f10c0d404516371be19d561bb21c8c0c7
ms.sourcegitcommit: 0b1a4101d575e28af0f0d161852b57d82c9b2a7e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73149608"
---
# <a name="quickstart-acquire-token-and-call-microsoft-graph-api-with-console-apps-identity-v10"></a>Краткое руководство. Получение маркера и вызов API Microsoft Graph с удостоверением консольного приложения (версия 1.0) 

[Платформа удостоверений Майкрософт](v2-overview.md) — это решение на основе платформы для разработчиков Azure Active Directory (Azure AD). С ее помощью разработчики могут создавать приложения, которые обеспечивают вход с использованием любых удостоверений Майкрософт и получение маркеров для вызова таких программных API-интерфейсов Майкрософт, как Microsoft Graph или других программных интерфейсов, созданных разработчиками.

[Библиотека аутентификации Майкрософт (MSAL)](msal-overview.md) позволяет разработчикам получать маркеры из конечной точки платформы удостоверений Майкрософт, чтобы получить доступ к защищенным веб-API. Библиотека проверки подлинности Active Directory (ADAL) интегрируется с конечной точкой Azure AD для разработчиков (версия 1.0), а MSAL интегрируется с конечной точкой платформы удостоверений Майкрософт (версия 2.0).

## <a name="next-steps"></a>Дополнительная информация

С новыми управляющими программами .NET мы рекомендуем использовать платформу удостоверений Майкрософт (версия 2.0) и MSAL для получения маркеров и доступа к защищенным веб-API: [Краткое руководство Получение маркера безопасности и вызов API Microsoft Graph из консольного приложения с помощью удостоверения приложения](quickstart-v2-netcore-daemon.md).
