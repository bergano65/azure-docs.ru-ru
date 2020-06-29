---
title: Сохранить настройки пользователя
titleSuffix: Azure Cognitive Services
description: В этой статье будет показано, как сохранить предпочтения пользователя.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: beb053551dc1fa28672c488b31dfb29ca3b53651
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486925"
---
# <a name="how-to-store-user-preferences"></a>Сохранение настроек пользователя

В этой статье показано, как сохранить предпочтения пользователя. Эта функция является альтернативным средством хранения предпочтений пользователя в случае, когда использование файлов cookie нежелательно или нецелесообразно.

## <a name="how-to-enable-storing-user-preferences"></a>Как включить сохранение настроек пользователя

`options`Параметр содержит `onPreferencesChanged` обратный вызов. Эта функция будет вызываться каждый раз, когда пользователь изменяет свои настройки (например, изменяют размер текста, цвет темы, шрифт и т. д.). `value`Параметр содержит строку, которая представляет текущие предпочтения пользователя. Эту строку можно сохранить с помощью любого предпочтительного механизма.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Загрузка пользовательских настроек в иммерсивное средство чтения

Передайте предпочтения пользователя в иммерсивное средство чтения с помощью `preferences` параметра. Упрощенный пример хранения и загрузки настроек пользователя выглядит следующим образом:

```typescript
let userPreferences = null;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
    }
};
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со справочной документацией по [пакету SDK для иммерсивного средства чтения](./reference.md).