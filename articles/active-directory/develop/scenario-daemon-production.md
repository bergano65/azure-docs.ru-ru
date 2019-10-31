---
title: Приложение управляющей программы, вызывающее веб-API (переместить в рабочую среду) — платформа Microsoft Identity
description: Узнайте, как создать управляющее приложение, вызывающее веб-API (переместить в рабочую среду).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/30/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 05418cde7b31392c1a55f64945764e978daba1bf
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175391"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Приложение управляющей программы, вызывающее веб-API — переместить в рабочую среду

Теперь, когда вы узнали, как получить и использовать маркер для вызова между службами, Узнайте, как переместить приложение в рабочую среду.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Развертывание — случаи использования приложений управляющей программы с несколькими клиентами

Если вы являетесь независимым поставщиком программного обеспечения, создающим управляющее приложение, которое может работать в нескольких клиентах, необходимо убедиться в том, что администраторы клиента:

- Подготовка субъекта-службы для приложения
- Предоставление согласия для приложения

Вам необходимо объяснить своим заказчикам, как выполнять эти операции. Дополнительные сведения см. в разделе [запрос согласия для всего клиента](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вот несколько ссылок для получения дополнительных сведений:

# <a name="nettabdotnet"></a>[.NET](#tab/dotnet)

- Если вы еще не сделали это, попробуйте [получить маркер и вызовите Microsoft Graph API из консольного приложения, используя удостоверение приложения](./quickstart-v2-netcore-daemon.md).
- Справочная документация по:
  - Создание экземпляра [конфидентиалклиентаппликатион](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Вызов [аккуиретокенфорклиент](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Другие примеры и учебники:
  - [Microsoft-Identity-Platform-Console-DAEMON —](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) это простое консольное приложение управляющей программы .NET Core, в котором отображаются пользователи клиента, которые запрашивают Microsoft Graph.

    ![Топология](media/scenario-daemon-app/daemon-app-sample.svg)

    В этом же примере также иллюстрируется вариант с сертификатами.

    ![Топология](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-DAEMON](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) включает в себя веб-приложение ASP.NET MVC, которое синхронизирует данные из Microsoft Graph с помощью удостоверения приложения, а не от имени пользователя. В этом примере также иллюстрируется процесс согласия администратора.

    ![Топология](media/scenario-daemon-app/damon-app-sample-web.svg)

# <a name="pythontabpython"></a>[Python](#tab/python)

Попробуйте [получить маркер и вызвать Microsoft Graph API из консольного приложения Python, используя удостоверение приложения](./quickstart-v2-python-daemon.md).

# <a name="javatabjava"></a>[Java](#tab/java)

MSAL Java в настоящее время находится в общедоступной предварительной версии. Дополнительные сведения см. в статье [MSAL Java dev Samples](https://github.com/AzureAD/microsoft-authentication-library-for-java/tree/dev/src/samples).

---
