---
title: Замечания по Xamarin Android (Библиотека проверки подлинности Microsoft для .NET) | Службы
description: Ознакомьтесь с конкретными соображениями при использовании Xamarin Android с библиотекой проверки подлинности Майкрософт для .NET (MSAL.NET).
services: active-directory
documentationcenter: dev-center-name
author: TylerMSFT
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/24/2019
ms.author: twhitney
ms.reviewer: saeeda
ms.custom: aaddev
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1b0c810097913e896027245b15600ed75aabcd25
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/16/2019
ms.locfileid: "69532578"
---
# <a name="xamarin-android-specific-considerations-with-msalnet"></a>Вопросы, связанные с Xamarin Android, с MSAL.NET
В этой статье рассматриваются конкретные рекомендации при использовании системного браузера в Xamarin Android с помощью библиотеки проверки подлинности Microsoft для .NET (MSAL.NET).

Начиная с MSAL.NET 2.4.0-Preview, MSAL.NET поддерживает браузеры, отличные от Chrome и больше не требует установки Chrome на устройстве Android для проверки подлинности.

Рекомендуется использовать браузеры, поддерживающие пользовательские вкладки, например следующие:

| Браузеры с поддержкой пользовательских вкладок | Имя пакета |
|------| ------- |
|Chrome | com. Android. Chrome|
|Microsoft Edge | com. Microsoft. еммкс|
|Firefox | org. Mozilla. Firefox|
|екосиа | com. екосиа. Android|
|Киви | com. кивибровсер. browser|
|Дивный | com. дивный. browser|

Помимо браузеров с поддержкой пользовательских вкладок на основе нашего тестирования, для проверки подлинности также будет работать несколько браузеров, которые не поддерживают пользовательские вкладки: Opera, Opera Mini, Browser и Макссон. Дополнительные сведения см. в [таблице для получения результатов теста](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/Android-system-browser#devices-and-browsers-tested).

## <a name="known-issues"></a>Известные проблемы

- Если на устройстве не включен браузер, MSAL.NET выдаст `AndroidActivityNotFound` исключение. 
  - **Устранение.** Сообщите пользователю, что он должен включить браузер (предпочтительно с поддержкой настраиваемых вкладок) на своем устройстве.

- Если проверка подлинности завершается неудачей (например, Проверка подлинности запускается с Дуккдуккго), MSAL.NET вернет `AuthenticationCanceled MsalClientException`. 
  - **Проблема**с корневым каталогом: На устройстве не включен браузер с поддержкой пользовательских вкладок. Проверка подлинности запущена в альтернативном браузере, который не смог пройти проверку подлинности. 
  - **Устранение.** Сообщите пользователю, что он должен установить браузер (желательно еще с поддержкой настраиваемой вкладки) на своем устройстве.

## <a name="devices-and-browsers-tested"></a>Тестируемые устройства и браузеры
В следующей таблице перечислены проверенные устройства и браузеры.

| | браузер&ast;     |  Результат  | 
| ------------- |:-------------:|:-----:|
| Huawei/один + | Chrome&ast; | Этап|
| Huawei/один + | Угловой&ast; | Этап|
| Huawei/один + | Браузера&ast; | Этап|
| Huawei/один + | Дивный&ast; | Этап|
| Один + | екосиа&ast; | Этап|
| Один + | Киви&ast; | Этап|
| Huawei/один + | Opera | Этап|
| Huawei | операмини | Этап|
| Huawei/один + | Браузер | Этап|
| Один + | макссон | Этап|
| Huawei/один + | дуккдуккго | Проверка подлинности отменена пользователем|
| Huawei/один + | Браузер UC | Проверка подлинности отменена пользователем|
| Один + | долфин | Проверка подлинности отменена пользователем|
| Один + | Браузер CM | Проверка подлинности отменена пользователем|
| Huawei/один + | нет установленных | Андроидактивитинотфаунд, пример|

&ast;Поддерживает пользовательские вкладки

## <a name="next-steps"></a>Следующие шаги
Фрагменты кода и дополнительные сведения об использовании системного браузера с Xamarin Android см. в этом [руководству](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/wiki/MSAL.NET-uses-web-browser#choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid).  