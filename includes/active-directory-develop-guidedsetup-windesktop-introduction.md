---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/10/2019
ms.author: jmprieur
ms.custom: include file
ms.openlocfilehash: 301abe95b245603e5414eef84ce74cdc8de01d19
ms.sourcegitcommit: 79496a96e8bd064e951004d474f05e26bada6fa0
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67509912"
---
# <a name="call-the-microsoft-graph-api-from-a-windows-desktop-app"></a>Вызов API Microsoft Graph из классического приложения для Windows

В этом руководстве показано, как в классическом приложении .NET для Windows (XAML) используется маркер доступа для вызова API Microsoft Graph. Приложению также доступны другие API, которым требуются маркеры доступа из конечной точки платформы удостоверений Microsoft для разработчиков версии 2.0. Эта платформа ранее именовалась Azure AD.

Когда вы завершите работу с руководством, ваше приложение сможет вызывать защищенный API, использующий личные учетные записи (в том числе outlook.com, live.com и другие). Приложение будет также использовать рабочие и учебные учетные записи из любой компании или организации, использующей Azure Active Directory.  

> [!NOTE]
> Для работы с руководством требуется Visual Studio 2015 с обновлением 3, Visual Studio 2017 или Visual Studio 2019. У вас нет ни одной из этих версий? [Скачайте бесплатно Visual Studio 2019](https://www.visualstudio.com/downloads/).

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве

![Схема работы примера приложения, создаваемого в этом кратком руководстве](./media/active-directory-develop-guidedsetup-windesktop-intro/windesktophowitworks.svg)

Пример приложения, создаваемый с помощью этого руководства, позволяет классическому приложению для Windows выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки платформы удостоверений Майкрософт. В этом сценарии маркер добавляется в запросы HTTP с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

## <a name="handling-token-acquisition-for-accessing-protected-web-apis"></a>Обработка получения маркера для доступа к защищенным веб-интерфейсам API

После того как пользователь пройдет проверку подлинности, пример приложения получит маркер, который может использоваться для запроса API Microsoft Graph или веб-API, защищенного с помощью платформы удостоверений Майкрософт для разработчиков.

Программным интерфейсам, таким как Microsoft Graph, для доступа к определенным ресурсам требуется маркер. Например, маркер необходим для чтения профиля пользователя, доступа к календарю пользователя и отправки электронной почты. Приложение может запросить маркер доступа с помощью MSAL, чтобы получить доступ к этим ресурсам, указав определенные области API. Затем этот маркер доступа добавляется в заголовок авторизации HTTP для каждого вызова к защищенному ресурсу.

MSAL управляет кэшированием и обновлением маркеров доступа, поэтому вашему приложению не нужно этого делать.

## <a name="nuget-packages"></a>Пакеты NuGet

В этом руководстве используются следующие пакеты NuGet:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[Microsoft.Identity.Client](https://www.nuget.org/packages/Microsoft.Identity.Client)|Библиотека аутентификации Майкрософт (MSAL)|
