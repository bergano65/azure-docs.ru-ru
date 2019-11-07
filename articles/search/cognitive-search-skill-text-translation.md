---
title: Неприятный навык преобразования текста (Предварительная версия)
titleSuffix: Azure Cognitive Search
description: Вычисляет текст и для каждой записи возвращает текст, переведенный на указанный целевой язык в конвейере обогащения AI в Azure Когнитивный поиск. Этот навык в настоящее время доступен в общедоступной предварительной версии.
manager: nitinme
author: careyjmac
ms.author: chalton
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 7c42c9033fac057c12426726a96ae6079f3080da
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73715411"
---
#   <a name="text-translation-cognitive-skill"></a>Неприятный навык преобразования текста

> [!IMPORTANT] 
> Этот навык в настоящее время доступен в общедоступной предварительной версии. Функции предварительной версии предоставляются без соглашения об уровне обслуживания и не рекомендуются для рабочих нагрузок. Дополнительные сведения см. в статье [Дополнительные условия использования предварительных выпусков Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). [REST API версия 2019-05-06-Preview](search-api-preview.md) предоставляет функции предварительной версии. В настоящее время поддерживается ограниченная поддержка портала и поддержка пакета SDK для .NET.

Навык **преобразования текста** оценивает текст, а для каждой записи возвращает текст, преобразованный на указанный целевой язык. Этот навык использует [API перевода текстов v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) , доступный в Cognitive Services.

Эта возможность полезна, если вы ожидаете, что документы могут находиться не на одном языке. в этом случае можно нормализовать текст на один язык перед индексированием для поиска путем его перевода.  Он также полезен для вариантов использования при локализации, где может потребоваться использовать копии одного и того же текста на нескольких языках.

[API перевода текстов v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) — это нерегиональная служба, которая означает, что ваши данные не всегда находятся в том же регионе, что и когнитивный Поиск Azure или подключенный Cognitive Services ресурс.

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Расходы начисляются при вызове API в Cognitive Services, а также для извлечения изображений в рамках этапа взлома документов в Azure Когнитивный поиск. За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Цены на извлечение изображений описаны на [странице цен на когнитивный Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. Транслатионскилл

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 50 000 символов, измеряемый [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length). Если необходимо разбить данные перед отправкой в навык перевода текста, рассмотрите возможность использования уровня " [разделение текста](cognitive-search-skill-textsplit.md)".

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Входные данные                | Description (Описание) |
|---------------------|-------------|
| дефаулттолангуажекоде | Необходимости Код языка для перевода документов в документы, не указывающие на язык явным образом. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| дефаултфромлангуажекоде | Используемых Код языка для перевода документов из для документов, которые не указывают язык явным образом.  Если параметр Дефаултфромлангуажекоде не указан, для определения языка используется автоматическое обнаружение языка, предоставляемое API перевода текстов. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | Используемых Код языка, с которого будут переводиться документы, если не указаны ни входные данные Фромлангуажекоде, ни параметр Дефаултфромлангуажекоде, и автоматическое обнаружение языка завершается неудачно.  Если язык Сугжестедфром не указан, в качестве языка Сугжестедфром будет использоваться английский (EN). <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени     | Description (Описание) |
|--------------------|-------------|
| text | Текст для перевода.|
| толангуажекоде    | Строка, указывающая язык, в который должен быть преобразован текст. Если этот вход не указан, Дефаулттолангуажекоде будет использоваться для преобразования текста. <br/>Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).|
| фромлангуажекоде  | Строка, указывающая текущий язык текста. Если этот параметр не указан, Дефаултфромлангуажекоде (или автоматическое определение языка, если Дефаултфромлангуажекоде не предоставлен) будет использоваться для преобразования текста. <br/>Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода    | Description (Описание) |
|--------------------|-------------|
| TranslatedText | Строковый результат преобразования текста из Транслатедфромлангуажекоде в Транслатедтолангуажекоде.|
| транслатедтолангуажекоде  | Строка, указывающая код языка, в который был преобразован текст. Полезен при переводе на несколько языков и необходимости в отслеживании текста на каком языке.|
| транслатедфромлангуажекоде    | Строка, указывающая код языка, из которого был преобразован текст. Полезно, если вы выбрали параметр автоматического определения языка, так как этот результат позволит получить результат этого обнаружения.|

##  <a name="sample-definition"></a>Пример определения

```json
 {
    "@odata.type": "#Microsoft.Skills.Text.TranslationSkill",
    "defaultToLanguageCode": "fr",
    "suggestedFrom": "en",
    "context": "/document",
    "inputs": [
      {
        "name": "text",
        "source": "/document/text"
      }
    ],
    "outputs": [
      {
        "name": "translatedText",
        "targetName": "translatedText"
      },
      {
        "name": "translatedFromLanguageCode",
        "targetName": "translatedFromLanguageCode"
      },
      {
        "name": "translatedToLanguageCode",
        "targetName": "translatedToLanguageCode"
      }
    ]
  }
```

##  <a name="sample-input"></a>Пример ввода

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "text": "We hold these truths to be self-evident, that all men are created equal."
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "text": "Estamos muy felices de estar con ustedes."
        }
    }
  ]
}
```


##  <a name="sample-output"></a>Пример выходных данных

```json
{
  "values": [
    {
      "recordId": "1",
      "data":
        {
          "translatedText": "Nous tenons ces vérités pour évidentes, que tous les hommes sont créés égaux.",
          "translatedFromLanguageCode": "en",
          "translatedToLanguageCode": "fr"
        }
    },
    {
      "recordId": "2",
      "data":
        {
          "translatedText": "Nous sommes très heureux d'être avec vous.",
          "translatedFromLanguageCode": "es",
          "translatedToLanguageCode": "fr"
        }
    }
  ]
}
```


## <a name="errors-and-warnings"></a>Ошибки и предупреждения
Если указан неподдерживаемый код языка для языка с или на, создается ошибка и текст не переводится.
Если текст пуст, появится предупреждение.
Если длина текста превышает 50 000 символов, будут переведены только первые 50 000 символов, и будет выдано предупреждение.

## <a name="see-also"></a>Дополнительные материалы

+ [Встроенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
