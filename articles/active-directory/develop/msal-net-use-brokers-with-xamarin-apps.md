---
title: Использование брокеров с Xamarin, iOS, & Android | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как перенести приложения Xamarin iOS, которые могут использовать Microsoft Authenticator из библиотеки аутентификация Azure AD для .NET (ADAL.NET) в библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 09/08/2019
ms.author: jmprieur
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 49198909da103debd77fcf0d630e0fa16c1e4448
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75424222"
---
# <a name="use-microsoft-authenticator-or-microsoft-intune-company-portal-on-xamarin-applications"></a>Использование Microsoft Authenticator или Microsoft Intune корпоративный портал в приложениях Xamarin

В Android и iOS такие брокеры, как Microsoft Authenticator или Microsoft Intune, корпоративный портал включить (только Android):

- Единый вход (SSO). Пользователям не потребуется входить в каждое приложение.
- Идентификация устройства. Брокер обращается к сертификату устройства, созданному на устройстве при присоединении к рабочей области.
- Проверка идентификации приложения. Когда приложение вызывает брокер, ему передается URL-адрес перенаправления, и брокер проверяет его.

Чтобы включить одну из этих функций, разработчикам приложений необходимо использовать параметр `WithBroker()` при вызове метода `PublicClientApplicationBuilder.CreateApplication`. по умолчанию `.WithBroker()` имеет значение true. Разработчикам также необходимо выполнить действия, описанные в этой статье для приложений [iOS](#brokered-authentication-for-ios) или [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Аутентификация через посредника для iOS

Выполните следующие действия, чтобы разрешить приложению Xamarin. iOS взаимодействовать с [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) приложением.

### <a name="step-1-enable-broker-support"></a>Шаг 1. Включение поддержки брокера
Поддержка брокера включена для каждого Публикклиентаппликатион. Она отключена по умолчанию. Используйте параметр `WithBroker()` (по умолчанию задано значение true) при создании Публикклиентаппликатион через Публикклиентаппликатионбуилдер.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Шаг 2. Включение доступа к цепочке ключей

Чтобы включить доступ к цепочке ключей, приложение должно иметь группу доступа к цепочке ключей. Вы можете использовать `WithIosKeychainSecurityGroup()` API, чтобы задать группу доступа к цепочке ключей при создании приложения:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Дополнительные сведения см. в разделе [Включение доступа к цепочке ключей](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Шаг 3. обновление AppDelegate для обработки обратного вызова
Когда библиотека проверки подлинности Microsoft для .NET (MSAL.NET) вызывает брокер, брокер, в свою очередь, осуществляет обратный вызов приложения с помощью метода `OpenUrl` класса `AppDelegate`. Так как MSAL ожидает ответа от брокера, приложение должно взаимодействовать для вызова MSAL.NET назад. Чтобы обеспечить взаимодействие, обновите файл `AppDelegate.cs`, чтобы переопределить следующий метод.

```csharp
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

Этот метод вызывается при каждом запуске приложения. Он используется как возможность обработки ответа от брокера и завершения процесса проверки подлинности, инициированного MSAL.NET.

### <a name="step-4-set-a-uiviewcontroller"></a>Шаг 4. Задание UIViewController ()
По-прежнему в `AppDelegate.cs`необходимо задать окно объекта. Как правило, при использовании Xamarin iOS не нужно задавать окно объекта. Для отправки и получения ответов от брокера требуется окно объекта. 

Для этого выполните два действия. 
1. В `AppDelegate.cs`задайте для `App.RootViewController` новое `UIViewController()`. Это назначение гарантирует, что UIViewController с вызовом брокера. Если оно задано неправильно, может появиться следующее сообщение об ошибке: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. В вызове Аккуиретокенинтерактиве используйте `.WithParentActivityOrWindow(App.RootViewController)` и передайте ссылку на окно объекта, которое вы будете использовать.

**Например:**

В `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
В `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
В вызове запроса маркера:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

### <a name="step-5-register-a-url-scheme"></a>Шаг 5. Регистрация схемы URL-адреса
MSAL.NET использует URL-адреса для вызова брокера, а затем возвращает ответ брокера обратно в приложение. Чтобы завершить цикл обработки, зарегистрируйте схему URL-адресов для приложения в файле `Info.plist`.

Имя `CFBundleURLSchemes` должно включать `msauth.` в качестве префикса, за которым следует `CFBundleURLName`.

`$"msauth.(BundleId)"`

**Например:**

`msauth.com.yourcompany.xforms`

> [!NOTE]
> Эта схема URL-адреса станет частью URI перенаправления, который используется для уникальной идентификации приложения при получении ответа от брокера.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Шаг 6. Добавление идентификатора брокера в раздел Лсаппликатионкуериессчемес
MSAL использует `–canOpenURL:`, чтобы проверить, установлен ли брокер на устройстве. В iOS 9 Компания Apple заблокировала, какие схемы приложение может запрашивать. 

Добавьте `msauthv2` в раздел `LSApplicationQueriesSchemes` файла `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Шаг 7. Регистрация URI перенаправления на портале приложения
При использовании брокера в URI перенаправления добавляется дополнительное требование. URI перенаправления _должен_ иметь следующий формат:
```csharp
$"msauth.{BundleId}://auth"
```
**Например:**
```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Обратите внимание, что URI перенаправления соответствует имени `CFBundleURLSchemes`, которое вы включили в файл `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Шаг 8. Убедитесь, что URI перенаправления зарегистрирован в приложении.

Этот URI перенаправления должен быть зарегистрирован на портале регистрации приложений (https://portal.azure.com) в качестве допустимого URI перенаправления для приложения. 

На портале есть новый портал регистрации приложений, который поможет вам вычислить URI ответа брокера из идентификатора пакета.

1. В окне Регистрация приложения выберите **Проверка подлинности** и нажмите кнопку **испытать новый интерфейс**.

   ![Ознакомьтесь с новыми возможностями регистрации приложений](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Выберите **Добавить платформу**.

   ![Добавление платформы](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Если список платформ поддерживается, выберите **iOS**.

   ![Настройка iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Введите идентификатор пакета в качестве запрошенного, а затем щелкните **настроить**.

   ![Введите идентификатор пакета](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

1. URI перенаправления вычисляются для вас.

   ![Копировать URI перенаправления](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Аутентификация через посредника для Android

В данный момент MSAL.NET поддерживает только платформу Xamarin. iOS. Он еще не поддерживает брокеры для платформы Xamarin. Android.

Собственная библиотека MSAL Android уже поддерживает ее. Дополнительные сведения см. [в разделе Проверка подлинности через посредника в Android](brokered-auth.md) .

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о [конкретных универсальная платформа Windowsх в MSAL.NET](msal-net-uwp-considerations.md).
