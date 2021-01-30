---
title: Перемещение управляющего приложения, вызывающего веб-API, в рабочую среду | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как переместить управляющее приложение, которое вызывает веб-API в рабочую среду.
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
ms.openlocfilehash: 04ee4ccc3b0553d6532869d6403000fdedd93b5f
ms.sourcegitcommit: 1a98b3f91663484920a747d75500f6d70a6cb2ba
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2021
ms.locfileid: "99062717"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Приложение управляющей программы, вызывающее веб-API — переместить в рабочую среду

Теперь, когда вы узнали, как получить и использовать маркер для вызова между службами, Узнайте, как переместить приложение в рабочую среду.

## <a name="deployment---multitenant-daemon-apps"></a>Развертывание — многоклиентские приложения управляющей программы

Если вы являетесь независимым поставщиком программного обеспечения, создающим управляющее приложение, которое может работать в нескольких клиентах, необходимо убедиться в том, что администратор клиента выполняет следующие действия.

- Подготавливает субъект-службу для приложения.
- Предоставляет согласие для приложения.

Вам необходимо объяснить своим заказчикам, как выполнять эти операции. Дополнительные сведения см. в разделе [запрос согласия для всего клиента](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Common steps to move to production](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="code-samples"></a>Примеры кода

# <a name="net"></a>[.NET](#tab/dotnet)

- Справочная документация по:
  - Создание экземпляра [конфидентиалклиентаппликатион](/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder).
  - Вызов [аккуиретокенфорклиент](/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder).
- Другие примеры и учебники:
  - [Microsoft-Identity-Platform-Console-DAEMON —](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) это небольшое консольное приложение управляющей программы .NET Core, в котором отображаются пользователи запросов клиентов Microsoft Graph.

    ![Пример топологии приложения управляющей программы](media/scenario-daemon-app/daemon-app-sample.svg)

    В этом же примере также показан вариант с сертификатами:

    ![Пример топологии приложения управляющей программы — сертификаты](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-DAEMON](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) включает в себя веб-приложение ASP.NET MVC, которое синхронизирует данные из Microsoft Graph с помощью удостоверения приложения, а не от имени пользователя. Этот пример также иллюстрирует процесс согласия администратора.

    ![Топология](media/scenario-daemon-app/damon-app-sample-web.svg)

---

## <a name="next-steps"></a>Дальнейшие действия

Вот несколько ссылок, которые помогут вам получить дополнительные сведения:

# <a name="python"></a>[Python](#tab/python)

Попробуйте [получить маркер и вызвать Microsoft Graph API из консольного приложения Python, используя удостоверение приложения](./quickstart-v2-python-daemon.md).

# <a name="java"></a>[Java](#tab/java)

Попробуйте [получить маркер и вызвать Microsoft Graph API из консольного приложения Java, используя удостоверение приложения](./quickstart-v2-java-daemon.md).

---
