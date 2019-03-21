---
title: включение файла
description: включение файла
services: active-directory
documentationcenter: dev-center-name
author: danieldobalian
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: include
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/13/2018
ms.author: dadobali
ms.custom: include file
ms.openlocfilehash: c8e5e4f826d7835a1fd38d1db5bfeab19b679b30
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58203757"
---
## <a name="test-your-app"></a>Тестирование приложения

1. Выполните код на своем устройстве или эмуляторе.
2. Попробуйте выполнить вход с использованием учетной записи Azure Active Directory (рабочую или учебную учетную запись) или учетной записи Майкрософт (live.com, outlook.com). 

    ![Тестирование приложения](media/active-directory-develop-guidedsetup-android-test/mainwindow.png)
    <br/><br/>
    ![Введите имя пользователя и пароль](media/active-directory-develop-guidedsetup-android-test/usernameandpassword.png)

### <a name="consent-to-your-app"></a>Предоставление согласия для приложения

При первом входе пользователя в приложение ему потребуется предоставить согласие на разрешения, необходимые приложению, как показано ниже. 

![Предоставление разрешения на доступ к приложению](media/active-directory-develop-guidedsetup-android-test/androidconsent.png)

### <a name="success"></a>Готово!

После входа и предоставления согласия приложение отобразит ответ из API Microsoft Graph. Этот вызов совершается на конечную точку **/me** и возвращает [профиль пользователя](https://developer.microsoft.com/graph/docs/api-reference/v1.0/api/user_get). Список конечных точек Microsoft Graph см. в документации [Обзор Microsoft Graph](https://developer.microsoft.com/graph/docs#common-microsoft-graph-queries).

<!--start-collapse-->
### <a name="scopes-and-delegated-permissions"></a>Области и делегированные разрешения

Для чтения профиля пользователя API Microsoft Graph требуется область *User.Read*. Эта область автоматически появляется в каждом приложении, зарегистрированном на портале регистрации приложений. Для других интерфейсов API потребуются дополнительные области. Например, для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`. 

> [!NOTE]
> Пользователям может потребоваться предоставить дополнительные согласия при изменении регистрации приложения.

<!--end-collapse-->

[!INCLUDE [Help and support](active-directory-develop-help-support-include.md)]
