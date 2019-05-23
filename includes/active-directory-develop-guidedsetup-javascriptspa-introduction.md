---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: navyasric
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/11/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: 8c2dc41fde9387da291b6e4a6c8a6220ae62b514
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66121810"
---
# <a name="sign-in-users-and-call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Вход пользователей и вызов API Microsoft Graph из одностраничного приложения JavaScript (SPA)

В этом руководстве показано, как одностраничное приложение JavaScript может выполнять вход с помощью личных, рабочих или учебных учетных записей, получать маркер доступа и вызывать API Microsoft Graph или другие API, требующие маркеры доступа от конечной точки платформы удостоверений Майкрософт.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве

![Работа примера приложения, созданного при работе с этими руководствами.](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro.svg)

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный в этом руководстве, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки платформы удостоверений Майкрософт. В этом сценарии после выполнения входа в систему маркер доступа запрашивается и добавляется в HTTP-запросы с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Библиотека аутентификации Майкрософт для JavaScript (предварительная версия)|

> [!NOTE]
> В качестве целевого объекта в библиотеке *msal.js* задана *конечная точка платформы удостоверений Майкрософт*, что позволяет выполнять вход и запрашивать маркеры, используя личные, рабочие и учебные учетные записи. *Конечная точка платформы удостоверений Майкрософт* имеет [некоторые ограничения](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Чтобы понять различия между конечными точками версий 1.0 и 2.0, ознакомьтесь с [руководством по сравнению](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
