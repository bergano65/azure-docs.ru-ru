---
title: Импорт из источников данных — QnA Maker
description: База знаний QnA Maker состоит из набора пар вопросов и ответов (QnA) и необязательных метаданных, связанных с каждой парой QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: eaa19cb2abf84f31cda9d8894e91ec1540980b27
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/27/2020
ms.locfileid: "83993105"
---
# <a name="importing-from-data-sources"></a>Импорт из источников данных

База знаний состоит из пар вопросов и ответов, предоставленных общедоступными URL-адресами и файлами.

## <a name="data-source-locations"></a>Расположение источника данных

Содержимое помещается в базу знаний из источника данных. Расположения источников данных — это **общедоступные URL-адреса или файлы**, которые не нуждаются в проверке подлинности.

Исключением являются [файлы SharePoint](../how-to/add-sharepoint-datasources.md), защищенные с помощью проверки подлинности. Ресурсы SharePoint должны быть файлами, а не веб-страницами. Если URL-адрес заканчивается веб-расширением, например .ASPX, импорт в QnA Maker из SharePoint не будет выполнен.

## <a name="chit-chat-content"></a>ХИ2ТЕСТ — содержимое чата

Набор содержимого «Хи2тест Chat QnA» предлагается как полный источник данных содержимого на нескольких языках и в стилях общения. Это может стать основой личности бота и сэкономить время и затраты на ее написание с нуля. Узнайте [, как добавить](../how-to/chit-chat-knowledge-base.md) этот набор содержимого в базу знаний.

## <a name="structured-data-format-through-import"></a>Загрузка данных в структурированном формате через импорт

При импорте базы знаний содержимое существующей базы знаний полностью заменяется. Для импорта требуется структурированный `.tsv` файл, который содержит вопросы и ответы. Эти сведения помогают QnA Maker сгруппировать пары "вопрос — ответ" и сопоставить их с конкретным источником данных.

| Вопрос  | Ответ  | Источник| Метаданные (1 ключ: 1 значение) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Редактирование|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Структурированное многострочное форматирование с помощью импорта

Можно создавать множественные беседы в `.tsv` формате файла. Этот формат позволяет создавать множественные беседы путем анализа предыдущих журналов чатов (с другими процессами, не использующими QnA Maker), а затем создавать `.tsv` файл с помощью автоматизации. Импортируйте файл, чтобы заменить существующую базу знаний.

> [!div class="mx-imgBorder"]
> ![Концептуальная модель с тремя уровнями многофакторного вопроса](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Столбец для множественной переворачивания `.tsv` , предназначенный для множественного включения, — это **запросы**. Пример `.tsv` , показанный в Excel, показывает сведения, которые необходимо включить, чтобы определить множественные дочерние элементы:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DISPLAYORDER** является числовым, а **DisplayText** — текстом, который не должен включать Markdown.

> [!div class="mx-imgBorder"]
> ![Пример многофакторного вопроса, как показано в Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Экспортировать как пример

Если вы не знаете, как представлять пару QnA в `.tsv` файле:
* Используйте этот [загружаемый пример из GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Или создайте пару на портале QnA Maker, сохраните, а затем экспортируйте базу знаний, чтобы получить пример представления пары.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Жизненный цикл базы знаний](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>См. также раздел

Используйте ссылку на QnA Maker [Markdown](../reference-markdown-format.md) , чтобы отформатировать ответы.

[Общие сведения о QnA Maker](../Overview/overview.md)

Создание и изменение базы знаний с помощью:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Создать ответ с помощью:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
