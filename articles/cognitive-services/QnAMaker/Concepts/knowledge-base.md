---
title: Импорт из источников данных — QnA Maker
description: База знаний QnA Maker состоит из набора наборов вопросов и ответов (QnA) и необязательных метаданных, связанных с каждой парой QnA.
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: f648e15be803159dadb3f8bd047b2f46885eec91
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/29/2020
ms.locfileid: "80804288"
---
# <a name="importing-from-data-sources"></a>Импорт из источников данных

База знаний состоит из вопросов и наборов ответов, предоставленных общедоступными URL-адресами и файлами.

## <a name="data-source-locations"></a>Расположение источника данных

Содержимое помещается в базу знаний из источника данных. Расположения источников данных — это **общедоступные URL-адреса или файлы**, которые не нуждаются в проверке подлинности.

Исключением являются [файлы SharePoint](../how-to/add-sharepoint-datasources.md), защищенные с помощью проверки подлинности. Ресурсы SharePoint должны быть файлами, а не веб-страницами. Если URL-адрес заканчивается веб-расширением, например. ASPX, он не будет импортирован в QnA Maker из SharePoint.

## <a name="chit-chat-content"></a>ХИ2ТЕСТ — содержимое чата

Набор содержимого «Хи2тест Chat QnA» предлагается как полный источник данных содержимого на нескольких языках и в стилях общения. Это может стать основой личности бота и сэкономить время и затраты на ее написание с нуля. Узнайте [, как добавить](../how-to/chit-chat-knowledge-base.md) этот набор содержимого в базу знаний.

## <a name="structured-data-format-through-import"></a>Загрузка данных в структурированном формате через импорт

При импорте базы знаний содержимое существующей базы знаний полностью заменяется. Для импорта требуется структурированный `.tsv` файл, который содержит вопросы и ответы. Эти сведения помогают QnA Maker группировать наборы вопросов и ответов и применять их к определенному источнику данных.

| Вопрос  | Ответ  | Источник| Метаданные (1 ключ: 1 значение) |
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Редактирование|    `Key:Value`       |

## <a name="structured-multi-turn-format-through-import"></a>Структурированное многострочное форматирование с помощью импорта

Можно создавать множественные беседы в формате `.tsv` файла. Этот формат позволяет создавать множественные беседы путем анализа предыдущих журналов чатов (с другими процессами, не использующими QnA Maker), а затем создавать `.tsv` файл с помощью автоматизации. Импортируйте файл, чтобы заменить существующую базу знаний.

> [!div class="mx-imgBorder"]
> ![Концептуальная модель с тремя уровнями многофакторного вопроса](../media/qnamaker-concepts-knowledgebase/nested-multi-turn.png)

Столбец для множественной переворачивания `.tsv`, предназначенный для множественного включения, — это **запросы**. Пример `.tsv`, показанный в Excel, показывает сведения, которые необходимо включить, чтобы определить множественные дочерние элементы:

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
* Или создайте набор на портале QnA Maker, сохраните, а затем экспортируйте базу знаний, чтобы получить пример представления набора.

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Жизненный цикл базы знаний](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>См. также

Используйте ссылку на QnA Maker [Markdown](../reference-markdown-format.md) , чтобы отформатировать ответы.

[Общие сведения о QnA Maker](../Overview/overview.md)

Создание и изменение базы знаний с помощью:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.knowledgebase?view=azure-dotnet)

Создать ответ с помощью:
* [REST API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer)
* [Пакет SDK для .NET](https://docs.microsoft.com/dotnet/api/microsoft.azure.cognitiveservices.knowledge.qnamaker.runtime?view=azure-dotnet)
