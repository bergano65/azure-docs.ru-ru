---
title: Переместите приложение daemon, которое вызывает веб-AIS в производство - платформа идентификации Microsoft (ru) Azure
description: Узнайте, как переместить приложение daemon, которое вызывает веб-AIS в производство
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: e5d25169aa3daee33a9f02e605cbff91776dc7f1
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885435"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Daemon приложение, которое вызывает веб-AIS - перейти к производству

Теперь, когда вы знаете, как приобрести и использовать токен для вызова службы в службу, узнайте, как переместить приложение в производство.

## <a name="deployment---multitenant-daemon-apps"></a>Развертывание - мультитенантные приложения daemon

Если вы isV создаете приложение daemon, которое может работать в нескольких арендаторах, необходимо убедиться, что админ-арендатор:

- Обеспечивает директора службы для приложения.
- Гранты дают согласие на применение.

Вам нужно будет объяснить своим клиентам, как выполнять эти операции. Для получения дополнительной [информации см.](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant)

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вот несколько ссылок, которые помогут вам узнать больше:

# <a name="net"></a>[.NET](#tab/dotnet)

- Быстрый запуск: [Приобрести токен и вызов Microsoft Graph API из консольного приложения, используя личность приложения.](./quickstart-v2-netcore-daemon.md)
- Справочная документация для:
  - Мгновенное [конфиденциальноеклиентноеприложение](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Вызов [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Другие образцы/учебники:
  - [Microsoft-идентификация-платформа-консоль-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) имеет простое приложение консоли .NET Core daemon, которое отображает пользователей арендатора запросmicrosoft Graph.

    ![Пример топологии приложения daemon](media/scenario-daemon-app/daemon-app-sample.svg)

    В той же выборке также иллюстрируется вариация с сертификатами:

    ![Пример топологии приложения daemon - сертификаты](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-идентификация-платформа-aspnet-webapp-daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) имеет ASP.NET MVC веб-приложение, которое синхронизирует данные из Microsoft Graph, используя личность приложения, а не от имени пользователя. Этот пример также иллюстрирует процесс согласия админ.

    ![Топология](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="python"></a>[Python](#tab/python)

Попробуйте быстрый запуск [Приобрести маркер и вызов Microsoft Graph API из приложения консоли Python, используя личность приложения.](./quickstart-v2-python-daemon.md)

# <a name="java"></a>[Java](#tab/java)

MSAL Java в настоящее время находится в открытом предварительном просмотре. Для получения дополнительной информации [см.](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples)

---
