---
title: Сценарий приложения с одной страницей JavaScript — платформа Microsoft Identity | Службы
description: Узнайте, как создать одностраничное приложение (обзор сценария) с помощью платформы Microsoft Identity.
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
ms.openlocfilehash: 6ab7b0f1e59504eae64422fd4bbaf6f9e0804ed4
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74919823"
---
# <a name="scenario-single-page-application"></a>Сценарий: одностраничное приложение

Узнайте все, что нужно для создания одностраничного приложения (SPA).

## <a name="prerequisites"></a>Технические условия

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Начало работы

Вы можете создать свое первое приложение, следуя руководству по JavaScript SPA:

> [!div class="nextstepaction"]
> [Краткое руководство. одностраничное приложение](./quickstart-v2-javascript.md)

## <a name="overview"></a>Краткое описание

Многие современные веб-приложения создаются как одностраничные приложения на стороне клиента. Разработчики пишут их с помощью JavaScript или платформы SPA, например, под углом, Vue. js и реагируют. js. Эти приложения работают в веб-браузере и имеют различные характеристики проверки подлинности, чем традиционные веб-приложения на стороне сервера. 

Платформа Microsoft Identity позволяет одностраничным приложениям входить в систему пользователей и получать маркеры для доступа к серверным службам или веб-API с помощью [неявного потока OAuth 2,0](./v2-oauth2-implicit-grant-flow.md). Неявный поток позволяет приложению получать маркеры ИДЕНТИФИКАТОРов для представления пользователя, прошедшего проверку подлинности, а также маркеры, необходимые для вызова защищенных API.

![Одностраничные приложения](./media/scenarios/spa-app.svg)

Этот поток проверки подлинности не включает сценарии приложений, в которых используются межплатформенные платформы JavaScript, такие как электронное и собственное реагирование. Для взаимодействия с собственными платформами требуются дополнительные возможности.

## <a name="specifics"></a>Особенности

Чтобы включить этот сценарий для приложения, вам потребуется:

* Регистрация приложения с помощью Azure Active Directory (Azure AD). Эта регистрация включает в себя включение неявного потока и задание URI перенаправления, в который возвращаются токены.
* Конфигурация приложения с зарегистрированными свойствами приложения, такими как идентификатор приложения.
* Использование библиотеки проверки подлинности Майкрософт (MSAL) для проверки подлинности при входе и получении маркеров.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Интеграция приложений с Azure Active Directory](scenario-spa-app-registration.md)
