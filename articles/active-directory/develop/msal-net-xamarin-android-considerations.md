---
title: Xamarin Android соображения (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о соображениях использования Xamarin Android с библиотекой подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 04/24/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81b55253d757f641979c6f72001803d7d38d9af3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132504"
---
# <a name="considerations-for-using-xamarin-android-with-msalnet"></a>Рассмотрение использования Xamarin Android с MSAL.NET
В этой статье обсуждается, что вы должны учитывать при использовании Xamarin Android с библиотекой подлинности Microsoft для .NET (MSAL.NET).

## <a name="set-the-parent-activity"></a>Установка родительской деятельности

На Xamarin Android установите родительскую активность так, чтобы токен возвращался после взаимодействия. Приведем пример кода:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

В MSAL 4.2 и позже, вы также можете `PublicClientApplication`установить эту функциональность на уровне . Для этого используйте обратный вызов:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Если вы используете [CurrentActivityPlugin,](https://github.com/jamesmontemagno/CurrentActivityPlugin)то код `PublicClientApplication` строителя выглядит следующим примером.

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Убедитесь, что контроль возвращается в MSAL 
Когда интерактивная часть потока проверки подлинности заканчивается, убедитесь, что управление восходит к MSAL. На Android переопределить `OnActivityResult` метод `Activity`. Затем позвоните по `AuthenticationContinuationHelper` методу `SetAuthenticationContinuationEventArgs` класса MSAL. 

Ниже приведен пример:

```csharp
protected override void OnActivityResult(int requestCode, 
                                         Result resultCode, Intent data)
{
 base.OnActivityResult(requestCode, resultCode, data);
 AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                         resultCode,
                                                                         data);
}

```

Эта строка гарантирует, что элемент управления возвращается в MSAL в конце интерактивной части потока проверки подлинности.

## <a name="update-the-android-manifest"></a>Обновление Android манифест
Файл *AndroidManifest.xml* должен содержать следующие значения:

<!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
```
  <activity
        android:name="com.microsoft.identity.client.BrowserTabActivity">
     <intent-filter>
            <action android:name="android.intent.action.VIEW" />
            <category android:name="android.intent.category.DEFAULT" />
            <category android:name="android.intent.category.BROWSABLE" />
            <data android:scheme="msauth"
                android:host="Enter_the_Package_Name"
                android:path="/Enter_the_Signature_Hash" />
     </intent-filter>
 </activity>
```

Заменить имя пакета, зарегистрированное на `android:host=` портале Azure, для значения. Замените значение ключевого хэша, зарегистрированного `android:path=` на портале Azure. Хэш подписи *не* должен быть закодирован URL. Убедитесь, что ведущий слэш ()`/`появляется в начале хэша подписи.

Кроме того, [создайте действие в коде,](https://docs.microsoft.com/xamarin/android/platform/android-manifest#the-basics) а не вручную редактировать *AndroidManifest.xml.* Чтобы создать действие в коде, сначала `Activity` создайте `IntentFilter` класс, который включает атрибут и атрибут. 

Вот пример класса, который представляет значения файла XML:

```csharp
  [Activity]
  [IntentFilter(new[] { Intent.ActionView },
        Categories = new[] { Intent.CategoryBrowsable, Intent.CategoryDefault },
        DataHost = "auth",
        DataScheme = "msal{client_id}")]
  public class MsalActivity : BrowserTabActivity
  {
  }
```

### <a name="xamarinforms-43x-manifest"></a>Xamarin.Forms 4.3.X манифест

Xamarin.Forms 4.3.x генерирует код, `package` который `com.companyname.{appName}` устанавливает атрибут в *AndroidManifest.xml*. Если вы `DataScheme` `msal{client_id}`используете в качестве, то вы можете изменить `MainActivity.cs` значение, чтобы соответствовать значению пространства имен.

## <a name="use-the-embedded-web-view-optional"></a>Используйте встроенный веб-вид (необязательно)

По умолчанию MSAL.NET использует веб-браузер системы. Этот браузер позволяет получить одиночный вписаться (SSO) с помощью веб-приложений и других приложений. В некоторых редких случаях можно захотеть, чтобы ваша система использовала встроенный веб-вид. 

Этот пример кода показывает, как настроить встроенный веб-вид:

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Для получения дополнительной информации, см [Xamarin Android system browser considerations](msal-net-system-browser-android-considerations.md) [MSAL.NET.](msal-net-web-browsers.md)


## <a name="troubleshoot"></a>Устранение неполадок
Вы можете создать новое приложение Xamarin.Forms и добавить ссылку на пакет MSAL.NET NuGet.
Но, возможно, возникнут проблемы с сборкой, если обновить существующее приложение Xamarin.Forms для MSAL.NET просмотра 1.1.2 или позже.

Для устранения неполадок в сборке:

- Обновление существующего пакета NuGet MSAL.NET, чтобы MSAL.NET просмотра 1.1.2 или позже.
- Убедитесь, что Xamarin.Forms автоматически обновляется до версии 2.5.0.122203. При необходимости обновите Xamarin.Forms в эту версию.
- Убедитесь, что Xamarin.Android.Support.v4 автоматически обновляется до версии 25.4.0.2. При необходимости обновите до версии 25.4.0.2.
- Убедитесь, что все пакеты Xamarin.Android.Support нацелены на версию 25.4.0.2.
- Очистите или перестроить приложение.
- В Visual Studio попробуйте установить максимальное количество параллельных построений проекта до 1. Для этого выберите **Параметры** > **проектов и решений** > **построить и запустить** > **максимальное количество параллельных построений проектов.**
- Если вы строите из командной строки и использует `/m`команду, попробуйте удалить этот элемент из команды.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Ошибка: Имя АутентификацияПродолжениеХлер не существует в текущем контексте

Если ошибка указывает `AuthenticationContinuationHelper` на то, что в текущем контексте ее нет, Visual Studio может неправильно обновить файл Android.csproj. Иногда путь файла * \<HintPath>* неправильно содержит *netstandard13* вместо *моноандроида90.*

Этот пример содержит правильный путь файла:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации смотрите пример [мобильного приложения Xamarin, использующем платформу идентификации Майкрософт.](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) В следующей таблице приводится обобщение соответствующей информации в файле README.

| Пример | Платформа | Описание |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin.iOS, Android, UWP | Простое приложение Xamarin.Forms, которое показывает, как использовать MSAL для аутентификации личных учетных записей Майкрософт и Azure AD через конечную точку Azure AD 2.0. Приложение также показывает, как получить доступ к Microsoft Graph и показывает полученный токен. <br>![Топология](media/msal-net-xamarin-android-considerations/topology.png) |
