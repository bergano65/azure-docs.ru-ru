---
title: Навык поиска с переводом текста — Поиск Azure
description: Вычисляет текст и для каждой записи возвращает текст, переведенный на указанный целевой язык в конвейере обогащения службы поиска Azure.
services: search
manager: nitinme
author: careyjmac
ms.service: search
ms.workload: search
ms.topic: conceptual
ms.date: 06/25/2019
ms.author: chalton
ms.openlocfilehash: ddfb35cbfcfbc262f3eff0de67f5cedfc31ea27e
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71265709"
---
#   <a name="text-translation-cognitive-skill"></a>Неприятный навык преобразования текста

Навык **преобразования текста** оценивает текст, а для каждой записи возвращает текст, преобразованный на указанный целевой язык. Этот навык использует [API перевода текстов v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-translate) , доступный в Cognitive Services.

Эта возможность полезна, если вы ожидаете, что документы могут находиться не на одном языке. в этом случае можно нормализовать текст на один язык перед индексированием для поиска путем его перевода.  Он также полезен для вариантов использования при локализации, где может потребоваться использовать копии одного и того же текста на нескольких языках.

[API перевода текстов v 3.0](https://docs.microsoft.com/azure/cognitive-services/translator/reference/v3-0-reference) — это нерегиональная служба, которая означает, что ваши данные не всегда находятся в том же регионе, что и поиск Azure или подключенный Cognitive Services ресурс.

> [!NOTE]
> По мере расширения области путем увеличения частоты обработки и добавления большего количества документов или дополнительных алгоритмов ИИ, вам нужно будет [присоединить оплачиваемый ресурс Cognitive Services](cognitive-search-attach-cognitive-services.md). Плата взимается при вызове API в Cognitive Services и извлечении изображений при открытии документов в службе "Поиск Azure". За извлечение текста из документов плата не взимается.
>
> Плата за выполнение встроенных навыков взимается в рамках существующей [модели оплаты Cognitive Services по мере использования](https://azure.microsoft.com/pricing/details/cognitive-services/). Плата за извлечение изображений указана на [странице с ценами на Поиск Azure](https://go.microsoft.com/fwlink/?linkid=2042400).

## <a name="odatatype"></a>@odata.type  
Microsoft. Skills. Text. Транслатионскилл

## <a name="data-limits"></a>Ограничения данных
Максимальный размер записи должен составлять 50 000 символов, [`String.Length`](https://docs.microsoft.com/dotnet/api/system.string.length)измеряемый. Если необходимо разбить данные перед отправкой в навык перевода текста, рассмотрите возможность использования уровня " [разделение текста](cognitive-search-skill-textsplit.md)".

## <a name="skill-parameters"></a>Параметры навыков

Параметры зависят от регистра.

| Входные данные                | Описание |
|---------------------|-------------|
| дефаулттолангуажекоде | Необходимости Код языка для перевода документов в документы, не указывающие на язык явным образом. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| дефаултфромлангуажекоде | Используемых Код языка для перевода документов из для документов, которые не указывают язык явным образом.  Если параметр Дефаултфромлангуажекоде не указан, для определения языка используется автоматическое обнаружение языка, предоставляемое API перевода текстов. <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |
| suggestedFrom | Используемых Код языка, с которого будут переводиться документы, если не указаны ни входные данные Фромлангуажекоде, ни параметр Дефаултфромлангуажекоде, и автоматическое обнаружение языка завершается неудачно.  Если язык Сугжестедфром не указан, в качестве языка Сугжестедфром будет использоваться английский (EN). <br/> Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support). |

## <a name="skill-inputs"></a>Входные данные навыков

| Ввод имени     | Описание |
|--------------------|-------------|
| text | Текст для перевода.|
| толангуажекоде    | Строка, указывающая язык, в который должен быть преобразован текст. Если этот вход не указан, Дефаулттолангуажекоде будет использоваться для преобразования текста. <br/>Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).|
| фромлангуажекоде  | Строка, указывающая текущий язык текста. Если этот параметр не указан, Дефаултфромлангуажекоде (или автоматическое определение языка, если Дефаултфромлангуажекоде не предоставлен) будет использоваться для преобразования текста. <br/>Ознакомьтесь с [полным списком поддерживаемых языков](https://docs.microsoft.com/azure/cognitive-services/translator/language-support).|

## <a name="skill-outputs"></a>Выходные данные навыка

| Имя вывода    | Описание |
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


##  <a name="sample-output"></a>Пример полученных результатов

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

## <a name="see-also"></a>См. также

+ [Предопределенные навыки](cognitive-search-predefined-skills.md)
+ [Определение набора навыков](cognitive-search-defining-skillset.md)
