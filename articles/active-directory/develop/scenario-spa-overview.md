---
title: Обзор сценария одностраничного приложения JavaScript - платформой Microsoft identity
description: Узнайте, как создать одностраничное приложение (Обзор сценария), который интегрируется с платформой Microsoft identity.
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 07a21e83f304f3e1acc0ed4033d832dd8e901ac9
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65076365"
---
# <a name="scenario-single-page-application"></a>Сценарий. Одностраничное приложение

Узнайте все необходимое для создания одностраничных приложений (SPA).

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Приступая к работе

Можно создать свое первое приложение, выполнив это краткое руководство одностраничного приложения JavaScript:

> [!div class="nextstepaction"]
> [Краткое руководство Одностраничное приложение](./quickstart-v2-javascript.md)

## <a name="overview"></a>Обзор

Многие современные веб-приложения создаются как клиентские одностраничного приложения, написанные с использованием JavaScript или платформы SPA, например Angular, Vue.js и React.js. Эти приложения выполняются в веб-браузер и имеют характеристики проверки подлинности, отличный от традиционных серверных веб-приложений. Платформе Microsoft identity позволяет приложениям одной страницей входа в систему и получить маркеры для доступа к внутренним службам или веб-API-интерфейсов с помощью [неявного потока OAuth 2.0](./v2-oauth2-implicit-grant-flow.md). Неявный поток позволяет приложению получить маркеры Идентификации представляют пользователя, прошедшего проверку подлинности и маркеры, требовалось вызывать защищенный API также доступа.

![Одностраничные приложения](./media/scenarios/spa-app.svg)

Этот поток проверки подлинности не поддерживает сценарии приложений, с помощью кросс платформенных инфраструктур JavaScript, например Electron, React-Native и т. д. так как они требуют дополнительные возможности для взаимодействия с платформ.

## <a name="specifics"></a>Особенности

Для реализации этого сценария для приложения требуются следующие аспекты:

* Регистрация приложения с Azure AD включает в себя включения неявного потока и установки URI перенаправления, в который возвращаются маркеры.
* Конфигурация приложения со свойствами зарегистрированного приложения, такие как идентификатор приложения.
* Использование библиотеки MSAL для выполнения потока проверки подлинности для входа и получения маркеров.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-spa-app-registration.md)
