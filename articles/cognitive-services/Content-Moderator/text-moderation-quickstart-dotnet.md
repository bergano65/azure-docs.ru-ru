---
title: Краткое руководство. Проверка содержимого текста с помощью C# в Content Moderator
titlesuffix: Azure Cognitive Services
description: Как анализировать содержимое текста на наличие различных нежелательных материалов с помощью пакета SDK Content Moderator для .NET
services: cognitive-services
author: sanjeev3
manager: nitinme
ms.service: cognitive-services
ms.subservice: content-moderator
ms.topic: quickstart
ms.date: 02/07/2019
ms.author: sajagtap
ms.openlocfilehash: cc8cbc19f416e6e80d2d0fdfb4862536da7f0ce2
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/15/2019
ms.locfileid: "56312248"
---
# <a name="quickstart-analyze-text-content-for-objectionable-material-in-c"></a>Краткое руководство. Анализ текста для выявления нежелательного содержимого с помощью C# 

В этой статье содержатся сведения и примеры кода, которые помогут вам приступить к работе с [пакетом SDK Content Moderator для .NET](https://www.nuget.org/packages/Microsoft.Azure.CognitiveServices.ContentModerator/). Вы узнаете, как выполнять фильтрацию по терминам и классифицировать содержимое текста с целью ограничения потенциально нежелательного материала.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="prerequisites"></a>Предварительные требования
- Ключ подписки Content Moderator. Следуйте инструкциям в статье [Create a Cognitive Services account](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) (Создание учетной записи Cognitive Services), чтобы получить подписку Content Moderator и свой ключ.
- Любой выпуск [Visual Studio 2015 или 2017](https://www.visualstudio.com/downloads/)

> [!NOTE]
> В этом руководстве используется бесплатная подписка Content Moderator. Чтобы получить сведения о каждом уровне подписки, см. страницу [Цены и ограничения](https://azure.microsoft.com/pricing/details/cognitive-services/content-moderator/).

## <a name="create-the-visual-studio-project"></a>Создание проекта Visual Studio

1. В Visual Studio создайте проект **Консольное приложение (.NET Framework)** и назовите его **TextModeration**. 
1. При наличии других проектов в решении выберите этот в качестве единого запускаемого проекта.
1. Получите необходимые пакеты NuGet. Щелкните правой кнопкой мыши проект в обозревателе решений и выберите **Управление пакетами NuGet**, затем найдите и установите следующие пакеты:
    - `Microsoft.Azure.CognitiveServices.ContentModerator`
    - `Microsoft.Rest.ClientRuntime`
    - `Newtonsoft.Json`

## <a name="add-text-moderation-code"></a>Добавление кода для модерации текста

Затем скопируйте и вставьте код из этого руководства в проект, чтобы реализовать основной сценарий модерации содержимого.

### <a name="include-namespaces"></a>Включение пространства имен

Добавьте следующие инструкции `using` в начало файла *Program.cs*.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=1-8)]

### <a name="create-the-content-moderator-client"></a>Создание клиента Content Moderator

Добавьте следующий фрагмент кода в файл *Program.cs*, чтобы создать поставщик клиента Content Moderator для своей подписки. Добавьте код вместе с классом **Program** в то же пространство имен. Необходимо обновить поля **AzureRegion** и **CMSubscriptionKey** значениями идентификатора региона и ключа подписки.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=54-77)]

### <a name="set-up-input-and-output-targets"></a>Настройка целевых файлов ввода-вывода

Добавьте следующие статические поля в класс **Program** в файле _Program.cs_. Таким образом можно указать файлы для входного содержимого текста и выходного содержимого JSON.

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=15-19)]

Вам нужно будет создать входной файл *TextFile.txt* и изменить его путь соответствующим образом (относительные пути задаются в зависимости от каталога выполнения). Откройте _TextFile.txt_ и добавьте текст для модерации. В этом руководстве используются следующие примеры текста.

```
Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052.
These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 
0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).
```

### <a name="load-the-input-text"></a>Загрузка введенного текста

Добавьте следующий код в метод **Main**. Метод **ScreenText** — это основная операция. Его параметры определяют, какие операции модерации контента будут использоваться. В этом примере метод настроен на:
- обнаружение ненормативной лексики в тексте;
- нормализацию текста и автоматическое исправление опечаток;
- обнаружение персональных данных, например телефонных номеров США и Великобритании, адресов электронной почты и почтовых адресов США;
- использование моделей на основе машинного обучения для классификации текста по трем категориям.

Если вы хотите узнать больше о возможностях отдельных операций, перейдите по ссылке в раздел [Следующие шаги](#next-steps).

[!code-csharp[](~/cognitive-services-content-moderator-samples/documentation-samples/csharp/text-moderation-quickstart-dotnet.cs?range=23-48)]

## <a name="run-the-program"></a>Запуск программы

Программа будет записывать данные строки JSON в файл _TextModerationOutput.txt_. Примеры текста, используемые в этом кратком руководстве, дают следующий результат.

```json
Autocorrect typos, check for matching terms, PII, and classify.
{
"OriginalText": "\"Is this a grabage or crap email abcdef@abcd.com, phone: 6657789887, IP: 255.255.255.255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number (SSN).\"",
"NormalizedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"AutoCorrectedText": "\" Is this a garbage or crap email abide@ abed. com, phone: 6657789887, IP: 255. 255. 255. 255, 1 Microsoft Way, Redmond, WA 98052. These are all UK phone numbers, the last two being Microsoft UK support numbers: +44 870 608 4000 or 0344 800 2400 or 0800 820 3300. Also, 999-99-9999 looks like a social security number ( SSN) . \"",
"Misrepresentation": null,
    
"Classification": {
        "Category1": {
        "Score": 1.5113095059859916E-06
        },
        "Category2": {
        "Score": 0.12747249007225037
        },
        "Category3": {
        "Score": 0.98799997568130493
        },
        "ReviewRecommended": true
  },
  "Status": {
    "Code": 3000,
    "Description": "OK",
    "Exception": null
  },
  "PII": {
        "Email": [
            {
            "Detected": "abcdef@abcd.com",
            "SubType": "Regular",
            "Text": "abcdef@abcd.com",
            "Index": 33
            }
            ],
        "IPA": [
            {
            "SubType": "IPV4",
            "Text": "255.255.255.255",
            "Index": 73
            }
            ],
        "Phone": [
            {
            "CountryCode": "US",
            "Text": "6657789887",
            "Index": 57
            },
            {
            "CountryCode": "US",
            "Text": "870 608 4000",
            "Index": 211
            },
            {
            "CountryCode": "UK",
            "Text": "+44 870 608 4000",
            "Index": 207
            },
            {
            "CountryCode": "UK",
            "Text": "0344 800 2400",
            "Index": 227
            },
            {
        "CountryCode": "UK",
            "Text": "0800 820 3300",
            "Index": 244
            }
            ],
         "Address": [{
             "Text": "1 Microsoft Way, Redmond, WA 98052",
             "Index": 89
                }]
    },
  "Language": "eng",
  "Terms": [
    {
        "Index": 22,
        "OriginalIndex": 22,
        "ListId": 0,
        "Term": "crap"
    }
  ],
  "TrackingId": "9392c53c-d11a-441d-a874-eb2b93d978d3"
}
```

## <a name="next-steps"></a>Дополнительная информация

В этом кратком руководстве вы разработали простое приложение .NET, которое использует службу Content Moderator, для возврата релевантной информации о заданном примере текста. Затем узнайте больше о том, что означают разные флаги и классификации, чтобы решить, какие данные вам нужны и как ваше приложение должно их обрабатывать.

> [!div class="nextstepaction"]
> [Руководство по модерации текста](text-moderation-api.md)
