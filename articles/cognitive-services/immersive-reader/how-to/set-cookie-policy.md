---
title: Установите политику погружения в cookie-файлы Reader
titleSuffix: Azure Cognitive Services
description: В этой статье будет показана, как настроить политику cookie для Immersive Reader.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75946114"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Как настроить политику cookie для immersive Reader

Immersive Reader отсватим использование файлов cookie по умолчанию. Если вы включите использование файлов cookie, то Immersive Reader может использовать файлы cookie для поддержания пользовательских предпочтений и отслеживания использования функций. Если вы включите использование файлов cookie в Immersive Reader, пожалуйста, рассмотрите требования политики ЕС по соблюдению требований к cookie. Приложение хоста обязано получить любое необходимое согласие пользователя в соответствии с Политикой соответствия требованиям ЕС Cookie.

Политика cookie может быть установлена с помощью [вариантов](../reference.md#options)Immersive Reader. Дополнительную информацию [можно узнать в перечне CookiePolicy.](../reference.md#cookiepolicy-enum)

## <a name="enable-cookie-usage"></a>Включить использование cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Использование файлов cookie-файлов

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [кратким руководством для разработчиков Node.js](../quickstart-nodejs.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Node.js
* Ознакомьтесь с [руководством для разработчиков Python](../tutorial-python.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Python.
* Посмотреть [учебник Swift,](../tutorial-ios-picture-immersive-reader.md) чтобы узнать, что еще вы можете сделать с Immersive Reader SDK с помощью Swift
* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](../reference.md).