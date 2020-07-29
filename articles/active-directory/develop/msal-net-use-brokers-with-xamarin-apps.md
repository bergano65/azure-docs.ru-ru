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
ms.openlocfilehash: 1a57173311278c5e3e0304aeb12d4d6999379eb5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84692331"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Использование Microsoft Authenticator или Корпоративный портал Intune в приложениях Xamarin

В Android и iOS такие брокеры, как Microsoft Authenticator и Microsoft Intune корпоративный портал для Android, включают:

- **Единый вход (SSO)**. пользователям не нужно входить в каждое приложение.
- **Идентификация устройства**. брокер обращается к сертификату устройства. Этот сертификат создается на устройстве при его присоединении к рабочей области.
- **Проверка идентификации приложения**. когда приложение вызывает брокер, ему передается URL-адрес перенаправления. Брокер проверяет URL-адрес.

Чтобы включить одну из этих функций, используйте `WithBroker()` параметр при вызове `PublicClientApplicationBuilder.CreateApplication` метода. По `.WithBroker()` умолчанию параметр имеет значение true. 

Кроме того, используйте инструкции в следующих разделах, чтобы настроить аутентификацию через посредника для приложений [iOS](#brokered-authentication-for-ios) или приложений [Android](#brokered-authentication-for-android) .

## <a name="brokered-authentication-for-ios"></a>Аутентификация через посредника для iOS

Чтобы разрешить приложению Xamarin. iOS взаимодействовать с приложением [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) , выполните следующие действия.

### <a name="step-1-enable-broker-support"></a>Шаг 1. Включение поддержки брокера
Необходимо включить поддержку брокера для отдельных экземпляров `PublicClientApplication` . По умолчанию поддержка отключена. При создании `PublicClientApplication` с помощью `PublicClientApplicationBuilder` используйте параметр, `WithBroker()` как показано в следующем примере. По `WithBroker()` умолчанию параметр имеет значение true.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Шаг 2. Включение доступа к цепочке ключей

Чтобы включить доступ к цепочке ключей, необходимо иметь группу доступа к цепочке ключей для приложения. Вы можете использовать `WithIosKeychainSecurityGroup()` API для настройки группы доступа к цепочке ключей при создании приложения:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Дополнительные сведения см. в разделе [Включение доступа к цепочке ключей](msal-net-xamarin-ios-considerations.md#enable-keychain-access).

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Шаг 3. обновление AppDelegate для обработки обратного вызова
Когда библиотека проверки подлинности Microsoft для .NET (MSAL.NET) вызывает брокер, брокер выполняет обратный вызов к приложению с помощью `OpenUrl` метода `AppDelegate` класса. Так как MSAL ожидает ответа брокера, приложение должно взаимодействовать для вызова MSAL.NET назад. Чтобы обеспечить взаимодействие, обновите `AppDelegate.cs` файл, чтобы переопределить следующий метод.

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
По-прежнему в `AppDelegate.cs` файле необходимо задать окно объекта. Как правило, для Xamarin iOS не нужно задавать окно объекта. Но для отправки и получения ответов от брокера требуется окно объекта. 

Чтобы настроить окно объекта, выполните следующие действия. 
1. В `AppDelegate.cs` файле задайте новое значение `App.RootViewController` `UIViewController()` . Это назначение гарантирует, что вызов брокера включает `UIViewController` . Если этот параметр назначен неправильно, может появиться следующее сообщение об ошибке:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. В `AcquireTokenInteractive` вызове используйте, `.WithParentActivityOrWindow(App.RootViewController)` а затем передайте ссылку на окно объекта, которое вы будете использовать.

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
MSAL.NET использует URL-адреса для вызова брокера, а затем возвращает ответ брокера в приложение. Чтобы завершить цикл обработки, зарегистрируйте схему URL-адресов для приложения в `Info.plist` файле.

`CFBundleURLSchemes`Имя должно включать в `msauth.` качестве префикса. После префикса используйте `CFBundleURLName` . 

В схеме URL-адресов `BundleId` уникальным образом идентифицирует приложение: `$"msauth.(BundleId)"` . Поэтому `BundleId` , если имеет значение, используется `com.yourcompany.xforms` Схема URL-адресов `msauth.com.yourcompany.xforms` .

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

MSAL использует, `–canOpenURL:` чтобы проверить, установлен ли брокер на устройстве. В iOS 9 Компания Apple заблокировала схемы, которые может запрашивать приложение. 

Добавьте `msauthv2` в `LSApplicationQueriesSchemes` раздел `Info.plist` файла, как показано в следующем примере:

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

Обратите внимание, что URI перенаправления совпадает с `CFBundleURLSchemes` именем, которое вы включили в `Info.plist` файл.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Шаг 8. Убедитесь, что URI перенаправления зарегистрирован в приложении.

URI перенаправления необходимо зарегистрировать на [портале регистрации приложений](https://portal.azure.com) в качестве ДОПУСТИМОго URI перенаправления для приложения. 

Портал регистрации приложений предоставляет новый интерфейс, который поможет вам вычислить URI ответа брокера из идентификатора пакета. 

Чтобы вычислить URI перенаправления:

1. На портале регистрации приложений выберите **Проверка подлинности**  >  **испытать новый интерфейс**.

   ![Ознакомьтесь с новыми возможностями регистрации приложений](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Выберите **Добавить платформу**.

   ![Добавление платформы](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. Если список платформ поддерживается, выберите **iOS**.

   ![Настройка iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Введите идентификатор пакета в качестве запрошенного, а затем щелкните **настроить**.

   ![Введите идентификатор пакета](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Когда вы завершите шаги, URI перенаправления вычислится.

![Копировать URI перенаправления](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Аутентификация через посредника для Android

### <a name="step-1-enable-broker-support"></a>Шаг 1. Включение поддержки брокера

Поддержка брокера включена для каждого Публикклиентаппликатион. Она отключена по умолчанию. Используйте `WithBroker()` параметр (по умолчанию задано значение true) при создании с `IPublicClientApplication` помощью `PublicClientApplicationBuilder` .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Шаг 2. обновление AppDelegate для обработки обратного вызова

Когда MSAL.NET вызывает брокер, брокер, в свою очередь, выполняет обратный вызов к приложению с помощью метода Онактивитиресулт (). Так как MSAL будет ждать ответа от брокера, приложению нужно направить результат в MSAL.NET.
Это можно сделать, отправляя результат в, `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` переопределив метод онактивитиресулт (), как показано ниже.

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Этот метод вызывается каждый раз при запуске приложения брокера и используется в качестве возможности для обработки ответа от брокера и завершения процесса проверки подлинности, запущенного MSAL.NET.

### <a name="step-3-set-an-activity"></a>Шаг 3. Задание действия

Для работы с брокерской проверкой подлинности необходимо настроить действие, чтобы MSAL мог отправлять и получать ответ от брокера.

Для этого необходимо предоставить действие (обычно MainActivity) в `WithParentActivityOrWindow(object parent)` качестве родительского объекта. 

**Пример:**

В вызове запроса маркера:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Шаг 4. Регистрация RedirectUri на портале приложения

MSAL использует URL-адреса для вызова брокера, а затем обратно в приложение. Чтобы завершить этот цикл обработки, необходимо зарегистрировать схему URL-адресов для приложения. Этот URI перенаправления необходимо зарегистрировать на портале регистрации приложений Azure AD в качестве допустимого URI перенаправления для приложения.


URI перенаправления, необходимый для приложения, зависит от сертификата, используемого для подписи APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Последняя часть URI, `hgbUYHVBYUTvuvT&Y6tr554365466=` , — это сигнатура, с которой подписывается APK, в кодировке Base64.
Однако на этапе разработки приложения, использующего Visual Studio, при отладке кода без подписи apk с помощью определенного сертификата Visual Studio подпишет APK для вас в целях отладки, предоставив APK уникальную подпись для компьютера, на котором она построена. Таким же, каждый раз при сборке приложения на другом компьютере необходимо обновить URI перенаправления в коде приложения и регистрацию приложения в портал Azure для проверки подлинности с помощью MSAL. 

При отладке может возникнуть исключение MSAL (или сообщение журнала), сообщающее, что указанный URI перенаправления неверен. **Это исключение также предоставит вам URI перенаправления, который следует использовать** с текущим компьютером, на котором выполняется отладка. Вы можете использовать этот универсальный код ресурса (URI) перенаправления, чтобы продолжить разработку на время.

Когда вы будете готовы завершить работу с кодом, не забудьте обновить URI перенаправления в коде и регистрации приложения в портал Azure, чтобы использовать подпись сертификата, с которым будет подписан APK.

На практике это означает, что необходимо зарегистрировать URI перенаправления для каждого члена команды, а также URI перенаправления для версии APK, подписанной в рабочей среде.

Вы также можете вычислить эту подпись самостоятельно, как это делает MSAL: 

```CSharp
   private string GetRedirectUriForBroker()
   {
      string packageName = Application.Context.PackageName;
      string signatureDigest = this.GetCurrentSignatureForPackage(packageName);
      if (!string.IsNullOrEmpty(signatureDigest))
      {
            return string.Format(CultureInfo.InvariantCulture, "{0}://{1}/{2}", RedirectUriScheme,
               packageName.ToLowerInvariant(), signatureDigest);
      }

      return string.Empty;
   }

   private string GetCurrentSignatureForPackage(string packageName)
   {
            PackageInfo info = Application.Context.PackageManager.GetPackageInfo(packageName,
               PackageInfoFlags.Signatures);
            if (info != null && info.Signatures != null && info.Signatures.Count > 0)
            {
               // First available signature. Applications can be signed with multiple signatures.
               // The order of Signatures is not guaranteed.
               Signature signature = info.Signatures[0];
               MessageDigest md = MessageDigest.GetInstance("SHA");
               md.Update(signature.ToByteArray());
               return Convert.ToBase64String(md.Digest(), Base64FormattingOptions.None);
               // Server side needs to register all other tags. ADAL will
               // send one of them.
            }
   }
```

Вы также можете получить подпись для пакета, используя keytool с помощью следующих команд:

Для Windows: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Для Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Дальнейшие шаги

Сведения о [вопросах использования универсальная платформа Windows с MSAL.NET](msal-net-uwp-considerations.md).
