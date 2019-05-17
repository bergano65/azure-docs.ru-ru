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
ms.openlocfilehash: c57feb33967732481d78e0ddaba5e90f4f82f327
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65544428"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Рекомендации по относящихся к Xamarin Android с помощью MSAL.NET
В этой статье рассматриваются специальные рекомендации, при использовании браузера системы на Xamarin Android с помощью библиотеки проверки подлинности Майкрософт для .NET (MSAL.NET).

Начиная с помощью MSAL.NET 2.4.0-preview MSAL.NET поддерживает обозреватели, отличные от Chrome и больше не требуется браузер Chrome устанавливаться на устройстве Android для проверки подлинности.

Мы рекомендуем использовать браузеры, поддерживающие пользовательских вкладок, такие:

| Браузеры с поддержкой пользовательских вкладок | Имя пакета |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com.microsoft.emmx|
|Firefox | org.Mozilla.Firefox|
|Ecosia | com.ecosia.android|
|Поставщик Киви | com.kiwibrowser.browser|
|Хватит смелости | com.brave.browser|

В дополнение к браузерах с поддержкой пользовательские вкладки, на нашем тестировании основе несколько браузеров, которые не поддерживают пользовательские вкладки также будет работать для проверки подлинности: Opera, Opera Mini InBrowser и Maxthon — дополнительный модуль. Дополнительные сведения в статье [таблицы для результатов тестов](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Известные проблемы

- Если у пользователя имеется браузер, не включен на устройстве, вызовет MSAL.NET `AndroidActivityNotFound` исключение. 
  - **Устранение.** Сообщения активировать браузера (предпочтительнее тот, с поддержкой пользовательских вкладок) на своем устройстве.

- При сбое проверки подлинности (например) Проверка подлинности запускается с DuckDuckGo), будет возвращать MSAL.NET `AuthenticationCanceled MsalClientException`. 
  - **Корневые проблемы**: Браузер с поддержкой пользовательских вкладок на устройстве не включено. Проверка подлинности запущено с альтернативного обозревателя, который не смог завершить проверку подлинности. 
  - **Устранение.** Информировать пользователей о том, следует установить браузер (предпочтительнее тот, с поддержкой пользовательская вкладка) на своем устройстве.

## <a name="devices-and-browsers-tested"></a>Мобильные устройства и браузеры протестированы
Ниже перечислены устройствах и браузерах, которые были протестированы.

| | браузер&ast;     |  Результат  | 
| ------------- |:-------------:|:-----:|
| Huawei / один + | Chrome&ast; | Этап|
| Huawei / один + | Edge&ast; | Этап|
| Huawei / один + | Firefox&ast; | Этап|
| Huawei / один + | Хватит смелости&ast; | Этап|
| Один + | Ecosia&ast; | Этап|
| Один + | Поставщик Киви&ast; | Этап|
| Huawei / один + | Opera | Этап|
| Huawei | OperaMini | Этап|
| Huawei / один + | InBrowser | Этап|
| Один + | Maxthon — дополнительный модуль | Этап|
| Huawei / один + | DuckDuckGo | Отменено пользователем проверки подлинности|
| Huawei / один + | Браузера UC Browser | Отменено пользователем проверки подлинности|
| Один + | Дельфин | Отменено пользователем проверки подлинности|
| Один + | Браузер CM | Отменено пользователем проверки подлинности|
| Huawei / один + | не установлено | AndroidActivityNotFound ex|

&ast; Поддерживает пользовательские вкладки

## <a name="next-steps"></a>Дальнейшие действия
Для кода фрагменты кода, а также дополнительные сведения об использовании браузера системы с Xamarin для Android, см. в этой [руководство](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  