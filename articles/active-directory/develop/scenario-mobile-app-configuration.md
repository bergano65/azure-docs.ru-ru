---
title: Мобильное приложение, вызывающее веб-API (конфигурация кода) — платформа Microsoft Identity | Службы
description: Узнайте, как создать мобильное приложение, вызывающее веб-API (конфигурация кода приложения).
services: active-directory
documentationcenter: dev-center-name
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/23/2019
ms.author: jmprieur
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0d0550dd92b786ec540bae6ae6da7322d4fb629
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175477"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Мобильное приложение, вызывающее веб-API — конфигурация кода

После создания приложения вы узнаете, как настроить код с помощью параметров регистрации приложения. Мобильные приложения также имеют некоторые сложные особенности, которые должны быть выполнены с учетом платформы, используемой для создания этих приложений.

## <a name="msal-libraries-supporting-mobile-apps"></a>Библиотеки MSAL, поддерживающие мобильные приложения

Библиотеки Майкрософт, поддерживающие мобильные приложения:

  Библиотека MSAL | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Для разработки переносимых приложений. MSAL.NET поддерживаемые платформы для создания мобильного приложения — UWP, Xamarin. iOS и Xamarin. Android.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Разработка собственных приложений iOS с помощью цели-C или SWIFT
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Разработка собственных приложений Android в Java для Android

## <a name="instantiating-the-application"></a>Создание экземпляра приложения

### <a name="android"></a>Android

Мобильные приложения используют класс `PublicClientApplication`. Вот как создать его экземпляр:

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

Мобильным приложениям на iOS необходимо создать экземпляр класса `MSALPublicClientApplication`.

Objective-C.

```objc
NSError *msalError = nil;
     
MSALPublicClientApplicationConfig *config = [[MSALPublicClientApplicationConfig alloc] initWithClientId:@"<your-client-id-here>"];    
MSALPublicClientApplication *application = [[MSALPublicClientApplication alloc] initWithConfiguration:config error:&msalError];
```

Swift:
```swift
let config = MSALPublicClientApplicationConfig(clientId: "<your-client-id-here>")
if let application = try? MSALPublicClientApplication(configuration: config){ /* Use application */}
```

Существуют [Дополнительные свойства мсалпубликклиентаппликатионконфиг](https://azuread.github.io/microsoft-authentication-library-for-objc/Classes/MSALPublicClientApplicationConfig.html#/Configuration%20options) , которые могут переопределить центр по умолчанию, указать универсальный код ресурса (URI) перенаправления или изменить поведение кэширования маркера MSAL. 

### <a name="xamarin-or-uwp"></a>Xamarin или UWP

В следующем абзаце объясняется, как создать приложение для приложений Xamarin. iOS, Xamarin. Android и UWP.

#### <a name="instantiating-the-application"></a>Создание экземпляра приложения

В Xamarin или UWP самый простой способ создания экземпляра приложения выглядит следующим образом, где `ClientId` является идентификатором GUID зарегистрированного приложения.

```CSharp
var app = PublicClientApplicationBuilder.Create(clientId)
                                        .Build();
```

Существуют дополнительные методы с*параметрами* , которые задают родительский элемент пользовательского интерфейса, переопределяют центр по умолчанию, указывают имя и версию клиента (для телеметрии), укажите URI перенаправления, укажите используемую фабрику HTTP (например, для обработки прокси-серверов). данные телеметрии и ведение журнала). Это раздел следующих абзацев.

##### <a name="specifying-the-parent-uiwindowactivity"></a>Указание родительского пользовательского интерфейса/окна или действия

В Android перед выполнением интерактивной проверки подлинности необходимо передать родительское действие. В iOS при использовании брокера необходимо передать ViewController. Точно так же, как и в UWP, может потребоваться передать родительское окно. Это возможно при получении маркера, но можно также указать обратный вызов во время создания приложения делегат, возвращающий Уипарент.

```CSharp
IPublicClientApplication application = PublicClientApplicationBuilder.Create(clientId)
  .ParentActivityOrWindowFunc(() => parentUi)
  .Build();
```

В Android рекомендуется использовать `CurrentActivityPlugin` [здесь](https://github.com/jamesmontemagno/CurrentActivityPlugin).  После этого код `PublicClientApplication` построителя будет выглядеть следующим образом:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Дополнительные параметры здания приложений

- Список всех модификаторов, доступных в `PublicClientApplicationBuilder`, см. в справочной документации [публикклиентаппликатионбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Описание всех параметров, предоставляемых в `PublicClientApplicationOptions` см. в справочной документации по [публикклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions).

## <a name="xamarin-ios-specific-considerations"></a>Особенности, связанные с Xamarin iOS

В Xamarin iOS существует несколько аспектов, которые необходимо учитывать при использовании MSAL.NET:

1. [Переопределите и реализуйте функцию `OpenUrl` в `AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Включение групп ключей цепочки](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Включить общий доступ к кэшу маркеров](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Включение доступа к цепочке ключей](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Подробные сведения приведены в разделе [рекомендации по Xamarin iOS](msal-net-xamarin-ios-considerations.md) .

## <a name="msal-for-ios-and-macos-specific-considerations"></a>Рекомендации по MSAL для iOS и macOS

Аналогичные рекомендации применяются при использовании MSAL для iOS и macOS.

1. [Реализация обратного вызова `openURL`](#brokered-authentication-for-msal-for-ios-and-macos)
2. [Включить группы доступа к цепочке ключей](howto-v2-keychain-objc.md)
3. [Настройка браузеров и представлений](customize-webviews.md)

## <a name="xamarin-android-specific-considerations"></a>Особенности, связанные с Xamarin Android

Ниже приведены особенности, относящиеся к Xamarin Android.

- [Обеспечение возврата управления в MSAL после завершения интерактивной части потока проверки подлинности](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Обновление манифеста Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Использовать внедренное веб-представление (необязательно)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Устранение неполадок](msal-net-xamarin-android-considerations.md#troubleshooting)

Подробные сведения приведены в разделе [рекомендации по Xamarin для Android](msal-net-xamarin-android-considerations.md)

Наконец, есть некоторые особенности, которые необходимо учитывать в браузерах на Android. Они объясняются в [вопросах, касающихся Xamarin для Android, с MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Особенности, связанные с UWP

В UWP можно использовать корпоративные сети. Дополнительные сведения об использовании библиотеки MSAL с UWP см. в разделе [вопросы, связанные с универсальная платформа Windows MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Настройка приложения для использования брокера

### <a name="why-use-brokers-in-ios-and-android-applications"></a>Зачем использовать брокеры в приложениях iOS и Android?

В Android и iOS, брокеры включают:

- Единый вход (SSO), когда устройство зарегистрировано в AAD. Пользователям не потребуется входить в каждое приложение.
- Идентификация устройства. Включает политики условного доступа, связанные с устройствами Azure AD, путем доступа к сертификату устройства, созданному на устройстве при присоединении к рабочей области.
- Проверка идентификации приложения. Когда приложение вызывает брокер, ему передается URL-адрес перенаправления, и брокер проверяет его.

### <a name="enable-the-broker-on-xamarin"></a>Включение брокера в Xamarin

Чтобы включить одну из этих функций, используйте параметр `WithBroker()` при вызове метода `PublicClientApplicationBuilder.CreateApplication`. по умолчанию `.WithBroker()` имеет значение true. Выполните следующие действия для [Xamarin. iOS](#brokered-authentication-for-xamarinios).

### <a name="enable-the-broker-for-msal-for-android"></a>Включение брокера для MSAL для Android

Сведения о включении брокера в Android см. [в разделе Проверка подлинности через посредник в Android](brokered-auth.md) . 

### <a name="enable-the-broker-for-msal-for-ios-and-macos"></a>Включение брокера для MSAL для iOS и macOS

Проверка подлинности через посредника включена по умолчанию для сценариев AAD в MSAL для iOS и macOS. Выполните следующие действия, чтобы настроить приложение для поддержки проверки подлинности через посредника для [MSAL для iOS и macOS](#brokered-authentication-for-msal-for-ios-and-macos). Обратите внимание, что некоторые шаги в [MSAL для Xamarin. iOS](#brokered-authentication-for-xamarinios) и [MSAL для iOS и macOS](#brokered-authentication-for-msal-for-ios-and-macos)различны.

### <a name="brokered-authentication-for-xamarinios"></a>Проверка подлинности через посредника для Xamarin. iOS

Выполните следующие действия, чтобы разрешить приложению Xamarin. iOS взаимодействовать с [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) приложением.

#### <a name="step-1-enable-broker-support"></a>Шаг 1. Включение поддержки брокера

Поддержка брокера включена для каждого`PublicClientApplication`. Она отключена по умолчанию. Необходимо использовать параметр `WithBroker()` (по умолчанию задано значение true) при создании `PublicClientApplication` с помощью `PublicClientApplicationBuilder`.

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Шаг 2. обновление AppDelegate для обработки обратного вызова

Когда MSAL.NET вызывает брокер, брокер, в свою очередь, выполняет обратный вызов к приложению с помощью метода `AppDelegate.OpenUrl`. Так как MSAL будет ждать ответа от брокера, ваше приложение должно взаимодействовать для вызова MSAL.NET назад. Это можно сделать, обновив файл `AppDelegate.cs`, чтобы переопределить приведенный ниже метод.

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

Этот метод вызывается каждый раз при запуске приложения и используется как возможность обработки ответа от брокера и завершения процесса проверки подлинности, инициированного MSAL.NET.

#### <a name="step-3-set-a-uiviewcontroller"></a>Шаг 3. Задание UIViewController ()

В случае Xamarin iOS вам обычно не нужно задавать окно объекта, но в данном случае для отправки и получения ответов от брокера. По-прежнему в `AppDelegate.cs`задайте ViewController.

Чтобы задать окно объекта, выполните следующие действия.

1) В `AppDelegate.cs`задайте для `App.RootViewController` новое `UIViewController()`. Это позволит убедиться в наличии `UIViewController` с вызовом брокера. Если оно задано неправильно, может появиться следующее сообщение об ошибке: `"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) В вызове Аккуиретокенинтерактиве используйте `.WithParentActivityOrWindow(App.RootViewController)` и передайте ссылку на окно объекта, которое вы будете использовать.

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

#### <a name="step-4-register-a-url-scheme"></a>Шаг 4. Регистрация схемы URL-адреса

MSAL.NET использует URL-адреса для вызова брокера, а затем возвращает ответ брокера обратно в приложение. Чтобы завершить цикл обработки, необходимо зарегистрировать схему URL-адресов для приложения в файле `Info.plist`.

Добавьте в `CFBundleURLSchemes` префикс `msauth`. Затем добавьте `CFBundleURLName` в конец.

`$"msauth.(BundleId)"`

**Например:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Эта схема URL-адресов станет частью RedirectUri, используемой для уникальной идентификации приложения при получении ответа от брокера.

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

#### <a name="step-5-lsapplicationqueriesschemes"></a>Шаг 5. Лсаппликатионкуериессчемес

MSAL использует `–canOpenURL:`, чтобы проверить, установлен ли брокер на устройстве. В iOS 9 Компания Apple заблокировала, какие схемы приложение может запрашивать.

**Добавьте** **`msauthv2`** в раздел `LSApplicationQueriesSchemes` файла `Info.plist`.

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-msal-for-ios-and-macos"></a>Аутентификация через посредника для MSAL для iOS и macOS

Проверка подлинности по брокеру включена по умолчанию для сценариев AAD.

#### <a name="step-1-update-appdelegate-to-handle-the-callback"></a>Шаг 1. обновление AppDelegate для обработки обратного вызова

Когда MSAL для iOS и macOS вызывает брокер, брокер, в свою очередь, выполняет обратный вызов к приложению с помощью метода `openURL`. Так как MSAL будет ждать ответа от брокера, ваше приложение должно взаимодействовать для вызова MSAL назад. Это можно сделать, обновив файл `AppDelegate.m`, чтобы переопределить приведенный ниже метод.

Objective-C.

```objc
- (BOOL)application:(UIApplication *)app
            openURL:(NSURL *)url
            options:(NSDictionary<UIApplicationOpenURLOptionsKey,id> *)options
{
    return [MSALPublicClientApplication handleMSALResponse:url 
                                         sourceApplication:options[UIApplicationOpenURLOptionsSourceApplicationKey]];
}
```

Swift:

```swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {
        
        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }
        
        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
```

Обратите внимание, что, если вы приняли Уисценеделегате в iOS 13 +, необходимо поместить обратный вызов MSAL в `scene:openURLContexts:` Уисценеделегате (см. [документацию Apple](https://developer.apple.com/documentation/uikit/uiscenedelegate/3238059-scene?language=objc)). `handleMSALResponse:sourceApplication:` MSAL необходимо вызывать только один раз для каждого URL-адреса.

#### <a name="step-2-register-a-url-scheme"></a>Шаг 2. Регистрация схемы URL-адреса

MSAL для iOS и macOS использует URL-адреса для вызова брокера, а затем возвращает ответ брокера обратно в приложение. Чтобы завершить цикл обработки, необходимо зарегистрировать схему URL-адресов для приложения в файле `Info.plist`.

Добавьте в префикс настраиваемой схемы URL-адреса `msauth`. Затем добавьте **идентификатор пакета** в конец.

`msauth.(BundleId)`

**Например:** 
`msauth.com.yourcompany.xforms`

> [!NOTE]
> Эта схема URL-адресов станет частью RedirectUri, используемой для уникальной идентификации приложения при получении ответа от брокера. Убедитесь, что RedirectUri в формате `msauth.(BundleId)://auth` зарегистрировано для вашего приложения на [портале Azure](https://portal.azure.com).

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

#### <a name="step-3-lsapplicationqueriesschemes"></a>Шаг 3. Лсаппликатионкуериессчемес

**Добавьте `LSApplicationQueriesSchemes`** , чтобы разрешить вызов Microsoft Authenticator, если он установлен.
Обратите внимание, что при компиляции приложения с помощью Xcode 11 и более поздних версий требуется схема "msauthv3". 

```XML 
<key>LSApplicationQueriesSchemes</key>
<array>
  <string>msauthv2</string>
  <string>msauthv3</string>
</array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Аутентификация через посредника для Xamarin. Android

MSAL.NET пока не поддерживает брокеры для Android.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Получение маркера](scenario-mobile-acquire-token.md)
