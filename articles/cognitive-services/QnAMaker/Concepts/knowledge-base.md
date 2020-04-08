---
title: Импорт из источников данных - NA Maker
description: База знаний создателя NA состоит из набора наборов вопросов и ответов и дополнительных метаданных, связанных с каждой парой NA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804288"
---
# <a name="importing-from-data-sources"></a>Импорт из источников данных

База знаний состоит из наборов вопросов и ответов, внеенных публичными URL-адресами и файлами.

## <a name="data-source-locations"></a>Расположение источника данных

Содержимое ввозимо в базу знаний из источника данных. Места исхода данных являются **общедоступными URL-адресами или файлами,** которые не требуют проверки подлинности.

[Файлы SharePoint,](../how-to/add-sharepoint-datasources.md)защищенные проверкой подлинности, являются исключением. Ресурсы SharePoint должны быть файлами, а не веб-страницами. Если URL-адрес заканчивается веб-расширением, например. ASPX, он не будет импортировать в NA Maker от SharePoint.

## <a name="chit-chat-content"></a>Чат чата Чит

Набор содержимого чата Chit snA предлагается в качестве полного источника данных содержимого на нескольких языках и в стиле разговорной. Это может стать основой личности бота и сэкономить время и затраты на ее написание с нуля. [Узнайте, как добавить](../how-to/chit-chat-knowledge-base.md) этот набор содержимого в базу знаний.

## <a name="structured-data-format-through-import"></a>Загрузка данных в структурированном формате через импорт

При импорте базы знаний содержимое существующей базы знаний полностью заменяется. Импорт требует структурированного файла, `.tsv` который содержит вопросы и ответы. Эта информация помогает группе «NA Maker» группировать наборы вопросов-ответов и относить их к определенному источнику данных.

| Вопрос  | Ответ  | Источник| Метаданные (1 ключ: 1 значение) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Редактирование|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Структурированный формат мульти-оборота через импорт

Можно создавать многоходовые `.tsv` разговоры в формате файла. Формат предоставляет вам возможность создавать многоходовые разговоры, анализируя предыдущие журналы чата (с другими `.tsv` процессами, а не с помощью NA Maker), а затем создавать файл с помощью автоматизации. Импортируйте файл для замены существующей базы знаний.

> [!div class="mx-imgBorder"]
> ![Концептуальная модель 3 уровней многоповоротного вопроса](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Столбец для мульти-поворота, `.tsv`специфичный для нескольких поворотов, является **Prompts.** Например, `.tsv`показанный в Excel, отображайте информацию для определения многоразовых детей:

```JSON
[
    {"displayOrder":0,"qnaId":2,"displayText":"Level 2 Question A"},
    {"displayOrder":0,"qnaId":3,"displayText":"Level 2 - Question B"}
]
```

**DisplayOrder** является числовым и **displayText** текст, который не должен включать разметку.

> [!div class="mx-imgBorder"]
> ![Пример вопроса с несколькими поворотами, показанный в Excel](../media/qnamaker-concepts-knowledgebase/multi-turn-tsv-columns-excel-example.png)

## <a name="export-as-example"></a>Экспорт в качестве примера

Если вы не знаете, как представить свою `.tsv` пару qnA в файле:
* Используйте этот [загружаемый пример с GitHub](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Structured-multi-turn-format.xlsx?raw=true)
* Или создайте набор на портале SnA Maker, сохраните, а затем экспортировать базу знаний на пример того, как представлять набор.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Жизненный цикл базы знаний](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>См. также

Используйте ссылку на знак «NA Maker [Markdown»,](../reference-markdown-format.md) чтобы помочь вам отформатировать ответы.

[Общие сведения о QnA Maker](../Overview/overview.md)

Создание и отодевание базы знаний с помощью:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Создайте ответ с:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
