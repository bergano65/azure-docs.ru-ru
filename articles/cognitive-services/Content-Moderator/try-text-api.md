---
title: Модерация текстового содержимого с использованием API модерации текста — Content Moderator
titlesuffix: Azure Cognitive Services
description: Опробуйте модерацию текста с помощью API модерации текста в веб-консоли.
services: cognitive-services
author: sanjeev3
ms.author: sajagtap
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: a3eb134d655f2a25acb45e0d249aa421667d1520
ms.sourcegitcommit: 6a42dd4b746f3e6de69f7ad0107cc7ad654e39ae
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/07/2019
ms.locfileid: "67621385"
---
# <a name="moderate-text-from-the-api-console"></a>Модерация текста с помощью консоли API

Используйте [API модерации текста](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) в Azure Content Moderator проверять содержимое текста для ненормативной лексики и сопоставить ее с пользовательским и общим списки.

## <a name="get-your-api-key"></a>Получение ключа API

Прежде чем можно будет опробовать API в веб-консоли, необходимо получить ключ подписки. Он указан на вкладке **Settings** (Параметры) в поле **Ocp-Apim-Subscription-Key**. Дополнительные сведения см. в этом [обзоре](overview.md).

## <a name="navigate-to-the-api-reference"></a>Переход к справочнику по API

Перейдите к [справочнику по API модерации текста](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f). 

  Откроется страница **Text - Screen** (Текст — проверка).

## <a name="open-the-api-console"></a>Открытие консоли API

Для параметра **Open API testing console** (Открытая тестовая консоль API) выберите регион, лучше всего подходящий для вашего расположения. 

  ![Выбор региона на странице "Text - Screen" (Текст — проверка)](images/test-drive-region.png)

  Откроется консоль API **Text - Screen** (Текст — проверка).

## <a name="select-the-inputs"></a>Выбор входных данных

### <a name="parameters"></a>Параметры

Выберите параметры запроса, которые требуется использовать при проверке текста. В этом примере используется значение по умолчанию для параметра **language** (Язык). Можно также оставить его пустым, так как эта операция автоматически определяет предполагаемый язык в ходе выполнения.

> [!NOTE]
> Для параметра **language** (Язык) задайте значение `eng` или оставьте это поле пустым, чтобы увидеть в ответе результат машинной **классификации**, которая сейчас выполняется в режиме предварительной версии. **Эта функция поддерживает только английский язык**.
>
> Для обнаружения **нецензурных терминов** укажите [код ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) для поддерживаемых языков, которые перечислены в этой статье, или оставьте это поле пустым.

Для параметров **autocorrect** (Автозамена), **PII** (Личные сведения) и **classify (preview)** (Классификация (предварительная версия)) выберите значение **true** (Истина). Оставьте поле **ListId** (ИД списка) пустым.

  ![Параметры запроса в консоли "Text - Screen" (Текст — проверка)](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Тип содержимого

Для параметра **Content-Type** (Тип содержимого) выберите тип проверяемого содержимого. В этом примере используйте тип содержимого по умолчанию, **text/plain**. В поле **Ocp-Apim-Subscription-Key** введите свой ключ подписки.

### <a name="sample-text-to-scan"></a>Пример текста для проверки

В поле **Request body** (Текст запроса) введите какой-либо текст. В следующем примере показана преднамеренная опечатка в тексте.

> [!NOTE]
> Недопустимый номер социального страхования в следующем примере текста указан намеренно. Цель — продемонстрировать пример формата входных и выходных данных.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.
Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Анализ ответа

Приведенный ниже ответ показывает различные важные сведения из API. Он содержит ненормативную лексику, персональные данные, классификации (Предварительная версия) и версии исправлены автоматически.

> [!NOTE]
> Функция машинной классификации находится в режиме предварительной версии и поддерживает только английский язык.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.\r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300.\r\nAlso, 544-56-7788 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. \r\nThese are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. \r\nAlso, 544- 56- 7788 looks like a social security number ( SSN) .",
"Misrepresentation":null,
"PII":{  
  "Email":[  
    {  
      "Detected":"abcdef@abcd.com",
      "SubType":"Regular",
      "Text":"abcdef@abcd.com",
      "Index":32
    }
  ],
  "IPA":[  
    {  
      "SubType":"IPV4",
      "Text":"255.255.255.255",
      "Index":72
    }
  ],
  "Phone":[  
    {  
      "CountryCode":"US",
      "Text":"6657789887",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"870 608 4000",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 870 608 4000",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0344 800 2400",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0800 820 3300",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1 Microsoft Way, Redmond, WA 98052",
      "Index":89
    }
  ],
  "SSN":[  
    {  
      "Text":"999999999",
      "Index":56
    },
    {  
      "Text":"999-99-9999",
      "Index":266
    }
  ]
},
"Classification":{  
  "ReviewRecommended":true,
  "Category1":{  
    "Score":1.5113095059859916E-06
  },
  "Category2":{  
    "Score":0.12747249007225037
  },
  "Category3":{  
    "Score":0.98799997568130493
  }
},
"Language":"eng",
"Terms":[  
  {  
    "Index":21,
    "OriginalIndex":21,
    "ListId":0,
    "Term":"crap"
  }
],
"Status":{  
  "Code":3000,
  "Description":"OK",
  "Exception":null
},
"TrackingId":"2eaa012f-1604-4e36-a8d7-cc34b14ebcb4"
}
```

Подробное описание всех разделов в ответе JSON, см. в разделе [Модерация текста](text-moderation-api.md) концептуальное руководство по.

## <a name="next-steps"></a>Дальнейшие действия

Используйте REST API в коде или начинаться с [краткого руководства по .NET для модерации текста](text-moderation-quickstart-dotnet.md) для интеграции с приложением.
