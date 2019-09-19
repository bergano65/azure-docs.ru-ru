---
title: Перенос приложений Xamarin iOS, использующих Microsoft Authenticator из ADAL.NET в MSAL.NET | Службы
description: Узнайте, как перенести приложения Xamarin iOS, использующие Microsoft Authenticator из библиотеки аутентификация Azure AD для .NET (ADAL.NET) в библиотеку проверки подлинности Майкрософт для .NET (MSAL.NET).
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
ms.openlocfilehash: 4426c070d2fd2e26e0d4368a94996bc32e95129d
ms.sourcegitcommit: 1c9858eef5557a864a769c0a386d3c36ffc93ce4
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/18/2019
ms.locfileid: "71103956"
---
# <a name="migrate-ios-applications-that-use-microsoft-authenticator-from-adalnet-to-msalnet"></a>Перенос приложений iOS, использующих Microsoft Authenticator из ADAL.NET в MSAL.NET

Вы использовали библиотеку проверки подлинности Azure Active Directory для .NET (ADAL.NET) и брокера iOS. Теперь пора перейти на [библиотеку проверки подлинности Майкрософт](msal-overview.md) для .net (MSAL.NET), которая поддерживает брокер в iOS из выпуска 4,3. 

Где начать? Эта статья поможет вам перенести приложение Xamarin iOS из ADAL в MSAL.

## <a name="prerequisites"></a>Предварительные требования
В этой статье предполагается, что у вас уже есть приложение Xamarin iOS, интегрированное с брокером iOS. Если этого не сделать, перейдите непосредственно к MSAL.NET и начните реализацию брокера. Сведения о том, как вызвать брокер iOS в MSAL.NET с помощью нового приложения, см. в [этой документации](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Leveraging-the-broker-on-iOS#why-use-brokers-on-xamarinios-and-xamarinandroid-applications).

## <a name="background"></a>Фоновый

### <a name="what-are-brokers"></a>Что такое брокеры?

Брокеры — это приложения, предоставляемые корпорацией Майкрософт в Android и iOS. (См. [Microsoft Authenticatorное](https://www.microsoft.com/account/authenticator) приложение в iOS и Android, а также корпоративный портал Intuneное приложение на Android.) 

Они включают:

- Единый вход.
- Идентификация устройства, необходимая для некоторых [политик условного доступа](../conditional-access/overview.md). Дополнительные сведения см. в разделе [Управление устройствами](../conditional-access/conditions.md#device-platforms).
- Проверка идентификации приложения, которая также необходима в некоторых корпоративных сценариях. Дополнительные сведения см. в статье [Управление мобильными приложениями Intune (MAM)](https://docs.microsoft.com/intune/mam-faq).

## <a name="migrate-from-adal-to-msal"></a>Миграция из ADAL в MSAL

### <a name="step-1-enable-the-broker"></a>Шаг 1. Включение брокера

<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>
В ADAL.NET поддержка брокера включена для каждого контекста проверки подлинности. Она отключена по умолчанию. Необходимо было задать 

`useBroker`для вызова брокера пометьте значение true в `PlatformParameters` конструкторе:

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
В MSAL.NET поддержка брокера включена для каждого Публикклиентаппликатион. Она отключена по умолчанию. Чтобы включить его, используйте 

`WithBroker()`параметр (по умолчанию имеет значение true), чтобы вызвать брокер:

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
В ADAL.NET вы передали UIViewController как часть `PlatformParameters`. (См. пример на шаге 1.) В MSAL.NET для предоставления разработчикам большей гибкости используется окно объекта, но оно не является обязательным при использовании обычного использования iOS. Чтобы использовать брокер, задайте окно объекта для отправки и получения ответов от брокера. 
<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>
UIViewController передается в 

`PlatformParameters`на платформе iOS.

```CSharp
page.BrokerParameters = new PlatformParameters(
          this, 
          true, 
          PromptBehavior.SelectAccount);
```
</td><td>
В MSAL.NET вы выполняете два действия, чтобы задать окно объекта для iOS:

1. В `AppDelegate.cs`задайте новое `App.RootViewController` значение.`UIViewController()` Это назначение гарантирует, что UIViewController с вызовом брокера. Если оно задано неправильно, может появиться следующее сообщение об ошибке:`"uiviewcontroller_required_for_ios_broker":"UIViewController is null, so MSAL.NET cannot invoke the iOS broker. See https://aka.ms/msal-net-ios-broker"`
1. В вызове аккуиретокенинтерактиве используйте `.WithParentActivityOrWindow(App.RootViewController)`и передайте ссылку на окно объекта, которое вы будете использовать.

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
И ADAL, и MSAL вызывают брокер, а брокер, в свою очередь, обращается к приложению через `OpenUrl` метод `AppDelegate` класса. Дополнительные сведения см. в этой [документации](msal-net-use-brokers-with-xamarin-apps.md#step-2-update-appdelegate-to-handle-the-callback).

Нет изменений между ADAL.NET и MSAL.NET.

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
> Эта схема URL-адреса станет частью URI перенаправления, который используется для уникальной идентификации приложения при получении ответа от брокера.

</table>

### <a name="step-5-add-the-broker-identifier-to-the-lsapplicationqueriesschemes-section"></a>Шаг 5. Добавление идентификатора брокера в раздел Лсаппликатионкуериессчемес

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

### <a name="step-6-register-your-redirect-uri-in-the-portal"></a>Шаг 6. Регистрация URI перенаправления на портале

ADAL.NET и MSAL.NET добавляют дополнительные требования к URI перенаправления, когда он предназначен для брокера. Зарегистрируйте универсальный код ресурса (URI) перенаправления в приложении на портале.
<table>
<tr><td>Текущий код ADAL:</td><td>MSAL аналога:</td></tr>
<tr><td>

`"<app-scheme>://<your.bundle.id>"`

Пример: 

`mytestiosapp://com.mycompany.myapp`
</td><td>

`$"msauth.{BundleId}://auth"`

Пример:

`public static string redirectUriOnIos = "msauth.com.yourcompany.XForms://auth"; `

</table>

Дополнительные сведения о регистрации URI перенаправления на портале см. в разделе [Использование брокера в приложениях Xamarin. iOS](msal-net-use-brokers-with-xamarin-apps.md#step-7-make-sure-the-redirect-uri-is-registered-with-your-app).

## <a name="next-steps"></a>Следующие шаги

Узнайте о [вопросах, связанных с Xamarin iOS, в MSAL.NET](msal-net-xamarin-ios-considerations.md). 
