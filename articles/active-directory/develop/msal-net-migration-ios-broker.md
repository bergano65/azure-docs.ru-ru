---
title: Перенос приложений Xamarin в MSAL.NET с помощью брокеров
titleSuffix: Microsoft identity platform
description: Узнайте, как перенести приложения Xamarin iOS, которые используют Microsoft Authenticator, из ADAL.NET в MSAL.NET.
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
ms.openlocfilehash: de259daa7fd27cc4f138c294a7f347502ca482a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185828"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Мемидируйте iOS-приложения, которые используют Microsoft Authenticator из ADAL.NET в MSAL.NET

Вы используете Библиотеку подлинности активных каталогов Azure для .NET (ADAL.NET) и брокера iOS. Теперь пришло время перейти в [библиотеку подлинности Microsoft](msal-overview.md) для .NET (MSAL.NET), которая поддерживает брокера на iOS от выпуска 4.3 года. 

С чего начать? Эта статья поможет вам перенести приложение Xamarin iOS из ADAL в MSAL.

## <a name="prerequisites"></a>Предварительные требования
Эта статья предполагает, что у вас уже есть приложение Xamarin iOS, интегрированное с брокером iOS. Если вы этого не сможете, перейдите непосредственно к MSAL.NET и начать реализацию брокера там. Для получения информации о том, как вызвать брокера iOS в MSAL.NET с новым приложением, смотрите [эту документацию](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Историческая справка

### <a name="what-are-brokers"></a>Что такое брокеры?

Брокеры — это приложения, предоставляемые корпорацией Майкрософт на Android и iOS. (См. приложение [Microsoft Authenticator](https://www.microsoft.com/p/microsoft-authenticator/9nblgggzmcj6) на iOS и Android, а также приложение Intune Company Portal на Android.) 

Они позволяют:

- Единый вход.
- Идентификация устройств, которая требуется [некоторыми политиками условного доступа.](../conditional-access/overview.md) Для получения дополнительной информации [см.](../conditional-access/concept-conditional-access-conditions.md#device-platforms)
- Проверка идентификации приложений, которая также необходима в некоторых корпоративных сценариях. Для получения дополнительной [Intune mobile application management (MAM)](https://docs.microsoft.com/intune/mam-faq)информации см.

## <a name="migrate-from-adal-to-msal"></a>Миграция из ADAL в MSAL

### <a name="step-1-enable-the-broker"></a>Шаг 1: Включить брокера

<table>
<tr><td>Текущий код ADAL:</td><td>Аналог MSAL:</td></tr>
<tr><td>
В ADAL.NET, поддержка брокера была включена на основе контекста проверки подлинности. Она отключена по умолчанию. Вы должны были установить 

`useBroker`флаг, чтобы `PlatformParameters` правда в конструктор, чтобы позвонить брокеру:

```csharp
public PlatformParameters(
        UIViewController callerViewController, 
        bool useBroker)
```
Кроме того, в коде, конкретном платформе, в этом примере, в редераторе страницы для iOS, установите`useBroker` 
флаг на истину:
```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```

Затем включите параметры в коллетона приобретения:
```csharp
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
В MSAL.NET, поддержка брокера включена на основе на основе публичного клиента. Она отключена по умолчанию. Для этого используйте 

`WithBroker()`параметр (установлен по умолчанию) для того, чтобы позвонить брокеру:

```csharp
var app = PublicClientApplicationBuilder
                .Create(ClientId)
                .WithBroker()
                .WithReplyUri(redirectUriOnIos)
                .Build();
```
В токене приобретается колл:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```
</table>

### <a name="step-2-set-a-uiviewcontroller"></a>Шаг 2: Установить UIViewController()
В ADAL.NET вы прошли в UIViewController `PlatformParameters`как часть . (См. пример в шаге 1.) В MSAL.NET, чтобы дать разработчикам больше гибкости, используется окно объекта, но оно не требуется при регулярном использовании iOS. Чтобы воспользоваться брокером, установите окно объекта для отправки и получения ответов от брокера. 
<table>
<tr><td>Текущий код ADAL:</td><td>Аналог MSAL:</td></tr>
<tr><td>
UIViewController передается в 

`PlatformParameters`в платформе для iOS.

```csharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
В MSAL.NET вы делаете две вещи, чтобы установить окно объекта для iOS:

1. В `AppDelegate.cs`, `App.RootViewController` установить `UIViewController()`на новый . Это назначение гарантирует наличие UIViewController с вызовом к брокеру. Если он не установлен правильно, вы можете получить эту ошибку:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. На вызове AcquireTokenInteractive `.WithParentActivityOrWindow(App.RootViewController)`и используйте и проходите в ссылке на окне объекта, которое вы будете использовать.

**Пример:**

В `App.cs`:
```csharp
   public static object RootViewController { get; set; }
```
В `AppDelegate.cs`:
```csharp
   LoadApplication(new App());
   App.RootViewController = new UIViewController();
```
В токене приобретается колл:
```csharp
result = await app.AcquireTokenInteractive(scopes)
             .WithParentActivityOrWindow(App.RootViewController)
             .ExecuteAsync();
```

</table>

### <a name="step-3-update-appdelegate-to-handle-the-callback"></a>Шаг 3: Обновление AppDelegate для обработки обратного вызова
И ADAL, и MSAL звонят брокеру, а брокер, `OpenUrl` в `AppDelegate` свою очередь, перезванивает к вашему приложению методом класса. Для получения дополнительной информации смотрите [эту документацию](msal-net-use-brokers-with-xamarin-apps.md#step-3-update-appdelegate-to-handle-the-callback).

Между ADAL.NET и MSAL.NET изменений нет.

### <a name="step-4-register-a-url-scheme"></a>Шаг 4: Регистрация схемы URL
ADAL.NET и MSAL.NET использовать URL-адреса, чтобы вызвать брокера и вернуть ответ брокера обратно в приложение. Зарегистрируйте схему `Info.plist` URL в файле для приложения следующим образом:

<table>
<tr><td>Текущий код ADAL:</td><td>Аналог MSAL:</td></tr>
<tr><td>
Схема URL-адреса уникальна для вашего приложения.
</td><td>
Компонент 

`CFBundleURLSchemes`имя должно включать 

`msauth.`

в качестве префикса, а затем ваш`CFBundleURLName`

Например: `$"msauth.(BundleId")`

```csharp
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
> Эта схема URL становится частью перенаправления URI, который используется для однозначной идентификации приложения, когда он получает ответ от брокера.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Шаг 5: Добавьте идентификатор брокера в раздел LSApplication-ЗапросыСхемы

ADAL.NET и MSAL.NET `-canOpenURL:` использовать как для проверки, если брокер установлен на устройстве. Добавьте правильный идентификатор для брокера iOS в раздел LSApplication-ЗапросыСхемы файла info.plist следующим образом:

<table>
<tr><td>Текущий код ADAL:</td><td>Аналог MSAL:</td></tr>
<tr><td>
Использование 

`msauth`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauth</string>
</array>
```
</td><td>
Использование 

`msauthv2`


```csharp
<key>LSApplicationQueriesSchemes</key>
<array>
     <string>msauthv2</string>
     <string>msauthv3</string>
</array>
```
</table>

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Шаг 6: Зарегистрируйте свой перенаправление URI на портале

ADAL.NET и MSAL.NET как добавить дополнительное требование на перенаправление URI, когда он ориентирован на брокера. Зарегистрируйте перенаправление URI с помощью приложения на портале.
<table>
<tr><td>Текущий код ADAL:</td><td>Аналог MSAL:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Пример 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Пример

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Для получения дополнительной информации о том, как зарегистрировать перенаправление URI на портале, [см.](msal-net-use-brokers-with-xamarin-apps.md#step-8-make-sure-the-redirect-uri-is-registered-with-your-app)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [конкретных соображениях Xamarin iOS с MSAL.NET.](msal-net-xamarin-ios-considerations.md) 
