---
title: Приложения на рабочем столе, вызовы веб-API-интерфейсы (Обзор) - платформой Microsoft identity
description: Научитесь создавать приложения для настольных систем, вызовы веб-API-интерфейсы (Обзор)
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
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
ms.openlocfilehash: 44d31011ca70bbebaf994b5fb80a45eee8dbde40
ms.sourcegitcommit: 0ae3139c7e2f9d27e8200ae02e6eed6f52aca476
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65076950"
---
# <a name="scenario-desktop-app-that-calls-web-apis"></a>Сценарий. Классическое приложение, что вызовы веб-API

Узнайте все необходимое для создания классического приложения, которое вызывает веб-API

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Pre-requisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Приступая к работе

Если это еще не сделано, создайте свое первое приложение, выполнив рабочего стола краткого руководства по .NET или быстрого запуска универсальной платформы Windows:

> [!div class="nextstepaction"]
> [Краткое руководство Получить маркер и вызвать Microsoft Graph API из классического приложения Windows](./quickstart-v2-windows-desktop.md)


> [!div class="nextstepaction"]
> [Краткое руководство Получение маркера и вызов Microsoft Graph API из приложения универсальной платформы Windows](./quickstart-v2-uwp.md)

## <a name="overview"></a>Обзор

Запись классического приложения, и требуется вход пользователей в приложение и вызова веб-API, например Microsoft Graph, другие Microsoft API или собственных веб-API. У вас есть несколько вариантов:

- Когда приложение рабочего стола поддерживает графические элементы управления, например если Windows.Form приложение или приложение WPF, можно использовать интерактивного получения маркера.
- Для приложений, размещенных Windows можно также для приложений, работающих на компьютерах, присоединенных к домену Windows или присоединенные к AAD, чтобы получить маркер автоматически с помощью встроенной проверки подлинности Windows.
- Наконец и несмотря на то, что не рекомендуется, можно использовать имя пользователя и пароль в общедоступных клиентских приложений. Он по-прежнему требуется в некоторых сценариях (например, DevOps), но имейте в виду, что с его помощью будет накладывают ограничения на приложение. К примеру он не сможет войти пользователь, которому требуется для выполнения многофакторной проверки подлинности (условный доступ). Также приложение не будет использовать преимущества единого входа (SSO).

  Он также от принципы современную проверку подлинности и предоставляется только для обеспечения обратной совместимости.

  ![Классическое приложение](media/scenarios/desktop-app.svg)

- Если вы пишете переносимой средство командной строки — возможно, приложение .NET Core, работающих под управлением Linux или Mac - вы не сможете использовать ни один из интерактивной проверки подлинности (как .NET Core не предоставляет [веб-браузере](https://aka.ms/msal-net-uses-web-browser)), ни встроенной Проверка подлинности Windows. В этом случае лучше использовать поток кода устройства. Этот поток также используется для приложений без браузера, таких как приложения Интернета вещей

  ![Browserless приложения](media/scenarios/device-code-flow-app.svg)

## <a name="specifics"></a>Особенности

Классические приложения имеют ряд specificities, которого зависит главным образом от ли приложение использует интерактивную проверку подлинности или нет.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Классическое приложение — Регистрация приложения](scenario-desktop-app-registration.md)
