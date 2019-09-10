---
title: Перенос приложений Xamarin iOS с помощью Microsoft Authenticator из ADAL.NET в MSAL.NET | Службы
description: Узнайте, как перенести приложения Xamarin iOS с помощью Microsoft Authenticator из библиотеки аутентификация Azure AD для .NET (ADAL.NET) в библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.openlocfilehash: 609aeeada78985466cb7c254cab32864e5d84b0e
ms.sourcegitcommit: 23389df08a9f4cab1f3bb0f474c0e5ba31923f12
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70875657"
---
# <a name="migrating-ios-applications-using-microsoft-authenticator-from-adalnet-to-msalnet"></a>Перенос приложений iOS с помощью Microsoft Authenticator из ADAL.NET в MSAL.NET

Вы использовали ADAL.NET и брокер iOS, и пора перейти на MSAL.NET [библиотеку проверки подлинности Майкрософт](msal-overview.md), которая поддерживает брокер в iOS из выпуска 4,3. 

Как начать работу? Эта статья поможет вам перенести приложение Xamarin iOS из ADAL в MSAL.

## <a name="prerequisites"></a>Предварительные требования
В этом документе предполагается, что у вас уже есть приложение Xamarin iOS, интегрированное с брокером iOS. В противном случае лучше перейти непосредственно к MSAL.NET и начать реализацию брокера. Дополнительные сведения о вызове брокера iOS в MSAL.NET с новым приложением см. в [этой документации](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications) .

## <a name="background"></a>Фоновый

### <a name="what-are-brokers"></a>Что такое брокеры?

Брокеры — это приложения, предоставляемые Майкрософт в Android и iOS ([Microsoft Authenticator](https://www.microsoft.com/account/authenticator) в iOS и android, корпоративный портал Intune на Android). 

Они включают:

- Единый вход,
- Идентификация устройств, необходимая для некоторых [политик условного доступа](../conditional-access/overview.md) (см. раздел [Управление устройствами](../conditional-access/conditions.md#device-platforms)).
- Проверка идентификации приложения, также необходимая в некоторых корпоративных сценариях (см. экземпляр [Управление мобильными приложениями Intune или MAM](https://docs.microsoft.com/intune/mam-faq)).

## <a name="migrate-from-adal-to-msal"></a>Миграция из ADAL в MSAL

### <a name="step-1-enable-the-broker"></a>Шаг 1. Включение брокера

<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>
В ADAL.NET поддержка брокера включена для каждого контекста проверки подлинности, она по умолчанию отключена. Для вызова брокера в `useBroker` `PlatformParameters` конструкторе необходимо установить флаг true.

```CSharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Кроме того, в коде, зависящем от платформы, в этом примере в модуле подготовки страниц для iOS установите`useBroker` 
пометить как истинное:
```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Затем включите параметры в вызов маркера получения:
```CSharp
 AuthenticationResult result =
                    await
                        AuthContext.AcquireTokenAsync(
                              Resource, 
                              ClientId, 
                              new Uri(RedirectURI), 
                              platformParameters)
                              .ConfigureAwait(false);
```

</td><td>
В MSAL.NET поддержка брокера включена для отдельных общедоступных клиентских приложений. Он отключен по умолчанию. Чтобы включить его, используйте: 

`WithBroker()`параметр (по умолчанию имеет значение true), чтобы вызвать Broker:

```CSharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
В вызове запроса маркера:
```CSharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Шаг 2. Задать UIViewController ()
В ADAL.NET вы передали UIViewController как часть Платформпараметерс (см. пример на шаге 1). Однако в MSAL.NET для предоставления разработчику большей гибкости используется окно объекта, но оно не требуется в обычном использовании iOS. Однако для использования брокера необходимо задать окно объекта, чтобы отправлять и получать ответы от брокера. 
<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>
UIViewController передается в Платформпарамтерс на конкретной платформе iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
В MSAL.NET необходимо выполнить два действия, чтобы задать окно объекта для iOS:

1) В `AppDelegate.cs` задайте`App.RootViewController` новое значение`UIViewController()`. Это назначение обеспечит UIViewController с вызовом брокера. Если оно задано неправильно, может появиться следующее сообщение об ошибке:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
2) При вызове Аккуиретокенинтерактиве используйте`.WithParentActivityOrWindow(App.RootViewController)`
и передать ссылку на окно объекта, йоу'вилл использовать.

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

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Шаг 3. Обновление AppDelegate для обработки обратного вызова
И ADAL, и MSAL будут вызывать брокер, а Broker, в свою очередь, будет обращаться к приложению через `OpenUrl` метод `AppDelegate` класса. Дополнительные сведения доступны [здесь](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS/_edit#step-two-update-appdelegate-to-handle-the-callback)

: heavy_check_mark:**Нет изменений между ADAL.NET и MSAL.NET**

### <a name="step-4-register-a-url-scheme"></a>Шаг 4. Регистрация схемы URL-адресов
ADAL.NET и MSAL.NET используют URL-адреса для вызова брокера и возврата ответа брокера обратно в приложение. Зарегистрируйте схему URL-адресов `Info.plist` в файле приложения следующим образом:

<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>
Схема URL-адресов уникальна для вашего приложения.
</td><td>
Атрибут 

`CFBundleURLSchemes`имя должно включать 

`msauth.`

в качестве префикса, за которым следует`CFBundleURLName`

Например: `$"msauth.(BundleId")`

```CSharp
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

> [!NOTE]
>  Эта схема URL-адресов станет частью RedirectUri, используемой для уникальной идентификации приложения при получении ответа от брокера.

</table>

### <a name="step-5-lsapplicationqueriesschemes"></a>Шаг 5. лсаппликатионкуериессчемес

ADAL.NET и MSAL.NET используют `-canOpenURL:` , чтобы проверить, установлен ли брокер на устройстве. Добавьте правильный идентификатор для брокера iOS в раздел Лсаппликатионкуериессчемес файла INFO. plist следующим образом: 
<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>
Использование 

`msauth`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Использование 

`msauthv2`


```CSharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
</array>
```
</table>

### <a name="step-6-register-you-redirecturi-in-the-portal"></a>Шаг 6. Регистрация RedirectUri на портале

ADAL.NET и MSAL.NET добавляют дополнительные требования к redirectUri при нацеливании на брокер. Зарегистрируйте универсальный код ресурса (URI) перенаправления в приложении на портале.
<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`Например`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Пример:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Дополнительные сведения о регистрации redirectUri на портале см. в статье Использование [брокера в приложениях Xamarin. iOS](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app) .

## <a name="next-steps"></a>Следующие шаги

Узнайте о [вопросах, связанных с Xamarin iOS, в MSAL.NET](msal-net-xamarin-ios-considerations.md). 
