---
title: Настройка перевода
titleSuffix: Azure Cognitive Services
description: В этой статье мы покажем, как настроить различные параметры для перевода.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 06/29/2020
ms.author: metan
ms.openlocfilehash: ea066fab713100309103a040d309bac5b984fb99
ms.sourcegitcommit: 1d9f7368fa3dadedcc133e175e5a4ede003a8413
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/27/2020
ms.locfileid: "85486937"
---
# <a name="how-to-configure-translation"></a>Настройка перевода

В этой статье показано, как настроить различные параметры для перевода в иммерсивное средство чтения.

## <a name="configure-translation-language"></a>Настройка языка перевода

`options`Параметр содержит все флаги, которые можно использовать для настройки перевода. Задайте для `language` параметра язык, который нужно преобразовать. Полный список поддерживаемых языков см. в разделе [Поддержка языков](./language-support.md) .

```typescript
const options = {
    translationOptions: {
        language: 'fr-FR'
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="automatically-translate-the-document-on-load"></a>Автоматически преобразовывать документ при загрузке

Задайте `autoEnableDocumentTranslation` значение `true` , чтобы разрешить автоматическое преобразование всего документа при загрузке иммерсивное средство чтения.

```typescript
const options = {
    translationOptions: {
        autoEnableDocumentTranslation: true
    }
};
```

## <a name="automatically-enable-word-translation"></a>Автоматическое включение перевода слов

Установите значение, чтобы `autoEnableWordTranslation` `true` включить преобразование одного слова.

```typescript
const options = {
    translationOptions: {
        autoEnableWordTranslation: true
    }
};
```

## <a name="next-steps"></a>Дальнейшие действия

* Ознакомьтесь со справочной документацией по [пакету SDK для иммерсивного средства чтения](./reference.md).