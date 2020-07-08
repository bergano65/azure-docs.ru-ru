---
title: Azure AD v2 Windows Desktop начало работы-config
description: Здесь описывается, как классическое приложение для Windows .NET (XAML) может получить маркер доступа и вызвать API, защищенный конечной точкой Azure Active Directory версии 2.
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 01/29/2020
ms.author: ryanwi
ms.custom: aaddev
ms.openlocfilehash: d82f9beecb1b558fca094c31f8c6718c990debd1
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "80885775"
---
# <a name="add-the-applications-registration-information-to-your-app"></a>Добавление в приложение сведений о его регистрации
На этом шаге вам нужно добавить идентификатор приложения в свой проект.

1.  Откройте файл `App.xaml.cs` и замените строку, содержащую `ClientId`, на:

```csharp
private static string ClientId = "[Enter the application Id here]";
```

### <a name="what-is-next"></a>Дальнейшие действия

[!INCLUDE [Test and Validate](../../../../includes/active-directory-develop-guidedsetup-windesktop-test.md)]
