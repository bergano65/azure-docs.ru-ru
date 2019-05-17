---
title: Рекомендации по Xamarin Android (библиотека проверки подлинности Майкрософт для .NET) | Azure
description: Дополнительные сведения о специальные рекомендации при использовании Xamarin Android с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: ryanwi
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: cb0cfb06e95cadbb549f669e5d59bdb0d795c896
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545853"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Рекомендации по относящихся к Xamarin Android с помощью MSAL.NET
В этой статье рассматриваются специальные рекомендации, при использовании Xamarin Android с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).

Эта статья предназначена для MSAL.NET версии 3.x. Если вы заинтересованы в MSAL.NET 2.x, см. в разделе [особенности Xamarin Android в MSAL.NET 2.x](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Xamarin-Android-specifics-2x).

## <a name="set-the-parent-activity"></a>Значение родительского действия

В Xamarin.Android необходимо задать родительского действия, чтобы маркер получает обратно в том случае, когда произошло взаимодействие.

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

## <a name="ensuring-control-goes-back-to-msal-once-the-interactive-portion-of-the-authentication-flow-ends"></a>Обеспечение управления возвращается к MSAL один раз интерактивная часть завершение потока проверки подлинности
В Android, необходимо переопределить `OnActivityResult` метод `Activity` и вызвать метод SetAuthenticationContinuationEventArgs класса AuthenticationContinuationHelper MSAL.

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
Эту строку гарантирует, что элемент управления возвращается к MSAL после завершения интерактивная часть потока проверки подлинности.

## <a name="update-the-android-manifest"></a>Обновить манифест Android
`AndroidManifest.xml` Должен содержать следующие значения:
```csharp
<activity android:name="microsoft.identity.client.BrowserTabActivity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
        <category android:name="android.intent.category.BROWSABLE" />
        <data android:scheme="msal{client_id}" android:host="auth" />
         </intent-filter>
</activity>
```

## <a name="use-the-embedded-web-view-optional"></a>Использование внедренных веб-представлении (необязательно)

По умолчанию MSAL.NET использует веб-браузера системы, который позволяет получить единый вход с веб-приложениями и другими приложениями. В некоторых редких случаях может потребоваться указать, что вы хотите использовать встроенный веб-представления. Дополнительные сведения см. в разделе [MSAL.NET использует веб-браузер](msal-net-web-browsers.md) и [браузера системы Android](msal-net-system-browser-android-considerations.md).

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

## <a name="troubleshooting"></a>Устранение неполадок
При создании приложения Xamarin.Forms и добавьте ссылку на пакет MSAL.Net NuGet, это будет просто работать.
Тем не менее если вы хотите обновить существующее приложение Xamarin.Forms для MSAL.NET предварительной версии 1.1.2 или более поздней версии могут возникать проблемы с построения.

Чтобы устранить эти проблемы, сделайте следующее:
- Обновление существующего пакета MSAL.NET NuGet до предварительной версии MSAL.NET 1.1.2 или более поздней версии
- Проверьте, что Xamarin.Forms, автоматически обновляются до версии 2.5.0.122203 (если нет, обновление до этой версии)
- Проверьте, что Xamarin.Android.Support.v4 автоматически обновляются до версии 25.4.0.2 (если нет, обновление до этой версии)
- Все пакеты Xamarin.Android.Support должен предназначенных для версий 25.4.0.2
- Очистки и перестроения
- Попробуйте установить max проекта параллельных сборок, 1, в Visual Studio (параметры "->" Проекты и решения "->" сборки и запустить -> максимальное число параллельных проектах сборок)
- Кроме того при построении из командной строки, попробуйте удалить /m из вашей команды, если вы используете.


### <a name="error-the-name-authenticationcontinuationhelper-does-not-exist-in-the-current-context"></a>Ошибка: Имя «AuthenticationContinuationHelper» не существует в текущем контексте

Это, вероятно, так как Visual Studio не удалось правильно обновить файл Android.csproj*. Иногда **<HintPath>** filepath неправильно содержит netstandard13 вместо **monoandroid90**.

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения и примеры приведены в [Android специальные рекомендации](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations) абзац файл readme.md в следующем примере:

| Образец | платформа | Описание |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin iOS, Android, UWP; | Простое приложение Xamarin Forms, с отображением использование MSAL для проверки подлинности MSA и Azure AD через ДОБАВЬТЕ конечную точку версии 2.0 и получить доступ к Microsoft Graph с помощью полученного маркера. <br>![Топология](media/msal-net-xamarin-android-considerations/topology.png) |