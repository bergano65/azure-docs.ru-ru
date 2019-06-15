---
title: Вызов приложений управляющей программы веб-API (перемещение в рабочую среду) - платформой Microsoft identity
description: Узнайте, как создать управляющую программу, вызовы веб-API-интерфейсы (перемещение в рабочую среду)
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
ms.date: 05/07/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 627dab0cb23800664c5fb5b3df9c61f5071d4b87
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65545404"
---
# <a name="daemon-app-that-calls-web-apis---move-to-production"></a>Управляющая программа приложение, которое вызывает веб-интерфейсы API — переносу в рабочую среду

Теперь, когда вы знаете, как получить и использовать маркер для вызова между службами, узнайте, как переместить приложение в рабочей среде.

## <a name="deployment---case-of-multi-tenant-daemon-apps"></a>Развертывание – вариант управляющей программы мультитенантных приложений

Если вы являетесь независимым разработчиком, Создавшим Создание управляющее приложение, которое может выполняться в нескольких клиентов, необходимо убедиться, что администраторы клиента:

- Подготавливает субъект-службу для приложения
- Предоставляет согласие для приложения

Необходимо объяснить заказчикам, как для выполнения этих операций. Дополнительные сведения см. в разделе [запрос на получение согласия для клиента](v2-permissions-and-consent.md#requesting-consent-for-an-entire-tenant).

[!INCLUDE [Move to production common steps](../../../includes/active-directory-develop-scenarios-production.md)]

## <a name="next-steps"></a>Дальнейшие действия

Ниже приведены ссылки на дополнительные сведения.

### <a name="net"></a>.NET

- Если вы еще этого не сделали, попробуйте это краткое руководство [получить маркер и вызвать Microsoft Graph API из консольное приложение, используя удостоверение приложения](./quickstart-v2-netcore-daemon.md).
- Справочная документация по:
  - Создание экземпляра [ConfidentialClientApplication](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.confidentialclientapplicationbuilder)
  - Вызов [AcquireTokenForClient](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.acquiretokenforclientparameterbuilder)
- Другие примеры и учебники:
  - [Microsoft-identity платформа консоль daemon](https://github.com/Azure-Samples/microsoft-identity-platform-console-daemon) функции простой управляющей программы консольного приложения .NET Core, отображающий пользователей клиента запрос Microsoft Graph.

    ![топология](media/scenario-daemon-app/daemon-app-sample.svg)

    Одного примера также иллюстрирует вариант с использованием сертификатов.

    ![топология](media/scenario-daemon-app/daemon-app-sample-with-certificate.svg)

  - [Microsoft-Identity-Platform-ASPNET-webapp-Daemon](https://github.com/Azure-Samples/microsoft-identity-platform-aspnet-webapp-daemon) функции веб-приложения ASP.NET MVC, который синхронизирует данные из Microsoft Graph с использованием удостоверения приложения, а не от имени пользователя. Пример также иллюстрирует процесс согласия администратора.

    ![топология](media/scenario-daemon-app/damon-app-sample-web.svg)

### <a name="python"></a>Python

MSAL Python в настоящее время находится в общедоступной предварительной версии. Дополнительные сведения см. в разделе [примера в хранилище учетных данных клиента MSAL Python](https://github.com/AzureAD/azure-activedirectory-library-for-python/blob/dev/sample/client_credentials_sample.py).

### <a name="java"></a>Java

MSAL Python в настоящее время находится в общедоступной предварительной версии. Дополнительные сведения см. в разделе [MSAL Java в репозиторий с примерами](https://github.com/AzureAD/azure-activedirectory-library-for-java/tree/dev/src/samples).