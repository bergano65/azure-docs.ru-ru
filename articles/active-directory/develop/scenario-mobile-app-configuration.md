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
ms.openlocfilehash: 3bdf9210eb88b2057cf861b208f19d3e6f562e9a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414854"
---
# <a name="mobile-app-that-calls-web-apis---code-configuration"></a>Мобильное приложение, вызывающее веб-API — конфигурация кода

После создания приложения вы узнаете, как настроить код из параметров приложения, которые вы получили при регистрации приложения. Мобильные приложения также имеют некоторые сложные особенности, которые должны быть выполнены с учетом платформы, используемой для создания этих приложений.

## <a name="msal-libraries-supporting-mobile-apps"></a>Библиотеки MSAL, поддерживающие мобильные приложения

Библиотеки Майкрософт, поддерживающие мобильные приложения:

  Библиотека MSAL | Описание
  ------------ | ----------
  ![MSAL.NET](media/sample-v2-code/logo_NET.png) <br/> MSAL.NET  | Для разработки переносимых приложений. MSAL.NET поддерживаемые платформы для создания мобильного приложения — UWP, Xamarin. iOS и Xamarin. Android.
  ![MSAL.iOS](media/sample-v2-code/logo_iOS.png) <br/> MSAL.iOS | Разработка собственных приложений iOS с помощью цели C или SWIFT
  ![MSAL.Android](media/sample-v2-code/logo_android.png) <br/> MSAL.Android | Разработка собственных приложений Android в Java для Android

## <a name="configuring-the-application"></a>Настройка приложения

Мобильные приложения используют `PublicClientApplication` класс MSAL. Вот как создать его экземпляр:

### <a name="android"></a>Android

```Java
PublicClientApplication sampleApp = new PublicClientApplication(
                    this.getApplicationContext(),
                    R.raw.auth_config);
```

### <a name="ios"></a>iOS

```swift
// Initialize the app.
guard let authorityURL = URL(string: kAuthority) else {
    self.loggingText.text = "Unable to create authority URL"
    return
}
let authority = try MSALAADAuthority(url: authorityURL)
let msalConfiguration = MSALPublicClientApplicationConfig(clientId: kClientID, redirectUri: nil, authority: authority)
self.applicationContext = try MSALPublicClientApplication(configuration: msalConfiguration)
}
```

### <a name="xamarin-or-uwp"></a>Xamarin или UWP

В следующем абзаце объясняется, как настроить код приложения для приложений Xamarin. iOS, Xamarin. Android и UWP. Первым шагом является создание экземпляра приложения. Необязательный шаг — настройка брокера.

#### <a name="instantiating-the-application"></a>Создание экземпляра приложения

В Xamarin или UWP самый простой способ создания экземпляра приложения выглядит следующим `ClientId` образом: идентификатор GUID приложения зарегистрирован.

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

В Android мы рекомендуем вам `CurrentActivityPlugin` [здесь](https://github.com/jamesmontemagno/CurrentActivityPlugin).  Затем код `PublicClientApplication` построителя будет выглядеть следующим образом:

```CSharp
// Requires MSAL.NET 4.2 or above
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

##### <a name="more-app-building-parameters"></a>Дополнительные параметры здания приложений

- Список всех модификаторов, доступных в `PublicClientApplicationBuilder`, см. в справочной документации [публикклиентаппликатионбуилдер](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationbuilder#methods)
- Описание всех параметров, предоставляемых в `PublicClientApplicationOptions` разделе см. [публикклиентаппликатионоптионс](https://docs.microsoft.com/dotnet/api/microsoft.identity.client.publicclientapplicationoptions), в справочной документации.

#### <a name="xamarin-ios-specific-considerations"></a>Особенности, связанные с Xamarin iOS

В Xamarin iOS существует несколько аспектов, которые необходимо учитывать при использовании MSAL.NET:

1. [Переопределите и реализуйте `OpenUrl` функцию в`AppDelegate`](msal-net-xamarin-ios-considerations.md#implement-openurl)
1. [Включение групп ключей цепочки](msal-net-xamarin-ios-considerations.md#enable-keychain-access)
1. [Включить общий доступ к кэшу маркеров](msal-net-xamarin-ios-considerations.md#enable-token-cache-sharing-across-ios-applications)
1. [Включение доступа к цепочке ключей](msal-net-xamarin-ios-considerations.md#enable-keychain-access)

Подробные сведения приведены в разделе [рекомендации по Xamarin iOS](msal-net-xamarin-ios-considerations.md) .

#### <a name="other-xamarin-android-specific-considerations"></a>Другие аспекты, связанные с Xamarin Android

Ниже приведены особенности, относящиеся к Xamarin Android.

- [Обеспечение возврата управления в MSAL после завершения интерактивной части потока проверки подлинности](msal-net-xamarin-android-considerations.md#ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends)
- [Обновление манифеста Android](msal-net-xamarin-android-considerations.md#update-the-android-manifest)
- [Использовать внедренное веб-представление (необязательно)](msal-net-xamarin-android-considerations.md#use-the-embedded-web-view-optional)
- [Устранение неполадок](msal-net-xamarin-android-considerations.md#troubleshooting)

Подробные сведения приведены в разделе [рекомендации по Xamarin для Android](msal-net-xamarin-android-considerations.md)

Наконец, есть некоторые особенности, которые необходимо учитывать в браузерах на Android. Они объясняются в [вопросах, касающихся Xamarin для Android, с MSAL.NET](msal-net-system-browser-android-considerations.md)

#### <a name="uwp-specific-considerations"></a>Особенности, связанные с UWP

В UWP можно использовать корпоративные сети. Сведения о характерах для UWP см. в разделе [рекомендации по универсальная платформа Windows MSAL.NET](msal-net-uwp-considerations.md).

## <a name="configuring-the-application-to-use-the-broker"></a>Настройка приложения для использования брокера

### <a name="why-use-brokers-on-xamarinios-and-xamarinandroid-applications"></a>Зачем использовать брокеры в приложениях Xamarin. iOS и Xamarin. Android?

В Android и iOS, брокеры включают:

- Единый вход (SSO). Пользователям не потребуется входить в каждое приложение.
- Идентификация устройства. Включает политики условного доступа, связанные с устройствами Azure AD, путем доступа к сертификату устройства, созданному на устройстве при присоединении к рабочей области.
- Проверка идентификации приложения. Когда приложение вызывает брокер, ему передается URL-адрес перенаправления, и брокер проверяет его.

### <a name="enable-the-brokers-on-xamarin"></a>Включение брокеров в Xamarin

Чтобы включить одну из этих функций, используйте `WithBroker()` параметр при `PublicClientApplicationBuilder.CreateApplication` вызове метода. `.WithBroker()`по умолчанию имеет значение true. Выполните следующие действия для [iOS](#brokered-authentication-for-xamarinios).

### <a name="brokered-authentication-for-xamarinios"></a>Проверка подлинности через посредника для Xamarin. iOS

Выполните следующие действия, чтобы разрешить приложению Xamarin. iOS взаимодействовать с [Microsoft Authenticator](https://itunes.apple.com/us/app/microsoft-authenticator/id983156458) приложением.

#### <a name="step-1-enable-broker-support"></a>Шаг 1.: Включить поддержку брокера

Поддержка брокера включается отдельно для каждого`PublicClientApplication` компонента. Она отключена по умолчанию. Необходимо использовать `WithBroker()` параметр (по умолчанию задано значение true) при создании с `PublicClientApplication` помощью. `PublicClientApplicationBuilder`

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos) // $"msauth.{Bundle.Id}://auth" (see step 6 below)
                .Build();
```

#### <a name="step-2-update-appdelegate-to-handle-the-callback"></a>Шаг 2.: Обновление AppDelegate для обработки обратного вызова

Когда MSAL.NET вызывает брокер, брокер, в свою очередь, выполняет обратный вызов к приложению с помощью `AppDelegate.OpenUrl` метода. Так как MSAL будет ждать ответа от брокера, ваше приложение должно взаимодействовать для вызова MSAL.NET назад. Для этого необходимо обновить `AppDelegate.cs` файл, переопределив приведенный ниже метод.

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

#### <a name="step-3-set-a-uiviewcontroller"></a>Шаг 3. Задать UIViewController ()

В случае Xamarin iOS вам обычно не нужно задавать окно объекта, но в данном случае это делается для отправки и получения ответов от брокера. По- `AppDelegate.cs`прежнему в установите ViewController.

Чтобы задать окно объекта, выполните следующие действия.

1) В `AppDelegate.cs` задайте`App.RootViewController` новое значение`UIViewController()`. Это позволит убедиться, что `UIViewController` с вызовом брокера. Если оно задано неправильно, может появиться следующее сообщение об ошибке:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
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

#### <a name="step-4-register-a-url-scheme"></a>Шаг 4. Регистрация схемы URL-адресов

MSAL.NET использует URL-адреса для вызова брокера, а затем возвращает ответ брокера обратно в приложение. Чтобы завершить цикл обработки, необходимо зарегистрировать схему URL-адресов для приложения в `Info.plist` файле.

Добавьте в `CFBundleURLSchemes` `msauth`префикс. Затем добавьте `CFBundleURLName` в конец.

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

#### <a name="step-5-lsapplicationqueriesschemes"></a>Шаг 5. лсаппликатионкуериессчемес

MSAL использует `–canOpenURL:` для проверки установки брокера на устройстве. В iOS 9 Компания Apple заблокировала, какие схемы приложение может запрашивать.

**Добавить** **`msauthv2`** в`LSApplicationQueriesSchemes` раздел файла`Info.plist` .

```XML 
<key>LSApplicationQueriesSchemes</key>
    <array>
      <string>msauthv2</string>
    </array>
```

### <a name="brokered-authentication-for-xamarinandroid"></a>Аутентификация через посредника для Xamarin. Android

MSAL.NET пока не поддерживает брокеры для Android.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Получение маркера](scenario-mobile-acquire-token.md)
