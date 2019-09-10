---
title: Использование Microsoft Authenticator или Microsoft Intune корпоративного портала в приложениях Xamarin iOS и Android | Службы
description: Узнайте, как выполнять миграцию приложений Xamarin iOS с помощью Microsoft Authenticator из библиотеки аутентификация Azure AD для .NET (ADAL.NET) в библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: e16ad801b3b691d942c2ba0dc723ba72e24cf4c0
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875644"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Использование Microsoft Authenticator или Microsoft Intune корпоративного портала в приложениях Xamarin

В Android и iOS такие брокеры, как Microsoft Authenticator или Microsoft Intune корпоративного портала, включают (только Android):

- Единый вход (SSO). Пользователям не потребуется входить в каждое приложение.
- Идентификация устройства. Путем доступа к сертификату устройства, созданному на устройстве при присоединении к рабочей области.
- Проверка идентификации приложения. Когда приложение вызывает брокер, ему передается URL-адрес перенаправления, и брокер проверяет его.

Чтобы включить одну из этих функций, разработчикам приложений необходимо использовать `WithBroker()` параметр при `PublicClientApplicationBuilder.CreateApplication` вызове метода. `.WithBroker()`по умолчанию имеет значение true. Разработчикам также потребуется выполнить приведенные ниже действия для приложений [iOS](#brokered-authentication-for-ios) или [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Аутентификация через посредника для iOS

Выполните следующие действия, чтобы разрешить приложению Xamarin. iOS взаимодействовать с [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) приложением.

### <a name="step-1-enable-broker-support"></a>Шаг 1. Включить поддержку брокера
Поддержка брокера включена для каждого Публикклиентаппликатион. Она отключена по умолчанию. `WithBroker()` Используйте параметр (по умолчанию задано значение true) при создании публикклиентаппликатион через публикклиентаппликатионбуилдер.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Шаг 2. Обновление AppDelegate для обработки обратного вызова
Когда MSAL.NET вызывает брокер, брокер, в свою очередь, выполняет обратный вызов к приложению через `OpenUrl` метод `AppDelegate` класса. Так как MSAL будет ждать ответа от брокера, ваше приложение должно взаимодействовать для вызова MSAL.NET назад. Чтобы обеспечить взаимодействие, обновите `AppDelegate.cs` файл, чтобы переопределить приведенный ниже метод.

```CSharp
public override bool OpenUrl(UIApplication app, NSUrl url, 
                             string sourceApplication,
                             NSObject annotation)
{
    if (AuthenticationContinuationHelper.IsBrokerResponse(sourceApplication))
    {
      AuthenticationContinuationHelper.SetBrokerContinuationEventArgs(url);
      return true;
    }
    
    else if (!AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(url))
    {               
         return false;                
    }
    
    return true;     
}           
```

Этот метод вызывается каждый раз при запуске приложения и используется в качестве возможности для обработки ответа от брокера и завершения процесса проверки подлинности, инициированного MSAL.NET.

### <a name="step-3-set-a-uiviewcontroller"></a>Шаг 3. Задать UIViewController ()
По- `AppDelegate.cs`прежнему в необходимо задать окно объекта. Как правило, при использовании Xamarin iOS вам не нужно задавать окно объекта, но для отправки и получения ответов из брокера потребуется окно объекта. 

Для этого необходимо выполнить два действия. 
1) В `AppDelegate.cs` задайте`App.RootViewController` новое значение`UIViewController()`. Это назначение обеспечит UIViewController с вызовом брокера. Если оно задано неправильно, может появиться следующее сообщение об ошибке:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) В вызове аккуиретокенинтерактиве используйте `.WithParentActivityOrWindow(App.RootViewController)` и передайте ссылку на окно объекта, которое вы будете использовать.

**Например:**

В `App.cs` добавьте:
```CSharp
   public static object RootViewController { get; set; }
```
В `AppDelegate.cs` добавьте:
```CSharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
В вызове запроса маркера:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-4-register-a-url-scheme"></a>Шаг 4. Регистрация схемы URL-адресов
MSAL.NET использует URL-адреса для вызова брокера, а затем возвращает ответ брокера обратно в приложение. Чтобы завершить цикл обработки, необходимо зарегистрировать схему URL-адресов для приложения в `Info.plist` файле.

Имя должно включать `msauth.` в качестве `CFBundleURLName`префикса, за которым следует. `CFBundleURLSchemes`

`$"msauth.(BundleId)"`

**Например:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Она станет частью RedirectUri, используемой для уникальной идентификации приложения при получении ответа от брокера.

```XML
 <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleTypeRole</key>
        <string>Editor</string>
        <key>CFBundleURLName</key>
        <string>com.yourcompany.xforms</string>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.com.yourcompany.xforms</string>
        </array>
      </dict>
    </array>
```

### <a name="step-5-lsapplicationqueriesschemes"></a>Шаг 5. лсаппликатионкуериессчемес
MSAL использует `–canOpenURL:` для проверки установки брокера на устройстве. В iOS 9 Компания Apple заблокировала, какие схемы приложение может запрашивать. 

**Добавить** **`msauthv2`** в`LSApplicationQueriesSchemes` раздел файла`Info.plist` .

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="step-6-register-your-redirecturi-in-the-application-portal"></a>Шаг 6. Регистрация RedirectUri на портале приложения
При использовании брокера добавляется дополнительное требование к redirectUri. RedirectUri _**должен**_ иметь следующий формат:
```CSharp
$"msauth.{BundleId}://auth"
```
**Например:**
```CSharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
**Обратите внимание, что RedirectUri соответствует `CFBundleURLSchemes` имени, которое вы включили `Info.plist` в файл.**

### <a name="step-7-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Шаг 7. Убедитесь, что URI перенаправления зарегистрирован в приложении.

Этот URI перенаправления должен быть зарегистрирован на портале регистрации приложений (https://portal.azure.com) в качестве допустимого URI перенаправления для приложения. 

На портале есть новый портал регистрации приложений, который поможет вам вычислить URI ответа брокера из идентификатора пакета:

1. В окне Регистрация приложения выберите **Проверка подлинности** и нажмите кнопку **испытать новый интерфейс**.
   ![Ознакомьтесь с новыми возможностями регистрации приложений](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

2. Выберите **Добавить платформу**.
   ![Добавление платформы](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

3. Если список платформ поддерживается, выберите **iOS**.
   ![Настройка iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

4. Введите идентификатор пакета в качестве запрошенного и нажмите кнопку **Register**.
   ![Введите идентификатор пакета](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

5. URI перенаправления вычисляются для вас.
   ![Копировать URI перенаправления](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Аутентификация через посредника для Android

Поддержка брокера недоступна для Android

## <a name="next-steps"></a>Следующие шаги

[Вопросы, связанные с универсальная платформа Windows MSAL.NET](msal-net-uwp-considerations.md)