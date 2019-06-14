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
ms.openlocfilehash: a11b291ab89dc9f8159e00e1f2304706f041068e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66120164"
---
## <a name="test-your-code"></a>Тестирование кода

Чтобы запустить проект в Visual Studio, нажмите клавишу **F5**. Отобразится приложение **MainWindow**, как показано ниже:

![Тестирование приложения](./media/active-directory-develop-guidedsetup-windesktop-test/samplescreenshot.png)

При первом запуске приложения и нажатии кнопки **Call Microsoft Graph API** (Вызов API Graph Microsoft) появится запрос на вход. Используйте учетную запись Azure Active Directory (рабочую или учебную) или учетную запись Майкрософт (live.com, outlook.com) для тестирования приложения.

![Вход в приложение](./media/active-directory-develop-guidedsetup-windesktop-test/signinscreenshot.png)

### <a name="provide-consent-for-application-access"></a>Предоставление разрешения на доступ к приложению

Войдя в приложение первый раз, необходимо будет предоставить ему разрешение на использование данных вашего профиля для входа, как показано ниже:

![Предоставление разрешения на доступ к приложению](./media/active-directory-develop-guidedsetup-windesktop-test/consentscreen.png)

### <a name="view-application-results"></a>Просмотр результатов приложения

После входа вы увидите сведения о профиле пользователя, который возвращается вызовом Microsoft Graph API. Результаты отображаются в поле **API Call Results** (Результаты вызова API). Основные сведения о токене, полученном при вызове `AcquireTokenInteractive` или `AcquireTokenSilent`, должны отображаться в поле **Token Info** (Сведения о токене). Результаты содержат следующие свойства:

|Свойство  |Формат  |ОПИСАНИЕ |
|---------|---------|---------|

|**Username** |<span>user@domain.com</span> |Имя пользователя для идентификации пользователя.| |**Token Expires** |Дата, время |Время истечения срока действия токена. MSAL продлевает срок действия, по мере необходимости обновляя токен.|


<!--start-collapse-->
### <a name="more-information-about-scopes-and-delegated-permissions"></a>Дополнительные сведения об областях и делегированных разрешениях

Для чтения профиля пользователя API Microsoft Graph требуется область *user.read*. По умолчанию эта область автоматически добавляется в каждое приложение, зарегистрированное на портале регистрации приложений. Для других API Microsoft Graph, а также для пользовательских API вашего внутреннего сервера, могут потребоваться дополнительные области. Для отображения списка календарей пользователя API Microsoft Graph требуется область *Calendars.Read*.

Чтобы из контекста приложения получить доступ к календарям пользователя, добавьте делегированное разрешение *Calendars.Read* в сведения о регистрации приложения. Затем добавьте область *Calendars.Read* в вызов `acquireTokenSilent`.

>[!NOTE]
>При увеличении количества областей от пользователя могут потребоваться дополнительные согласия.

<!--end-collapse-->

[!INCLUDE [Help and support](./active-directory-develop-help-support-include.md)]
