---
title: Xamarin Android системы браузера соображения (MSAL.NET) Azure
titleSuffix: Microsoft identity platform
description: Узнайте о соображениях использования системных браузеров на Xamarin Android с библиотекой подлинности Microsoft для .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: marsma
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: ad26a4d619a7984f08a8decc87f9339adae47cdd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77132616"
---
#  <a name="xamarin-android-system-browser-considerations-for-using-msalnet"></a>Xamarin Android системы браузера соображения для использования MSAL.NET

В этой статье обсуждается, что вы должны учитывать при использовании системного браузера на Xamarin Android с библиотекой подлинности Microsoft для .NET (MSAL.NET).

Начиная с MSAL.NET 2.4.0 Предварительный просмотр, MSAL.NET поддерживает браузеры, кроме Chrome. Он больше не требует, чтобы Chrome был установлен на устройстве Android для проверки подлинности.

Мы рекомендуем использовать браузеры, поддерживающие пользовательские вкладки. Вот несколько примеров этих браузеров:

| Браузеры, которые имеют пользовательскую поддержку вкладок | Имя пакета |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.mozilla.firefox|
|Экозия | com.ecosia.android|
|Киви | com.kiwibrowser.browser.браузер|
|Храбрый | com.brave.browser|

В дополнение к идентификации браузеров, которые предлагают поддержку пользовательских вкладок, наше тестирование показывает, что несколько браузеров, которые не поддерживают пользовательские вкладки также работают для проверки подлинности. Эти браузеры включают Opera, Opera Mini, InBrowser и Maxthon. 

## <a name="tested-devices-and-browsers"></a>Проверенные устройства и браузеры
В следующей таблице перечислены устройства и браузеры, которые были протестированы на совместимость с проверкой подлинности.

| Устройство | Браузер     |  Результат  | 
| ------------- |:-------------:|:-----:|
| Huawei/Один из | Chrome\* | Успех|
| Huawei/Один из | Edge\* | Успех|
| Huawei/Один из | Firefox\* | Успех|
| Huawei/Один из | Храбрый\* | Успех|
| Один из них | Экозия\* | Успех|
| Один из них | Киви\* | Успех|
| Huawei/Один из | Opera | Успех|
| Huawei | OperaMini | Успех|
| Huawei/Один из | InBrowser | Успех|
| Один из них | Maxthon | Успех|
| Huawei/Один из | Duckduckgo | Пользователь отменил проверку подлинности|
| Huawei/Один из | UC Браузер | Пользователь отменил проверку подлинности|
| Один из них | Дельфин | Пользователь отменил проверку подлинности|
| Один из них | CM Браузер | Пользователь отменил проверку подлинности|
| Huawei/Один из | Ни один из установленных | AndroidActivityNotFound исключение|

\*Поддерживает пользовательские вкладки

## <a name="known-issues"></a>Известные проблемы

Если у пользователя нет включенного браузера на `AndroidActivityNotFound` устройстве, MSAL.NET будет заверять.  
  - **Смягчение:** Попросите пользователя включить браузер на своем устройстве. Рекомендуйте браузер, поддерживающий пользовательские вкладки.

Если проверка подлинности не удается (например, если проверка `AuthenticationCanceled MsalClientException`подлинности запускается с помощью DuckDuckGo), MSAL.NET вернется. 
  - **Корневая проблема:** Браузер, поддерживающий пользовательские вкладки, не был включен на устройстве. Аутентификация запущена с помощью браузера, который не может завершить аутентификацию. 
  - **Смягчение:** Попросите пользователя включить браузер на своем устройстве. Рекомендуйте браузер, поддерживающий пользовательские вкладки.

## <a name="next-steps"></a>Дальнейшие действия
Для получения дополнительной информации и примеров кода, см [Выбор между встроенным веб-браузером и системным браузером на Xamarin Android](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid) и Embedded против [системного веб-интерфейса.](msal-net-web-browsers.md#embedded-vs-system-web-ui)  