---
title: Когнитивный навык распознавания текста
titleSuffix: Azure Cognitive Search
description: Извлеките текст из файлов изображений с помощью оптического распознавания символов (OCR) в конвейере обогащения в Azure Когнитивный поиск.
manager: nitinme
author: luiscabrer
ms.author: luisca
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: bdb510113a8d65ac04b54e77158f46d03cccd9de
ms.sourcegitcommit: b050c7e5133badd131e46cab144dd5860ae8a98e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72791927"
---
# <a name="ocr-cognitive-skill"></a>Когнитивный навык распознавания текста

Уровень опыта **распознавания текста (OCR)** распознает печатный и рукописный текст в файлах изображений. Этот навык использует модели машинного обучения, предоставляемые [API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) в Cognitive Services. Навык **OCR** сопоставляет следующие функциональные возможности службы.

+ API [OCR](../cognitive-services/computer-vision/concept-recognizing-text.md#ocr-optical-character-recognition-api) используется для языков, отличных от английского. 
+ Для английского языка используется новый API [Read](../cognitive-services/computer-vision/concept-recognizing-text.md#read-api) .

Навык **OCR** извлекает текст из файлов изображений. Поддерживаемые форматы файлов включают:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ . -

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Расходы начисляются при вызове API в Cognitive Services, а также для извлечения изображений в рамках этапа взлома документов в Azure Когнитивный поиск. За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Цены на извлечение изображений описаны на [странице цен на когнитивный Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).


## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | Описание |
|--------------------|-------------|
| detectOrientation | Включает автоопределение ориентации изображения. <br/> Допустимые значения: true или false.|
|defaultLanguageCode | <p>  Код языка вводимого текста. Поддерживаемые языки включают в себя: <br/> zh-Hans (китайский, упрощенное письмо) <br/> zh-Hant (китайский, традиционное письмо) <br/>CS (чешский) <br/>da (датский) <br/>nl (нидерландский) <br/>en (английский) <br/>fi (финский)  <br/>fr (французский) <br/>  de (немецкий) <br/>el (греческий) <br/> hu (венгерский) <br/> it (итальянский) <br/>  ja (японский) <br/> ko (корейский) <br/> nb (норвежский) <br/>   pl (польский) <br/> pt (португальский) <br/>  ru (русский) <br/>  es (испанский) <br/>  sv (шведский) <br/>  tr (турецкий) <br/> ar (арабский) <br/> ro (румынский) <br/> sr-Cyrl (сербский, кириллица) <br/> sr-Latn (сербский, латиница) <br/>  sk (словацкий) <br/>  unk (неизвестно) <br/><br/> Если код языка не указан или для него задано значение NULL, будет использоваться английский язык. Если языку явным образом задано значение unk, язык будет определяться автоматически. </p> |
|лининдинг | Значение, используемое для каждой обнаруженной строки. Возможные значения: "Space", "Карриажеретурн", "перевод строки".  Значение по умолчанию — "Space" |

Ранее был параметр с именем «Текстекстрактионалгорисм», определяющий, должен ли навык извлекать текст «печатный» или «рукописный».  Этот параметр является устаревшим и больше не нужен, так как последний алгоритм API чтения способен одновременно извлечь оба типа текста.  Если определение навыка уже включает этот параметр, его не нужно удалять, но он больше не будет использоваться, и оба типа текста будут извлечены перенаправлены, независимо от того, что оно имеет значение.

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | Описание                                          |
|---------------|------------------------------------------------------|
| image         | Сложный тип. Сейчас работает только с полем /document/normalized_images, созданным индексатором большого двоичного объекта Azure, если для ```imageAction``` установлено значение, отличное от ```none```. Дополнительные сведения см. в [этом примере](#sample-output).|


## <a name="skill-outputs"></a>Выходные данные навыка
| Имя вывода     | Описание                   |
|---------------|-------------------------------|
| текст          | Обычный текст, извлеченный из изображения.   |
| layoutText    | Сложный тип, описывающий извлеченный текст и расположение, где найден указанный текст.|


## <a name="sample-definition"></a>Пример определения

```json
{
  "skills": [
    {
      "description": "Extracts text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": null,
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text",
          "targetName": "myText"
        },
        {
          "name": "layoutText",
          "targetName": "myLayoutText"
        }
      ]
    }
  ]
}
```
<a name="sample-output"></a>

## <a name="sample-text-and-layouttext-output"></a>Пример вывода текста и layoutText

```json
{
  "text": "Hello World. -John",
  "layoutText":
  {
    "language" : "en",
    "text" : "Hello World. -John",
    "lines" : [
      {
        "boundingBox":
        [ {"x":10, "y":10}, {"x":50, "y":10}, {"x":50, "y":30},{"x":10, "y":30}],
        "text":"Hello World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ],
    "words": [
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"Hello"
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"World."
      },
      {
        "boundingBox": [ {"x":110, "y":10}, {"x":150, "y":10}, {"x":150, "y":30},{"x":110, "y":30}],
        "text":"-John"
      }
    ]
  }
}
```

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Пример. Слияние текста, извлеченного из внедренных изображений с содержимым документа.

Распространенным вариантом использования текстового слияния является возможность объединить текстовое представление изображений (текст из навыка OCR или заголовок изображения) с полем содержимого документа.

В следующем примере набора навыков создается поле *merged_text*. Это поле содержит текстовое содержимое вашего документа и текст OCRed из каждого изображения, встроенного в этот документ.

#### <a name="request-body-syntax"></a>Синтаксис текста запроса
```json
{
  "description": "Extract text from images and merge with content text to produce merged_text",
  "skills":
  [
    {
      "description": "Extract text (plain and structured) from image.",
      "@odata.type": "#Microsoft.Skills.Vision.OcrSkill",
      "context": "/document/normalized_images/*",
      "defaultLanguageCode": "en",
      "detectOrientation": true,
      "inputs": [
        {
          "name": "image",
          "source": "/document/normalized_images/*"
        }
      ],
      "outputs": [
        {
          "name": "text"
        }
      ]
    },
    {
      "@odata.type": "#Microsoft.Skills.Text.MergeSkill",
      "description": "Create merged_text, which includes all the textual representation of each image inserted at the right location in the content field.",
      "context": "/document",
      "insertPreTag": " ",
      "insertPostTag": " ",
      "inputs": [
        {
          "name":"text", "source": "/document/content"
        },
        {
          "name": "itemsToInsert", "source": "/document/normalized_images/*/text"
        },
        {
          "name":"offsets", "source": "/document/normalized_images/*/contentOffset"
        }
      ],
      "outputs": [
        {
          "name": "mergedText", "targetName" : "merged_text"
        }
      ]
    }
  ]
}
```
В приведенном выше примере набора навыков предполагается наличие поля нормализованных изображений. Чтобы создать это поле, настройте конфигурацию *imageAction* в определении индексатора *generateNormalizedImages*, как показано ниже.

```json
{
  //...rest of your indexer definition goes here ...
  "parameters": {
    "configuration": {
      "dataToExtract":"contentAndMetadata",
      "imageAction":"generateNormalizedImages"
    }
  }
}
```

## <a name="see-also"></a>Дополнительные материалы
+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Text Merge cognitive skill](cognitive-search-skill-textmerger.md) (Когнитивный навык слияния текста)
+ [How to define a skillset](cognitive-search-defining-skillset.md) (Определение набора навыков)
+ [Создание индексатора (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
