---
title: Используйте брокеров с Xamarin iOS & Android Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как настроить приложения Xamarin iOS, которые могут использовать Microsoft Authenticator и библиотеку аутентификации Майкрософт для .NET (MSAL.NET). Также узнайте, как перейти из Библиотеки аутентификации Azure AD для .NET (ADAL.NET) в Библиотеку подлинности Майкрософт для .NET (MSAL.NET).
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79262793"
---
# <a name="use-microsoft-authenticator-or-intune-company-portal-on-xamarin-applications"></a>Используйте портал Microsoft Authenticator или Intune Company на приложениях Xamarin

На Android и iOS такие брокеры, как Microsoft Authenticator и Android-специфический портал Microsoft Intune Company, позволяют:

- **Единый вданный (SSO)**: Пользователям не нужно входить в каждое приложение.
- **Идентификация устройства**: Брокер получает доступ к сертификату устройства. Этот сертификат создается на устройстве при присоединении к рабочему месту.
- **Проверка идентификации приложения:** Когда приложение звонит брокеру, оно передает свой url-адрес перенаправления. Брокер проверяет URL.

Чтобы включить одну из этих `WithBroker()` функций, `PublicClientApplicationBuilder.CreateApplication` используйте параметр при вызове метода. Параметр `.WithBroker()` настроен на истину по умолчанию. 

Также используйте инструкции в следующих разделах для настройки посреднической аутентификации для приложений [iOS](#brokered-authentication-for-ios) или [Android.](#brokered-authentication-for-android)

## <a name="brokered-authentication-for-ios"></a>Брокерская аутентификация для iOS

Используйте следующие действия, чтобы включить приложение Xamarin.iOS для связи с приложением [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

### <a name="step-1-enable-broker-support"></a>Шаг 1: Включить поддержку брокера
Вы должны включить брокерскую поддержку для отдельных `PublicClientApplication`экземпляров . Поддержка отключена по умолчанию. При создании `PublicClientApplication` `PublicClientApplicationBuilder`через, `WithBroker()` использовать параметр, как показано в следующем примере. Параметр `WithBroker()` настроен на истину по умолчанию.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

### <a name="step-2-enable-keychain-access"></a>Шаг 2: Включить доступ к брелоку

Чтобы включить доступ к брелоку, необходимо иметь группу доступа к брелоку для приложения. При создании `WithIosKeychainSecurityGroup()` приложения можно использовать API для настройки группы доступа к брелоку:

```csharp
var builder = PublicClientApplicationBuilder
     .Create(ClientId)
      
     .WithIosKeychainSecurityGroup("com.microsoft.adalcache")
     .Build();
```

Для получения дополнительной информации [см.](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Шаг 3: Обновление AppDelegate для обработки обратного вызова
Когда библиотека аутентификации Майкрософт для .NET (MSAL.NET) звонит `OpenUrl` брокеру, `AppDelegate` брокер перезванивает к вашему приложению методом класса. Поскольку MSAL ждет ответа брокера, ваше приложение должно сотрудничать, чтобы перезвонить MSAL.NET. Чтобы обеспечить такое `AppDelegate.cs` сотрудничество, обновите файл, чтобы переопределить следующий метод.

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

Этот метод вызывается каждый раз при начале запуска приложения. Он используется как возможность обработки ответа от брокера и завершить процесс проверки подлинности, который MSAL.NET запущен.

### <a name="step-4-set-uiviewcontroller"></a>Шаг 4: Установить UIViewController()
Все еще `AppDelegate.cs` в файле необходимо установить окно объекта. Обычно для Xamarin iOS не нужно устанавливать окно объекта. Но вам нужно окно объекта для отправки и получения ответов от брокера. 

Для настройки окна объекта: 
1. В `AppDelegate.cs` файле, `App.RootViewController` установленном `UIViewController()`на новый . Это назначение гарантирует, что вызов `UIViewController`брокеру включает в себя. Если эта настройка назначена неправильно, вы можете получить эту ошибку:

      `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`

1. При `AcquireTokenInteractive` вызове `.WithParentActivityOrWindow(App.RootViewController)` используйте, а затем передайте ссылку на окне объекта, которое вы будете использовать.

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

### <a name="step-5-register-a-url-scheme"></a>Шаг 5: Регистрация схемы URL
MSAL.NET использует URL-адреса для того, чтобы вызвать брокера, а затем вернуть ответ брокера на ваше приложение. Чтобы завершить поездку туда и обратно, зарегистрируйте схему URL для приложения в файле. `Info.plist`

Имя `CFBundleURLSchemes` должно `msauth.` быть включено в качестве префикса. Следуйте префиксс с `CFBundleURLName`. 

В схеме `BundleId` URL однозначно идентифицирует `$"msauth.(BundleId)"`приложение: . Так `BundleId` что `com.yourcompany.xforms`если это, `msauth.com.yourcompany.xforms`то схема URL .

> [!NOTE]
> Эта схема URL становится частью перенаправления URI, который однозначно идентифицирует ваше приложение, когда он получает ответ от брокера.

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

### <a name="step-6-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Шаг 6: Добавьте идентификатор брокера в раздел LSApplication-ЗапросыСхемы

MSAL `–canOpenURL:` использует для проверки того, установлен ли брокер на устройстве. В iOS 9 Apple заблокировала схемы, которые приложение может запросить. 

Добавьте `msauthv2` `LSApplicationQueriesSchemes` в раздел `Info.plist` файла, как в следующем примере:

```XML
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
      <string>msauthv3</string>
    </array>
```

### <a name="step-7-register-your-redirect-uri-in-the-application-portal"></a>Шаг 7: Зарегистрируйте свой перенаправление URI на портале приложений

Когда вы используете брокера, ваш перенаправить URI имеет дополнительное требование. Перенаправление URI _должно_ иметь следующий формат:

```csharp
$"msauth.{BundleId}://auth"
```

Ниже приведен пример:

```csharp
public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; 
```

Обратите внимание, что перенаправление URI совпадает с `CFBundleURLSchemes` именем, которое вы включили `Info.plist` в файл.

### <a name="step-8-make-sure-the-redirect-uri-is-registered-with-your-app"></a>Шаг 8: Убедитесь, что перенаправление URI зарегистрировано в вашем приложении

Перенаправление URI должно быть зарегистрировано на [портале регистрации приложений](https://portal.azure.com) в качестве действительного перенаправления URI для вашего приложения. 

Портал регистрации приложений предоставляет новый опыт, чтобы помочь вам вычислить посреднические ответ URI из идентификатора расслоения. 

Для вычисления перенаправления URI:

1. На портале регистрации приложений выберите **Authentication** > **Попробуйте новый опыт.**

   ![Опробовать новый опыт регистрации приложений](media/msal-net-use-brokers-with-xamarin-apps/60799285-2d031b00-a173-11e9-9d28-ac07a7ae894a.png)

1. Выберите **Добавить платформу**.

   ![Добавить платформу](media/msal-net-use-brokers-with-xamarin-apps/60799366-4c01ad00-a173-11e9-934f-f02e26c9429e.png)

1. При поддержке списка платформ выберите **iOS**.

   ![Настройка iOS](media/msal-net-use-brokers-with-xamarin-apps/60799411-60de4080-a173-11e9-9dcc-d39a45826d42.png)

1. Введите идентификатор пакета по запросу, а затем выберите **настройку.**

   ![Введите идентификатор пакета](media/msal-net-use-brokers-with-xamarin-apps/60799477-7eaba580-a173-11e9-9f8b-431f5b09344e.png)

Когда вы закончите с шагами, перенаправление URI вычисляется для вас.

![Копирование перенаправить URI](media/msal-net-use-brokers-with-xamarin-apps/60799538-9e42ce00-a173-11e9-860a-015a1840fd19.png)

## <a name="brokered-authentication-for-android"></a>Брокерская аутентификация для Android

### <a name="step-1-enable-broker-support"></a>Шаг 1: Включить поддержку брокера

Поддержка брокера включена на основе на основе публичного клиентского приложения. Она отключена по умолчанию. Используйте `WithBroker()` параметр (установленный по умолчанию) `PublicClientApplicationBuilder`при создании `IPublicClientApplication` через .

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithRedirectUri(redirectUriOnAndroid) //(see step 4 below)
                .Build();
```

### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Шаг 2: Обновление AppDelegate для обработки обратного вызова

Когда MSAL.NET звонит брокеру, брокер, в свою очередь, перезвонит в ваше приложение методом OnActivityResult() Так как MSAL будет ждать ответа от брокера, ваше приложение должно направить результат на MSAL.NET.
Это может быть достигнуто путем перехивания результата `SetAuthenticationContinuationEventArgs(int requestCode, Result resultCode, Intent data)` на переопределение метода OnActivityResult() в том виде, в каком показано ниже

```CSharp
protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
{
   base.OnActivityResult(requestCode, resultCode, data);
   AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode, resultCode, data);
}
```

Этот метод вызывается каждый раз, когда приложение брокера запущен и используется как возможность обработки ответа от брокера и завершить процесс проверки подлинности, начатый MSAL.NET.

### <a name="step-3-set-an-activity"></a>Шаг 3: Установить действие

Для работы посреднической аутентификации необходимо настроить действие, чтобы MSAL мог отправлять и получать ответ от брокера.

Для этого необходимо предоставить действие (обычно MainActivity) `WithParentActivityOrWindow(object parent)` родительскому объекту. 

**Пример:**

В коллеже Приобретаем токене:

```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow((Activity)context))
             .ExecuteAsync();
```

### <a name="step-4-register-your-redirecturi-in-the-application-portal"></a>Шаг 4: Зарегистрируйте свой RedirectUri на портале приложений

MSAL использует URL-адреса для того, чтобы вызвать брокера, а затем вернуться обратно в ваше приложение. Чтобы завершить эту поездку туда и обратно, необходимо зарегистрировать схему URL для вашего приложения. Этот redirect URI должен быть зарегистрирован на портале регистрации приложений Azure AD в качестве действительного перенаправления URI для вашего приложения.


Перенаправление URI, необходимое для приложения, зависит от сертификата, используемого для подписания APK.

```
Example: msauth://com.microsoft.xforms.testApp/hgbUYHVBYUTvuvT&Y6tr554365466=
```

Последняя часть URI, `hgbUYHVBYUTvuvT&Y6tr554365466=`является подпись, что APK подписан, base64 закодированы.
Однако на этапе разработки приложения с помощью Visual Studio, если вы отлаживаете свой код без подписания APK с конкретным сертификатом, Visual Studio подпишет apk для вас для отладки, что даст APK уникальную подпись для машина, на которую он построен. Таким образом, каждый раз, когда вы создаете приложение на другой машине, вам нужно будет обновлять перенаправление URI в коде приложения и регистрацию приложения на портале Azure, чтобы проверить подлинность с помощью MSAL. 

При отладке может возникнуть исключение MSAL (или сообщение журнала), в котором говорится, что предоставленное URI перенаправление неверно. **Это исключение также предоставит вам перенаправление URI, который вы должны использовать** с текущей машиной, на которую вы отлажаете. Вы можете использовать это перенаправление URI продолжать развиваться в настоящее время.

Как только вы будете готовы доработать свой код, не забудьте обновить перенаправление URI в коде и на регистрацию приложения на портале Azure, чтобы использовать подпись сертификата, с которого вы подписываете APK.

На практике это означает, что для каждого члена команды необходимо зарегистрировать перенаправление URI, а также перенаправить URI для подписанной на производство версии APK.

Вы также можете вычислить эту подпись самостоятельно, подобно тому, как MSAL делает это: 

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

У вас также есть возможность получить подпись для вашего пакета с помощью ключа со следующими командами:

Для Windows: `keytool.exe -list -v -keystore "%LocalAppData%\Xamarin\Mono for Android\debug.keystore" -alias androiddebugkey -storepass android -keypass android`

Для Mac:`keytool -exportcert -alias androiddebugkey -keystore ~/.android/debug.keystore | openssl sha1 -binary | openssl base64`

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [возможностях использования универсальной платформы Windows с MSAL.NET.](msal-net-uwp-considerations.md)
