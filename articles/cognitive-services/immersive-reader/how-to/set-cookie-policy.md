---
title: Настройка политики cookie для иммерсивного чтения
titleSuffix: Azure Cognitive Services
description: В этой статье показано, как задать политику cookie для иммерсивного модуля чтения.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: eb31fa271496b0eeedf4a2b845ab05e5f241e167
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86041988"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Настройка политики cookie для иммерсивное средство чтения

По умолчанию иммерсивное средство чтения отключит использование файлов cookie. При включении использования файлов cookie иммерсивное средство чтения может использовать файлы cookie для сохранения настроек пользователей и отслеживания использования компонентов. При включении использования файлов cookie в иммерсивное средство чтения следует учитывать требования политики соответствия файлов cookie в ЕС. Ведущее приложение отвечает за получение любого необходимого согласия пользователя в соответствии с политикой соответствия файлов cookie ЕС.

Политику файлов cookie можно задать с помощью [параметров](../reference.md#options)иммерсивное средство чтения.

## <a name="enable-cookie-usage"></a>Включить использование файлов cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Отключить использование файлов cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Дальнейшие шаги

* Ознакомьтесь с [кратким руководством для разработчиков Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Node.js
* Ознакомьтесь с [руководством по Android](../tutorial-android.md) , чтобы узнать, что еще можно сделать с помощью пакета SDK для иммерсивное средство чтения с использованием Java или Котлин для Android.
* Ознакомьтесь с [руководством по iOS](../tutorial-ios.md) , чтобы узнать, что еще можно сделать с помощью пакета SDK для иммерсивное средство чтения, используя SWIFT для iOS.
* Ознакомьтесь с [руководством для разработчиков Python](../tutorial-python.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Python.
* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](../reference.md).