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
ms.openlocfilehash: 876379a211038933cf665bd1a4a4daae9c9b2787
ms.sourcegitcommit: 309cf6876d906425a0d6f72deceb9ecd231d387c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/01/2020
ms.locfileid: "84267075"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Настройка политики cookie для иммерсивное средство чтения

По умолчанию иммерсивное средство чтения отключит использование файлов cookie. При включении использования файлов cookie иммерсивное средство чтения может использовать файлы cookie для сохранения настроек пользователей и отслеживания использования компонентов. При включении использования файлов cookie в иммерсивное средство чтения следует учитывать требования политики соответствия файлов cookie в ЕС. Ведущее приложение отвечает за получение любого необходимого согласия пользователя в соответствии с политикой соответствия файлов cookie ЕС.

Политику файлов cookie можно задать с помощью [параметров](../reference.md#options)иммерсивное средство чтения. Дополнительные сведения см. в разделе [кукиеполици enum](../reference.md#cookiepolicy-enum) .

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

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь с [кратким руководством для разработчиков Node.js](../quickstarts/client-libraries.md?pivots=programming-language-nodejs), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Node.js
* Ознакомьтесь с [руководством для разработчиков Python](../tutorial-python.md), чтобы узнать другие возможности пакета SDK иммерсивного средства чтения при использовании Python.
* Просмотрите [учебник по SWIFT](../tutorial-ios-picture-immersive-reader.md) , чтобы узнать, что еще можно сделать с помощью пакета SDK для иммерсивное средство чтения, используя SWIFT.
* Ознакомьтесь с разделом о [пакете SDK для иммерсивного средства чтения](https://github.com/microsoft/immersive-reader-sdk) и [справочнике по этому пакету](../reference.md).