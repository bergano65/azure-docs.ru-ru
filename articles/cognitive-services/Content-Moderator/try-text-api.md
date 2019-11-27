---
title: Модерация текстового содержимого с использованием API модерации текста — Content Moderator
titleSuffix: Azure Cognitive Services
description: Опробуйте модерацию текста с помощью API модерации текста в веб-консоли.
services: cognitive-services
author: PatrickFarley
ms.author: pafarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: conceptual
ms.date: 05/29/2019
ms.openlocfilehash: e0930558f31b27a77fa2cd6b44fcea2fe9091086
ms.sourcegitcommit: 36eb583994af0f25a04df29573ee44fbe13bd06e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/26/2019
ms.locfileid: "74538821"
---
# <a name="moderate-text-from-the-api-console"></a>Модерация текста с помощью консоли API

Используйте [API-интерфейс](https://westus.dev.cognitive.microsoft.com/docs/services/57cf753a3f9b070c105bd2c1/operations/57cf753a3f9b070868a1f66f) для контроля текста в Azure Content Moderator, чтобы проверить текстовое содержимое на ненормативную лексику и сравнить его с настраиваемыми и общими списками.

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

### <a name="parameters"></a>parameters

Выберите параметры запроса, которые требуется использовать при проверке текста. В этом примере используется значение по умолчанию для параметра **language** (Язык). Можно также оставить его пустым, так как эта операция автоматически определяет предполагаемый язык в ходе выполнения.

> [!NOTE]
> Для параметра **language** (язык) задайте значение `eng` или оставьте это поле пустым, чтобы в ответе отобразился результат **classification** (классификация), которая сейчас выполняется в режиме предварительной версии. **Эта функция поддерживает только английский язык**.
>
> Для обнаружения **ненормативной лексики** укажите [код ISO 639-3](http://www-01.sil.org/iso639-3/codes.asp) для поддерживаемых языков, которые перечислены в этой статье, или оставьте это поле пустым.

Для параметров **autocorrect** (Автозамена), **PII** (Личные сведения) и **classify (preview)** (Классификация (предварительная версия)) выберите значение **true** (Истина). Оставьте поле **ListId** (ИД списка) пустым.

  ![Параметры запроса в консоли "Text - Screen" (Текст — проверка)](images/text-api-console-inputs.PNG)

### <a name="content-type"></a>Тип содержимого

Для параметра **Content-Type** (Тип содержимого) выберите тип проверяемого содержимого. В этом примере используйте тип содержимого по умолчанию, **text/plain**. В поле **Ocp-Apim-Subscription-Key** введите свой ключ подписки.

### <a name="sample-text-to-scan"></a>Пример текста для проверки

В поле **Request body** (Текст запроса) введите какой-либо текст. В следующем примере показана преднамеренная опечатка в тексте.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

## <a name="analyze-the-response"></a>Анализ ответа

Приведенный ниже ответ показывает различные важные сведения из API. Она содержит потенциально ненормативную лексику, персональные данные, классификацию (Предварительная версия) и автоматическую исправленную версию.

> [!NOTE]
> Функция машинной классификации находится в режиме предварительной версии и поддерживает только английский язык.

```json
{"OriginalText":"Is this a grabage or crap email abcdef@abcd.com, phone: 4255550111, IP: 255.255.255.255, 1234 Main Boulevard, Panapolis WA 96555.\r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123.\r\nAlso, 999-99-9999 looks like a social security number (SSN).",
"NormalizedText":"Is this a grabage or crap email abcdef@ abcd. com, phone: 4255550111, IP: 255. 255. 255. 255, 1234 Main Boulevard, Panapolis WA 96555. \r\nThese are all UK phone numbers: +44 123 456 7890 or 0234 567 8901 or 0456 789 0123. \r\nAlso, 999- 99- 9999 looks like a social security number ( SSN) .",
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
      "Text":"4255550111",
      "Index":56
    },
    {  
      "CountryCode":"US",
      "Text":"425 555 0111",
      "Index":211
    },
    {  
      "CountryCode":"UK",
      "Text":"+44 123 456 7890",
      "Index":207
    },
    {  
      "CountryCode":"UK",
      "Text":"0234 567 8901",
      "Index":227
    },
    {  
      "CountryCode":"UK",
      "Text":"0456 789 0123",
      "Index":244
    }
  ],
  "Address":[  
    {  
      "Text":"1234 Main Boulevard, Panapolis WA 96555",
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

Подробное описание всех разделов в ответе JSON см. в концептуальном руководстве по поиску [текста](text-moderation-api.md) .

## <a name="next-steps"></a>Дополнительная информация

Используйте REST API в коде или следуйте инструкциям в [кратком руководстве по пакету SDK для .NET](dotnet-sdk-quickstart.md) , чтобы интегрировать его с приложением.
