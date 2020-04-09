---
title: Сценарий одностраничного приложения JavaScript - идентификационная платформа Майкрософт Azure
description: Узнайте, как создать одностраничное приложение (обзор сценариев) с помощью платформы идентификации Майкрософт.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/07/2019
ms.author: nacanuma
ms.custom: aaddev, identityplatformtop40
ms.openlocfilehash: 3ead0ea58c6860519f027eb6a7450df37396bd89
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80885180"
---
# <a name="scenario-single-page-application"></a>Сценарий: Одностраничное приложение

Узнайте все, что вам нужно для создания одностраничного приложения (SPA).

## <a name="prerequisites"></a>Предварительные требования

[!INCLUDE [Prerequisites](../../../includes/active-directory-develop-scenarios-prerequisites.md)]

## <a name="getting-started"></a>Начало работы

Вы можете создать свое первое приложение, следуя quickstart JavaScript SPA:

> [!div class="nextstepaction"]
> [Быстрый запуск: Одностраничное приложение](./quickstart-v2-javascript.md)

## <a name="overview"></a>Обзор

Многие современные веб-приложения построены как одностраничные приложения на стороне клиента. Разработчики пишут их с помощью JavaScript или платформы SPA, такой как Angular, Vue.js и React.js. Эти приложения работают в веб-браузере и имеют различные характеристики аутентификации, чем традиционные веб-приложения на стороне сервера. 

Платформа идентификации Майкрософт позволяет одностраничным приложениям подписывать сява в число пользователей и получать токены для доступа к бэк-эндовым службам или веб-AIS с помощью [неявного потока OAuth 2.0.](./v2-oauth2-implicit-grant-flow.md) Неявный поток позволяет приложению получать токены ID для представления аутентифицированных пользователей, а также маркеров доступа, необходимых для вызова защищенных AIS.

![Одностраничные приложения](./media/scenarios/spa-app.svg)

Этот поток аутентификации не включает сценарии приложений, в которых используются кроссплатформенные платформы JavaScript, такие как Electron и React-Native. Они требуют дополнительных возможностей для взаимодействия с родными платформами.

## <a name="specifics"></a>Специфики

Для включения этого сценария для приложения необходимо:

* Регистрация заявлений в Active Directory Azure (Azure AD). Эта регистрация включает в себя включение неявного потока и установку перенаправления URI, к которому возвращаются токены.
* Конфигурация приложения с зарегистрированными свойствами приложения, такими как идентификатор приложения.
* Использование библиотеки аутентификации Майкрософт (MSAL) для проверки подлинности потока для входинии и приобретения токенов.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Регистрация приложения](scenario-spa-app-registration.md)
