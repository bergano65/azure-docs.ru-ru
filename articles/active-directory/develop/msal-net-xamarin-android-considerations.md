---
title: Настройка кода и устранение неполадок в Xamarin Android (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Сведения о вопросах использования Xamarin Android с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: jmprieur
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 08/28/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 256bee26ec6aef464d9ee838ffca4d8c1ecbbb4e
ms.sourcegitcommit: 8a7b82de18d8cba5c2cec078bc921da783a4710e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2020
ms.locfileid: "89047792"
---
# <a name="configuration-requirements-and-troubleshooting-tips-for-xamarin-android-with-msalnet"></a>Требования к конфигурации и советы по устранению неполадок для Xamarin Android с MSAL.NET

Существует несколько изменений конфигурации, которые необходимо внести в код при использовании Xamarin Android с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET). В следующих разделах описываются необходимые изменения, а затем раздел " [Устранение неполадок](#troubleshooting) ", который поможет избежать некоторых наиболее распространенных проблем.

## <a name="set-the-parent-activity"></a>Задание родительского действия

В Xamarin Android задайте родительское действие, чтобы токен возвращался после взаимодействия:

```csharp
var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .ExecuteAsync();
```

В MSAL.NET 4,2 и более поздних версиях эту функцию можно также установить на уровне [публикклиентаппликатион][PublicClientApplication]. Для этого используйте обратный вызов:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => parentActivity)
  .Build();
```

Если вы используете [куррентактивитиплугин](https://github.com/jamesmontemagno/CurrentActivityPlugin), [`PublicClientApplication`][PublicClientApplication] код построителя должен выглядеть примерно так:

```csharp
// Requires MSAL.NET 4.2 or later
var pca = PublicClientApplicationBuilder
  .Create("<your-client-id-here>")
  .WithParentActivityOrWindow(() => CrossCurrentActivity.Current)
  .Build();
```

## <a name="ensure-that-control-returns-to-msal"></a>Убедитесь, что управление возвращается к MSAL.

После завершения интерактивной части потока проверки подлинности верните управление в MSAL, переопределив [`Activity`][Activity] .[`OnActivityResult()`][OnActivityResult] метод.

В переопределении вызовите MSAL. NET `AuthenticationContinuationHelper` .`SetAuthenticationContinuationEventArgs()` метод, возвращающий управление MSAL в конце интерактивной части потока проверки подлинности.

```csharp
protected override void OnActivityResult(int requestCode,
                                         Result resultCode,
                                         Intent data)
{
    base.OnActivityResult(requestCode, resultCode, data);

    // Return control to MSAL
    AuthenticationContinuationHelper.SetAuthenticationContinuationEventArgs(requestCode,
                                                                            resultCode,
                                                                            data);
}
```

## <a name="update-the-android-manifest"></a>Обновление манифеста Android

Файл *AndroidManifest.xml* должен содержать следующие значения:

```XML
  <!--Intent filter to capture System Browser or Authenticator calling back to our app after sign-in-->
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

Замените имя пакета, зарегистрированное в портал Azure, на `android:host=` значение. Замените хэш ключа, зарегистрированный в портал Azure, на `android:path=` значение. Хэш подписи *не* должен быть закодирован в URL-адресе. Убедитесь, что в начале хэша подписи отображается начальная косая черта ( `/` ).

Кроме того, можно [создать действие в коде](/xamarin/android/platform/android-manifest#the-basics) , а не вручную изменять *AndroidManifest.xml*. Чтобы создать действие в коде, сначала создайте класс, включающий `Activity` атрибут и `IntentFilter` атрибут.

Ниже приведен пример класса, представляющего значения XML-файла:

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

### <a name="xamarinforms-43x-manifest"></a>Манифест Xamarin. Forms 4.3. x

Xamarin. Forms 4.3. x создает код, который присваивает `package` атрибуту значение `com.companyname.{appName}` в *AndroidManifest.xml*. Если используется `DataScheme` как `msal{client_id}` , может потребоваться изменить значение в соответствии со значением `MainActivity.cs` пространства имен.

## <a name="use-the-embedded-web-view-optional"></a>Использовать внедренное веб-представление (необязательно)

По умолчанию MSAL.NET использует системный веб-браузер. Этот браузер позволяет получить единый вход (SSO) с помощью веб-приложений и других приложений. В некоторых редких случаях может потребоваться, чтобы система использовала встроенное веб-представление.

В этом примере кода показано, как настроить встроенное веб-представление.

```csharp
bool useEmbeddedWebView = !app.IsSystemWebViewAvailable;

var authResult = AcquireTokenInteractive(scopes)
 .WithParentActivityOrWindow(parentActivity)
 .WithEmbeddedWebView(useEmbeddedWebView)
 .ExecuteAsync();
```

Дополнительные сведения см. в статье [Использование веб-браузеров для MSAL.NET и в](msal-net-web-browsers.md) [браузере Xamarin Android System](msal-net-system-browser-android-considerations.md).

## <a name="troubleshooting"></a>Устранение неполадок

### <a name="general-tips"></a>Общие советы

- Обновите существующий пакет NuGet MSAL.NET до [последней версии MSAL.NET](https://www.nuget.org/packages/Microsoft.Identity.Client/).
- Убедитесь, что Xamarin. Forms находится в последней версии.
- Убедитесь, что Xamarin. Android. support. v4 находится в последней версии.
- Убедитесь, что все пакеты Xamarin. Android. support предназначены для последней версии.
- Очистите или перестройте приложение.
- В Visual Studio попробуйте установить максимальное число параллельных сборок проекта равным **1**. Для этого выберите **Параметры**  >  **проекты и решения**  >  **Создание и запуск**  >  **максимального числа параллельных сборок проектов**.
- Если вы создаете из командной строки и используете команду `/m` , попробуйте удалить этот элемент из команды.

### <a name="error-the-name-authenticationcontinuationhelper-doesnt-exist-in-the-current-context"></a>Ошибка: имя Аусентикатионконтинуатионхелпер не существует в текущем контексте

Если ошибка указывает, что `AuthenticationContinuationHelper` не существует в текущем контексте, возможно, Visual Studio неправильно обновил файл *Android. csproj \* * . Иногда путь к файлу в `<HintPath>` элементе неправильно содержит `netstandard13` вместо `monoandroid90` .

Этот пример содержит правильный путь к файлу:

```xml
<Reference Include="Microsoft.Identity.Client, Version=3.0.4.0, Culture=neutral, PublicKeyToken=0a613f4dd989e8ae,
           processorArchitecture=MSIL">
  <HintPath>..\..\packages\Microsoft.Identity.Client.3.0.4-preview\lib\monoandroid90\Microsoft.Identity.Client.dll</HintPath>
</Reference>
```

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения см. в примере [мобильного приложения Xamarin, использующего платформу Microsoft Identity](https://github.com/azure-samples/active-directory-xamarin-native-v2#android-specific-considerations). В следующей таблице перечислены соответствующие сведения в файле сведений.

| Пример | Платформа | Описание |
| ------ | -------- | ----------- |
|[https://github.com/Azure-Samples/active-directory-xamarin-native-v2](https://github.com/azure-samples/active-directory-xamarin-native-v2) | Xamarin. iOS, Android, UWP | Простое приложение Xamarin. Forms, которое показывает, как использовать MSAL для проверки подлинности личных учетных записей Майкрософт и Azure AD через конечную точку Azure AD 2,0. В приложении также показано, как получить доступ к Microsoft Graph и показать получившийся маркер. <br>![Схема потока проверки подлинности](media/msal-net-xamarin-android-considerations/topology.png) |

<!-- REF LINKS -->
[PublicClientApplication]: /dotnet/api/microsoft.identity.client.publicclientapplication
[OnActivityResult]: /dotnet/api/android.app.activity.onactivityresult
[Activity]: /dotnet/api/android.app.activity
