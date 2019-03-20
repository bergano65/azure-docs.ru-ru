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
ms.date: 03/20/2019
ms.author: nacanuma
ms.custom: include file
ms.openlocfilehash: a6fcbc0e8adac75f17d7379ff512ba650d0bb118
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203349"
---
# <a name="call-the-microsoft-graph-api-from-a-javascript-single-page-application-spa"></a>Вызов API Microsoft Graph из одностраничного приложения JavaScript

В этом руководстве показано, как одностраничное приложение JavaScript может выполнять вход с помощью личных, рабочих или учебных учетных записей, получать маркер доступа и вызывать API Microsoft Graph или другие API, требующие маркеры доступа, из конечной точки Azure Active Directory версии 2.0.

## <a name="how-the-sample-app-generated-by-this-guide-works"></a>Как работает пример приложения, созданный в этом руководстве

![Показано, как в примере приложения создаются, это работает, учебники](media/active-directory-develop-guidedsetup-javascriptspa-introduction/javascriptspa-intro-updated.png)

<!--start-collapse-->
### <a name="more-information"></a>Дополнительные сведения

Пример приложения, созданный в этом руководстве, позволяет одностраничному приложению JavaScript выполнять запрос к API Microsoft Graph или веб-API, принимающему маркеры от конечной точки Azure Active Directory версии 2.0. В этом сценарии после выполнения входа в систему маркер доступа запрашивается и добавляется в HTTP-запросы с помощью заголовка авторизации. Получение маркера и его обновление выполняет библиотека проверки подлинности Майкрософт (MSAL).

<!--end-collapse-->

<!--start-collapse-->
### <a name="libraries"></a>Библиотеки

В этом руководстве используется следующая библиотека:

|Библиотека|ОПИСАНИЕ|
|---|---|
|[msal.js](https://github.com/AzureAD/microsoft-authentication-library-for-js)|Библиотека аутентификации Майкрософт для JavaScript (предварительная версия)|

> [!NOTE]
> В качестве целевого объекта в библиотеке *msal.js* задана *конечная точка Azure Active Directory версии 2.0*, что позволяет выполнять вход и запрашивать маркеры, используя личные, рабочие и учебные учетные записи. В отношении *конечной точки Azure Active Directory версии 2.0* применяется [ряд ограничений](../articles/active-directory/develop/active-directory-v2-limitations.md).
> Чтобы понять различия между конечными точками версий 1.0 и 2.0, ознакомьтесь с [руководством по сравнению](../articles/active-directory/develop/azure-ad-endpoint-comparison.md).

<!--end-collapse-->
