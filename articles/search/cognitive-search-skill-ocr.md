---
title: 'Навык когнитивного поиска: распознавание текста — служба "Поиск Azure"'
description: Сведения об извлечении текста из файлов изображений путем распознавания текста (OCR) в конвейере обогащения службы "Поиск Azure".
services: search
manager: pablocas
author: luiscabrer
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.date: 01/17/2019
ms.author: luisca
ms.custom: seodec2018
ms.openlocfilehash: fb7fe0689ce54031880565c0c6409afeab2ff523
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57777897"
---
# <a name="ocr-cognitive-skill"></a>Когнитивный навык распознавания текста

Навык оптического распознавания символов (OCR) распознает печатный и рукописный текст в файлах изображений. Этот навык использует модели машинного обучения, предоставляемые [API компьютерного зрения](https://docs.microsoft.com/azure/cognitive-services/computer-vision/home) в Cognitive Services. Навык **OCR** сопоставляет следующие функциональные возможности службы.

+ Если для textExtractionAlgorithm указано значение "handwritten", используется функция ["RecognizeText"](../cognitive-services/computer-vision/quickstarts-sdk/csharp-hand-text-sdk.md).
+ Если для textExtractionAlgorithm указано значение "printed", для других языков (кроме английского) используется функция ["OCR"](../cognitive-services/computer-vision/concept-extracting-text-ocr.md). Для английского языка используется новая функция [Распознавание текста](../cognitive-services/computer-vision/concept-recognizing-text.md) для печатного текста.

Навык **OCR** извлекает текст из файлов изображений. Поддерживаемые форматы файлов включают:

+ .JPEG
+ .JPG
+ .PNG
+ .BMP
+ .GIF
+ . TIFF

> [!NOTE]
> Начиная с 21 декабря 2018 г. можно [связывать ресурсы Cognitive Services](cognitive-search-attach-cognitive-services.md) с набором навыков службы "Поиск Azure". Это позволяет нам взимать плату за выполнение набора навыков. С этого момента мы также начали начислять плату за извлечение изображений при открытии документов. Извлечение текста из документов будет выполняться бесплатно, как и прежде.
>
> За операции с применением [встроенных навыков](cognitive-search-predefined-skills.md) взимается [плата по мере использования по тарифам для служб Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services), как если бы вы выполнили эту задачу непосредственно. Плата за извлечение изображения взимается по тарифам службы "Поиск Azure", которая сейчас предлагается по цене предварительной версии. Дополнительные сведения см. на странице [Цены на Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400) и в разделе [Как работает выставление счетов](search-sku-tier.md#how-billing-works).

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Имя параметра     | ОПИСАНИЕ |
|--------------------|-------------|
| detectOrientation | Включает автоопределение ориентации изображения. <br/> Допустимые значения: true или false.|
|defaultLanguageCode | <p>  Код языка вводимого текста. Поддерживаемые языки включают в себя: <br/> zh-Hans (китайский, упрощенное письмо) <br/> zh-Hant (китайский, традиционное письмо) <br/>CS (чешский) <br/>da (датский) <br/>nl (нидерландский) <br/>en (английский) <br/>fi (финский)  <br/>fr (французский) <br/>  de (немецкий) <br/>el (греческий) <br/> hu (венгерский) <br/> it (итальянский) <br/>  ja (японский) <br/> ko (корейский) <br/> nb (норвежский) <br/>   pl (польский) <br/> pt (португальский) <br/>  ru (русский) <br/>  es (испанский) <br/>  sv (шведский) <br/>  tr (турецкий) <br/> ar (арабский) <br/> ro (румынский) <br/> sr-Cyrl (сербский, кириллица) <br/> sr-Latn (сербский, латиница) <br/>  sk (словацкий) <br/>  unk (неизвестно) <br/><br/> Если код языка не указан или для него задано значение NULL, будет использоваться английский язык. Если языку явным образом задано значение unk, язык будет определяться автоматически. </p> |
| textExtractionAlgorithm | printed или handwritten. Алгоритм распознавания текста handwritten в настоящее время находится в режиме предварительной версии и поддерживается только на английском языке. |

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени      | ОПИСАНИЕ                                          |
|---------------|------------------------------------------------------|
| изображение         | Сложный тип. Сейчас работает только с полем /document/normalized_images, созданным индексатором большого двоичного объекта Azure, если для ```imageAction``` установлено значение, отличное от ```none```. Дополнительные сведения см. в [этом примере](#sample-output).|


## <a name="skill-outputs"></a>Выходные данные навыка
| Имя вывода     | ОПИСАНИЕ                   |
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

## <a name="sample-merging-text-extracted-from-embedded-images-with-the-content-of-the-document"></a>Пример: Слияние текста, извлеченного из внедренных изображений с содержимым документа.

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

## <a name="see-also"></a>См. также
+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Text Merge cognitive skill](cognitive-search-skill-textmerger.md) (Когнитивный навык слияния текста)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
+ [Создание индексатора (REST)](https://docs.microsoft.com/rest/api/searchservice/create-indexer)
