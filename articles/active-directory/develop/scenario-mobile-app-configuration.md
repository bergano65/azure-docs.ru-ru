---
title: Настройка мобильных приложений, которые вызывают веб-AI AIS Azure
titleSuffix: Microsoft identity platform
description: Узнайте, как создать мобильное приложение, которое вызывает web-апотированные данные (конфигурация кода приложения)
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.openlocfilehash: 06475b53acf6c9161888e29723feab9cdc4336d5
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80882732"
---
# <a name="configure-a-mobile-app-that-calls-web-apis"></a>Настройка мобильного приложения, которое вызывает web-аПО

После создания приложения вы узнаете, как настроить код, используя параметры регистрации приложения. Мобильные приложения представляют некоторые сложности, связанные с вписыванием в их рамки создания.

## <a name="find-msal-support-for-mobile-apps"></a>Найти поддержку MSAL для мобильных приложений

Следующие типы библиотеки аутентификации Майкрософт (MSAL) поддерживают мобильные приложения.

MSAL | Описание
------------ | ----------
![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Используется для разработки портативных приложений. MSAL.NET поддерживает следующие платформы для создания мобильного приложения: Universal Windows Platform (UWP), Xamarin.iOS и Xamarin.Android.
![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Используется для разработки нативных приложений iOS с помощью Objective-C или Swift.
![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Используется для разработки нативных Android-приложений на Java для Android.

## <a name="instantiate-the-application"></a>Мгновенное применение

### <a name="android"></a>Android

Мобильные приложения `PublicClientApplication` используют класс. Вот как мгновенно его:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Мобильные приложения на iOS должны `MSALPublicClientApplication` мгновенно изысковать класс. Чтобы мгновенно увлечь класс, используйте следующий код. 

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

[Дополнительные свойства MSALPublicClientApplicationConfig](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) могут переопределить полномочия по умолчанию, указать перенаправление URI или изменить поведение кэширования токенов MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin или UWP

В этом разделе объясняется, как мгновенно едать приложение для приложений Xamarin.iOS, Xamarin.Android и UWP.

#### <a name="instantiate-the-application"></a>Мгновенное применение

В Xamarin или UWP, самый простой способ мгновенного приложения с помощью следующего кода. В этом `ClientId` коде можно забыть О ГРАФИЧЕСКОм интерфейсе зарегистрированного приложения.

```csharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Дополнительные `With<Parameter>` методы устанавливают родительский uI, переопределяют полномочия по умолчанию, указывают имя клиента и версию для телеметрии, указывают перенаправление URI и указывают для использования фабрику HTTP. Фабрика HTTP может использоваться, например, для обработки прокси-подсудок и для указания телеметрии и лесозаготовок. 

В следующих разделах приводится более подробная информация о мгновенном применении.

##### <a name="specify-the-parent-ui-window-or-activity"></a>Указать родительский ui, окно или действие

На Android необходимо пройти родительскую деятельность, прежде чем делать интерактивную аутентификацию. На iOS, когда вы используете брокера, `ViewController`вам нужно пройти в . Таким же образом на UWP, вы можете пройти в родительское окно. Вы проходите его, когда вы приобретаете токен. Но при создании приложения можно также указать обратный вызов в `UIParent`качестве делегата, который возвращается.

```csharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

На Android мы рекомендуем [`CurrentActivityPlugin`](https://github.com/jamesmontemagno/CurrentActivityPlugin)использовать. Полученный `PublicClientApplication` код строителя выглядит следующим образом:

```csharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="find-more-app-building-parameters"></a>Найти дополнительные параметры создания приложений

Список всех методов, доступных `PublicClientApplicationBuilder`на, см. [Methods list](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)

Для описания всех вариантов, `PublicClientApplicationOptions`которые выставлены в , см [справочная документация](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="tasks-for-xamarin-ios"></a>Задачи для Xamarin iOS

Если вы используете MSAL.NET на Xamarin iOS, выполняй следующие задачи.

* [Переопределение и `OpenUrl` реализация функции в`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
* [Включить группы брелоков](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
* [Включить совместное использование кэша токенов](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
* [Включить доступ к брелоку](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Для получения дополнительной [информации](msal-net-xamarin-ios-considerations.md)см.

## <a name="tasks-for-msal-for-ios-and-macos"></a>Задачи для MSAL для iOS и macOS

Эти задачи необходимы при использовании MSAL для iOS и macOS:

* [Реализация `openURL` обратного вызова](#brokered-authentication-for-msal-for-ios-and-macos)
* [Включить группы доступа к брелоку](howto-v2-keychain-objc.md)
* [Настройка браузеров и веб-представлений](customize-webviews.md)

## <a name="tasks-for-xamarinandroid"></a>Задачи для Xamarin.Android

Если вы используете Xamarin.Android, выполняй следующие задачи:

- [Убедитесь, что контроль возвращается к MSAL после окончания интерактивной части потока проверки подлинности](msal-net-xamarin-android-considerations.md#ensure-that-control-returns-to-msal)
- [Обновление Android манифест](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Используйте встроенный веб-вид (необязательно)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Устранение неполадок по мере необходимости](msal-net-xamarin-android-considerations.md#troubleshoot)

Для получения дополнительной информации, см [Xamarin.Android соображения](msal-net-xamarin-android-considerations.md).

Для рассмотрения о браузерах на Android, см [Xamarin.Android-специфические соображения с MSAL.NET](msal-net-system-browser-android-considerations.md).

#### <a name="tasks-for-uwp"></a>Задачи для UWP

На UWP можно использовать корпоративные сети. В следующих разделах объясняются задачи, которые необходимо выполнить в корпоративном сценарии.

Для получения дополнительной информации см. [UWP-специфические соображения с MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configure-the-application-to-use-the-broker"></a>Настройка приложения для использования брокера

На Android и iOS брокеры позволяют:

- **Единый вписаться (SSO)**: Вы можете использовать SSO для устройств, зарегистрированных в Azure Active Directory (Azure AD). При использовании SSO пользователям не нужно входить в каждое приложение.
- **Идентификация устройств**: Этот параметр позволяет политики условного доступа, связанные с устройствами Azure AD. В процессе проверки подлинности используется сертификат устройства, созданный при присоединении устройства к рабочему месту.
- **Проверка идентификации приложения:** Когда приложение звонит брокеру, оно передает свой url-адрес перенаправления. Затем брокер проверяет его.

### <a name="enable-the-broker-on-xamarin"></a>Включить брокера на Xamarin

Для включения брокера на Xamarin, используйте параметр, `WithBroker()` когда вы называете `PublicClientApplicationBuilder.CreateApplication` метод. По умолчанию, `.WithBroker()` установлен на истину. 

Для обеспечения посреднической аутентификации для Xamarin.iOS выполните последующие действия в [разделе Xamarin.iOS](#enable-brokered-authentication-for-xamarin-ios) в этой статье.

### <a name="enable-the-broker-for-msal-for-android"></a>Включить брокера для MSAL для Android

Для получения информации о включении брокера на Android, см [Брокерская аутентификация на Android](brokered-auth.md). 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Включить брокера для MSAL для iOS и macOS

По умолчанию по умолчанию включена брокерская аутентификация для сценариев Azure AD в MSAL для iOS и macOS. 

В следующих разделах приведены инструкции по настройке приложения для поддержки посреднической аутентификации для MSAL для Xamarin.iOS или MSAL для iOS и macOS. В двух наборах инструкций некоторые шаги отличаются.

### <a name="enable-brokered-authentication-for-xamarin-ios"></a>Включить посредническая аутентификация для Xamarin iOS

Выполните действия в этом разделе, чтобы приложение Xamarin.iOS поговорило с приложением [Microsoft Authenticator.](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458)

#### <a name="step-1-enable-broker-support"></a>Шаг 1: Включить поддержку брокера

Поддержка брокера отключена по умолчанию. Вы включите `PublicClientApplication` его для отдельного класса. Используйте `WithBroker()` параметр при `PublicClientApplication` создании `PublicClientApplicationBuilder`класса через . Параметр `WithBroker()` настроен на истину по умолчанию.

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Шаг 2: Обновление AppDelegate для обработки обратного вызова

Когда MSAL.NET звонит брокеру, брокер затем перезванивает к вашей заявке. Он перезванивает `AppDelegate.OpenUrl` с помощью метода. Поскольку MSAL ждет ответа от брокера, ваше приложение должно сотрудничать, чтобы перезвонить MSAL.NET. Это поведение настраивается путем `AppDelegate.cs` обновления файла для переопределения метода, как показано в следующем коде.

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

Этот метод вызывается при запуске приложения. Это возможность обработать ответ от брокера и завершить процесс проверки подлинности, который MSAL.NET запущен.

#### <a name="step-3-set-a-uiviewcontroller"></a>Шаг 3: Установить UIViewController()

Для Xamarin iOS обычно не требуется устанавливать окно объекта. Но в этом случае вы должны установить его так, что вы можете отправить и получить ответы от брокера. Чтобы установить окно `AppDelegate.cs`объекта, в `ViewController`, вы установите .

Чтобы установить окно объекта, выполните следующие действия:

1. В, `AppDelegate.cs`установить `App.RootViewController` на `UIViewController()`новый . Эта настройка гарантирует, что звонок `UIViewController`брокеру включает в себя. Если он не установлен правильно, вы можете получить эту ошибку:

    `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker."`

1. При `AcquireTokenInteractive` вызове `.WithParentActivityOrWindow(App.RootViewController)`используйте . Передайте ссылку на окно объекта, которое вы будете использовать. Ниже приведен пример:

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
    
#### <a name="step-4-register-a-url-scheme"></a>Шаг 4: Регистрация схемы URL

MSAL.NET использует URL-адреса для того, чтобы вызвать брокера, а затем вернуть ответ брокера обратно в ваше приложение. Чтобы завершить поездку туда и обратно, зарегистрируйте схему URL-адреса приложения в файле. `Info.plist` 

Чтобы зарегистрировать схему URL-адреса приложения, выполните следующие действия:

1. `CFBundleURLSchemes` Префикс `msauth`с . 
1. Добавьте `CFBundleURLName` до конца. Следуйте этому шаблону: 

   `$"msauth.(BundleId)"`

   Здесь `BundleId` однозначно идентифицирует ваше устройство. Например, `BundleId` если `yourcompany.xforms`это, ваша `msauth.com.yourcompany.xforms`схема URL находится под вашим url-адресом.
    
   > [!NOTE]
   > Эта схема URL станет частью перенаправления URI, который однозначно идентифицирует ваше приложение, когда он получает ответ брокера.
    
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
    
#### <a name="step-5-add-to-the-lsapplicationqueriesschemes-section"></a>Шаг 5: Добавить в раздел LSApplicationЗапросыСхемы

MSAL `–canOpenURL:` использует для проверки, если брокер установлен на устройстве. В iOS 9 Apple заблокировала схемы, которые приложение может запросить.

Добавьте `msauthv2` `LSApplicationQueriesSchemes` в раздел `Info.plist` файла, как в следующем примере кода:

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Брокерская аутентификация для MSAL для iOS и macOS

По умолчанию для сценариев Azure AD включена по умолчанию.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Шаг 1: Обновление AppDelegate для обработки обратного вызова

Когда MSAL для iOS и macOS звонит брокеру, брокер `openURL` перезванивает к вашему приложению с помощью метода. Поскольку MSAL ждет ответа от брокера, ваше приложение должно сотрудничать, чтобы перезвонить MSAL. Наспособните эту возможность, `AppDelegate.m` обновив файл для переопределения метода, как показывают следующие примеры кода.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

> [!NOTE]
> Если вы `UISceneDelegate` приняли на iOS 13 или позже, `scene:openURLContexts:` а `UISceneDelegate` затем поместите MSAL обратный вызов в вместо. MSAL `handleMSALResponse:sourceApplication:` должен вызываться только один раз для каждого URL- л.с.
>
> Для получения дополнительной [информации](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)см.

#### <a name="step-2-register-a-url-scheme"></a>Шаг 2: Регистрация схемы URL

MSAL для iOS и macOS использует URL-адреса для того, чтобы вызвать брокера, а затем вернуть ответ брокера на ваше приложение. Чтобы завершить поездку туда и обратно, зарегистрируйте схему URL для приложения в файле. `Info.plist`

Чтобы зарегистрировать схему для приложения: 

1. Префикс пользовательский `msauth`URL-схемой с помощью . 

1. Добавьте идентификатор расслоения в конец схемы. Следуйте этому шаблону: 

   `$"msauth.(BundleId)"`

   Здесь `BundleId` однозначно идентифицирует ваше устройство. Например, `BundleId` если `yourcompany.xforms`это, ваша `msauth.com.yourcompany.xforms`схема URL находится под вашим url-адресом.
  
   > [!NOTE]
   > Эта схема URL станет частью перенаправления URI, который однозначно идентифицирует ваше приложение, когда он получает ответ брокера. Убедитесь, что перенаправление URI `msauth.(BundleId)://auth` в формате зарегистрировано для вашего приложения на [портале Azure.](https://portal.azure.com)
  
   ```XML
   <key>CFBundleURLTypes</key>
   <array>
       <dict>
           <key>CFBundleURLSchemes</key>
           <array>
               <string>msauth.[BUNDLE_ID]</string>
           </array>
       </dict>
   </array>
   ```

#### <a name="step-3-add-lsapplicationqueriesschemes"></a>Шаг 3: Добавить LSApplicationЗапросыСхемы

Добавьте, `LSApplicationQueriesSchemes` чтобы разрешить звонки в приложение Microsoft Authenticator, если оно установлено.

> [!NOTE]
> Схема `msauthv3` необходима при компилировании приложения с помощью Xcode 11 и позже. 

Вот пример того, как `LSApplicationQueriesSchemes`добавить:

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Брокерская аутентификация для Xamarin.Android

MSAL.NET не поддерживает брокеров для Android.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Приобретение токена](scenario-mobile-acquire-token.md)
