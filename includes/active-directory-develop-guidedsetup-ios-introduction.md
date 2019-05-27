---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: ios
ms.workload: identity
ms.date: 03/20/2019
ms.author: jmprieur
ms.reviwer: brandwe
ms.custom: include file
ms.openlocfilehash: 08849cb44b5a3db3d66dc444d5e84fb3df66ad9a
ms.sourcegitcommit: e9a46b4d22113655181a3e219d16397367e8492d
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65967640"
---
# <a name="call-the-microsoft-graph-api-from-an-ios-application"></a>Вызов API Microsoft Graph из приложения iOS

В этом руководстве описано, как собственное приложение iOS (Swift) может вызывать API, для которых требуются маркеры доступа от конечной точки платформы удостоверений Майкрософт. В этом руководстве описано, как получить маркеры доступа и использовать их в вызовах к API Microsoft Graph и другим API.

Когда вы выполните все инструкции из этого руководства, ваше приложение сможет вызывать защищенный API из любой компании или организации, где используется Azure AD. Для защищенных вызовов API ваше приложение сможет использовать личные учетные записи, например outlook.com, live.com и другие, а также рабочие или учебные учетные записи.

## <a name="prerequisites"></a>Технические условия

- Для создания примера в этом руководстве используется XCode версии 10.x. Скачать XCode можно с [сайта iTunes](https://geo.itunes.apple.com/us/app/xcode/id497799835?mt=12 "XCode Download URL").
- Для управления пакетами требуется диспетчер зависимостей [Carthage](https://github.com/Carthage/Carthage).

## <a name="how-this-guide-works"></a>Принцип работы с руководством

![Работа примера приложения, созданного при работе с этими руководствами](media/active-directory-develop-guidedsetup-ios-introduction/iosintro.svg)

В этом руководстве пример приложения позволяет приложению iOS выполнять запрос к API Microsoft Graph или веб-API, который принимает маркеры от конечной точки платформы удостоверений Майкрософт. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка **авторизации**. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

### <a name="handle-token-acquisition-for-access-to-protected-web-apis"></a>Получение маркера для доступа к защищенным веб-интерфейсам API

После аутентификации пользователя пример приложения получает маркер. Этот маркер используется для выполнения запроса к API Microsoft Graph или веб-API, который защищен конечной точкой платформы удостоверений Майкрософт.

API-интерфейсам, таким как Microsoft Graph, для обращения к определенным ресурсам требуется маркер доступа. Маркеры необходимы для чтения профиля пользователя, доступа к календарю пользователя, отправки электронной почты и т. д. Приложение может запросить маркер доступа, используя MSAL и определяя области API. Затем этот маркер доступа добавляется в заголовок **авторизации** HTTP для каждого вызова к защищенному ресурсу.

MSAL управляет кэшированием и обновлением маркеров доступа, поэтому вашему приложению не нужно этого делать.

## <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|Описание|
|---|---|
|[MSAL.framework](https://github.com/AzureAD/microsoft-authentication-library-for-objc)|Предварительная версия библиотеки проверки подлинности Microsoft для iOS|
