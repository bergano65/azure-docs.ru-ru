---
title: Обзор сценария одностраничного приложения JavaScript — платформа Microsoft Identity
description: Узнайте, как создать одностраничное приложение (обзор сценария), которое интегрирует платформу Microsoft Identity.
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
ms.custom: aaddev, identityplatformtop40
ms.collection: M365-identity-device-management
ms.openlocfilehash: c3c4e671473ff6c6fecdc13fe61dbde1d3fb3809
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68852518"
---
# <a name="scenario-single-page-application"></a>Сценарий: Одностраничное приложение

Узнайте все, что нужно для создания одностраничного приложения (SPA).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Приступая к работе

Вы можете создать свое первое приложение, следуя руководству по JavaScript SPA:

> [!div class="nextstepaction"]
> [Краткое руководство Одностраничное приложение](./quickstart-v2-javascript.md)

## <a name="overview"></a>Обзор

Многие современные веб-приложения создаются в качестве одностраничных клиентских приложений, написанных с использованием JavaScript или платформы SPA, таких как Angular, Vue.js и React.js. Эти приложения работают в веб-браузере и имеют характеристики проверки подлинности, отличные от традиционных серверных веб-приложений. Платформа Microsoft Identity позволяет одностраничным приложениям входить в систему пользователей и получать маркеры для доступа к серверным службам или веб-API с помощью неявного [потока OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Неявный поток позволяет приложению получать маркеры ИДЕНТИФИКАТОРов для представления пользователя, прошедшего проверку подлинности, а также маркеры, необходимые для вызова защищенных API.

![Одностраничные приложения](./media/scenarios/spa-app.svg)

Этот поток проверки подлинности не включает сценарии приложений, использующие межплатформенные платформы JavaScript, такие как электронное и аппаратное реагирование и т. д. так как для взаимодействия с собственными платформами требуются дополнительные возможности.

## <a name="specifics"></a>Особенности

Чтобы включить этот сценарий для приложения, необходимы следующие аспекты.

* Регистрация приложений в Azure AD включает в себя включение неявного потока и задание URI перенаправления, в который возвращаются токены.
* Конфигурация приложения с зарегистрированными свойствами приложения, такими как идентификатор приложения.
* Использование библиотеки MSAL для выполнения входа и получения маркеров в потоке проверки подлинности.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-spa-app-registration.md)
