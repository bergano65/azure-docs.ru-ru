---
title: Рекомендации по браузеру системы Xamarin Android (MSAL.NET) | Службы
titleSuffix: Microsoft identity platform
description: Ознакомьтесь с конкретными соображениями при использовании системных браузеров Xamarin Android с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
author: TylerMSFT
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/30/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d3ea2554fac8654b052e3e38633af23e7c778b3
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/08/2019
ms.locfileid: "74915463"
---
#  <a name="xamarin-android-system-browser-considerations-with-msalnet"></a>Рекомендации по браузеру системы Xamarin Android с MSAL.NET

В этой статье рассматриваются конкретные рекомендации при использовании системного браузера в Xamarin Android с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).

Начиная с MSAL.NET 2.4.0-Preview, MSAL.NET поддерживает браузеры, отличные от Chrome и больше не требует установки Chrome на устройстве Android для проверки подлинности.

Рекомендуется использовать браузеры, поддерживающие пользовательские вкладки, например следующие:

| Браузеры с поддержкой пользовательских вкладок | Имя пакета |
|------| ------- |
|Chrome | com.android.chrome|
|Microsoft Edge | com. Microsoft. еммкс|
|Firefox | org. Mozilla. Firefox|
|екосиа | com. екосиа. Android|
|Киви | com. кивибровсер. browser|
|Дивный | com. дивный. browser|

Помимо браузеров с поддержкой пользовательских вкладок на основе нашего тестирования, для проверки подлинности также будет работать несколько браузеров, которые не поддерживают пользовательские вкладки: Opera, Opera Mini, Browser и Макссон. Дополнительные сведения см. в [таблице для получения результатов теста](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Известные проблемы

- Если на устройстве не включен браузер, MSAL.NET выдаст исключение `AndroidActivityNotFound`. 
  - **Устранение**. Сообщите пользователю, что он должен включить браузер (предпочтительно с поддержкой настраиваемых вкладок) на своем устройстве.

- Если проверка подлинности завершается неудачей (например, Проверка подлинности запускается с Дуккдуккго), MSAL.NET вернет `AuthenticationCanceled MsalClientException`. 
  - **Проблема с корневым каталогом**. на устройстве не включен браузер с поддержкой пользовательских вкладок. Проверка подлинности запущена в альтернативном браузере, который не смог пройти проверку подлинности. 
  - **Устранение**. Сообщите пользователю, что он должен установить браузер (предпочтительно с поддержкой настраиваемой вкладки) на своем устройстве.

## <a name="devices-and-browsers-tested"></a>Тестируемые устройства и браузеры
В следующей таблице перечислены проверенные устройства и браузеры.

| | &ast; браузера     |  Результат  | 
| ------------- |:-------------:|:-----:|
| Huawei/один + | &ast; Chrome | Pass;|
| Huawei/один + | Пограничные&ast; | Pass;|
| Huawei/один + | Firefox&ast; | Pass;|
| Huawei/один + | Дивный&ast; | Pass;|
| Один + | Екосиа&ast; | Pass;|
| Один + | Киви&ast; | Pass;|
| Huawei/один + | Opera | Pass;|
| Huawei | операмини | Pass;|
| Huawei/один + | Браузер | Pass;|
| Один + | Maxthon | Pass;|
| Huawei/один + | дуккдуккго | Проверка подлинности отменена пользователем|
| Huawei/один + | Браузер UC | Проверка подлинности отменена пользователем|
| Один + | долфин | Проверка подлинности отменена пользователем|
| Один + | Браузер CM | Проверка подлинности отменена пользователем|
| Huawei/один + | нет установленных | Андроидактивитинотфаунд, пример|

&ast; поддерживает пользовательские вкладки

## <a name="next-steps"></a>Дальнейшие действия
Фрагменты кода и дополнительные сведения об использовании системного браузера с Xamarin Android см. в этом [руководству](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  