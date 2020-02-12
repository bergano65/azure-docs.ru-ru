---
title: Использование брокеров с Xamarin iOS & Android | Службы
titleSuffix: Microsoft identity platform
description: Узнайте, как настроить приложения Xamarin iOS, которые могут использовать Microsoft Authenticator и библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET). Также Узнайте, как выполнить миграцию из библиотеки аутентификация Azure AD для .NET (ADAL.NET) в библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.openlocfilehash: 25b8aa9b5e80720e9543dafce7970404a62b7d1f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77132636"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Использование Microsoft Authenticator или Корпоративный портал Intune в приложениях Xamarin

В Android и iOS такие брокеры, как Microsoft Authenticator и Microsoft Intune корпоративный портал для Android, включают:

- **Единый вход (SSO)** . пользователям не нужно входить в каждое приложение.
- **Идентификация устройства**. брокер обращается к сертификату устройства. Этот сертификат создается на устройстве при его присоединении к рабочей области.
- **Проверка идентификации приложения**. когда приложение вызывает брокер, ему передается URL-адрес перенаправления. Брокер проверяет URL-адрес.

Чтобы включить одну из этих функций, используйте параметр `WithBroker()` при вызове метода `PublicClientApplicationBuilder.CreateApplication`. Параметр `.WithBroker()` по умолчанию имеет значение true. 

Кроме того, используйте инструкции в следующих разделах, чтобы настроить аутентификацию через посредника для приложений [iOS](#brokered-authentication-for-ios) или приложений [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Аутентификация через посредника для iOS

Чтобы разрешить приложению Xamarin. iOS взаимодействовать с приложением [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) , выполните следующие действия.

### <a name="step-1-enable-broker-support"></a>Шаг 1. Включение поддержки брокера
Необходимо включить поддержку брокера для отдельных экземпляров `PublicClientApplication`. По умолчанию поддержка отключена. При создании `PublicClientApplication` с помощью `PublicClientApplicationBuilder`используйте параметр `WithBroker()`, как показано в следующем примере. Параметр `WithBroker()` по умолчанию имеет значение true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Шаг 2. Включение доступа к цепочке ключей

Чтобы включить доступ к цепочке ключей, необходимо иметь группу доступа к цепочке ключей для приложения. Вы можете использовать `WithIosKeychainSecurityGroup()` API, чтобы задать группу доступа к цепочке ключей при создании приложения:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Дополнительные сведения см. в разделе [Включение доступа к цепочке ключей](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Шаг 3. обновление AppDelegate для обработки обратного вызова
Когда библиотека проверки подлинности Microsoft для .NET (MSAL.NET) вызывает брокер, брокер выполняет обратный вызов к приложению с помощью метода `OpenUrl` класса `AppDelegate`. Так как MSAL ожидает ответа брокера, приложение должно взаимодействовать для вызова MSAL.NET назад. Чтобы обеспечить взаимодействие, обновите файл `AppDelegate.cs`, чтобы переопределить следующий метод.

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

Этот метод вызывается при каждом запуске приложения. Он используется как возможность обработки ответа от брокера и завершения процесса проверки подлинности, MSAL.NET запущен.

### <a name="step-4-set-uiviewcontroller"></a>Шаг 4. Настройка UIViewController ()
По-прежнему в файле `AppDelegate.cs` необходимо задать окно объекта. Как правило, для Xamarin iOS не нужно задавать окно объекта. Но для отправки и получения ответов от брокера требуется окно объекта. 

Чтобы настроить окно объекта, выполните следующие действия. 
1. В файле `AppDelegate.cs` задайте для `App.RootViewController` новое `UIViewController()`. Это назначение гарантирует, что вызов брокера будет включать `UIViewController`. Если этот параметр назначен неправильно, может появиться следующее сообщение об ошибке:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. В `AcquireTokenInteractive` вызове используйте `.WithParentActivityOrWindow(App.RootViewController)`, а затем передайте ссылку на окно объекта, которое вы будете использовать.

    В `App.cs`:
    
    ```csharp
       public static object RootViewController { get; set; }
    ```
    
    В `AppDelegate.cs`:
    
    ```csharp
       LoadApplication(new App());
       App.RootViewController = new UIViewController();
    ```
    
    В `AcquireToken` вызове:
    
    ```csharp
    result = await app.AcquireTokenInteractive(scopes)
                 .WithParentActivityOrWindow(App.RootViewController)
                 .ExecuteAsync();
    ```

### <a name="step-5-register-a-url-scheme"></a>Шаг 5. Регистрация схемы URL-адреса
MSAL.NET использует URL-адреса для вызова брокера, а затем возвращает ответ брокера в приложение. Чтобы завершить цикл обработки, зарегистрируйте схему URL-адресов для приложения в файле `Info.plist`.

Имя `CFBundleURLSchemes` должно включать `msauth.` в качестве префикса. Используйте префикс с `CFBundleURLName`. 

В схеме URL-адресов `BundleId` однозначно определяет приложение: `$"msauth.(BundleId)"`. Поэтому, если `BundleId` `com.yourcompany.xforms`, схема URL-адреса `msauth.com.yourcompany.xforms`.

> [!NOTE]
> Эта схема URL-адреса преобразуется в URI перенаправления, который уникальным образом идентифицирует ваше приложение при получении ответа от брокера.

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
MSAL использует `–canOpenURL:`, чтобы проверить, установлен ли брокер на устройстве. В iOS 9 Компания Apple заблокировала схемы, которые может запрашивать приложение. 

Добавьте `msauthv2` в раздел `LSApplicationQueriesSchemes` файла `Info.plist`, как показано в следующем примере:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Шаг 7. Регистрация URI перенаправления на портале приложения
При использовании брокера URI перенаправления имеет дополнительное требование. URI перенаправления _должен_ иметь следующий формат:
```csharp
$"msauth.{BundleId}://auth"
```

Ниже приведен пример: 

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```
Обратите внимание, что URI перенаправления соответствует имени `CFBundleURLSchemes`, которое вы включили в файл `Info.plist`.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Шаг 8. Убедитесь, что URI перенаправления зарегистрирован в приложении.

URI перенаправления необходимо зарегистрировать на [портале регистрации приложений](https://portal.azure.com) в качестве ДОПУСТИМОго URI перенаправления для приложения. 

Портал регистрации приложений предоставляет новый интерфейс, который поможет вам вычислить URI ответа брокера из идентификатора пакета. 

Чтобы вычислить URI перенаправления:

1. На портале регистрации приложений выберите **Проверка Подлинности** > **испытать новый интерфейс**.

   ![Ознакомьтесь с новыми возможностями регистрации приложений](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Выберите **Добавить платформу**.

   ![Добавление платформы](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Если список платформ поддерживается, выберите **iOS**.

   ![Настройка iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Введите идентификатор пакета в качестве запрошенного, а затем щелкните **настроить**.

   ![Введите идентификатор пакета](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

По завершении этих действий URI перенаправления вычислится.

![Копировать URI перенаправления](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Аутентификация через посредника для Android

MSAL.NET поддерживает только платформу Xamarin. iOS. Он еще не поддерживает брокеры для платформы Xamarin. Android.

Собственная библиотека MSAL Android уже поддерживает проверку подлинности через посредника. Дополнительные сведения см. [в статье Проверка подлинности через посредника в Android](brokered-auth.md).

## <a name="next-steps"></a>Дальнейшие действия

Сведения о [вопросах использования универсальная платформа Windows с MSAL.NET](msal-net-uwp-considerations.md).
