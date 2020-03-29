---
title: Формат разметки - NA Maker
description: Ниже приведен список форматов разметки, которые можно использовать в тексте ответа создателя qnA.
ms.topic: reference
ms.date: 01/09/2020
ms.openlocfilehash: 3fb7d73afdfd5ab7f1fb56a685b21538b97c8ea4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77045392"
---
# <a name="markdown-format-supported-in-qna-maker-answer-text"></a>Формат разметки, поддерживаемый в тексте ответа на вопрос о создателе qnA

Магазины «NA Maker» отвечают на текст как на разметку. Есть много вкусов разметки. Для того, чтобы убедиться, что текст ответа возвращается и отображается правильно, используйте эту ссылку.

Используйте **[CommonMark](https://commonmark.org/help/tutorial/index.html)** для проверки Markdown. Руководство содержит функцию **Попробовать** для быстрой проверки копирования и вставки.

## <a name="supported-markdown-format"></a>Поддерживаемый формат разметки

Ниже приведен список форматов разметки, которые можно использовать в тексте ответа создателя qnA.

|Назначение|Формат|Пример разметки|Отрисовка<br>как показано в чат-бот|
|--|--|--|--|
Новая линия между 2 предложениями.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![формат новой линии между двумя предложениями](./media/qnamaker-concepts-datasources/format-newline.png)|
|Заголовки от h1 до h6, количество `#` обозначает который заголовок. 1 `#` это h1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![формат с заголовками разметки](./media/qnamaker-concepts-datasources/format-headers.png)<br>![формат с заголовками разметки H1 до H5](./media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Курсив |`*text*`|`How do I create a bot with *QnA Maker*?`|![формат с курсивом](./media/qnamaker-concepts-datasources/format-italics.png)|
|Сильный (смелый)|`**text**`|`How do I create a bot with **QnA Maker**?`|![формат с сильной маркировкой для смелых](./media/qnamaker-concepts-datasources/format-strong.png)|
|URL для ссылки|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![формат URL (гиперссылка)](./media/qnamaker-concepts-datasources/format-url.png)|
|URL для общедоступного изображения|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![формат URL-адреса общедоступных изображений ](./media/qnamaker-concepts-datasources/format-image-url.png)|
|Зачеркнутый|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![формат для strikethrough](./media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Смелый и курсив|`***text***`|`How can I create a ***QnA Maker*** bot?`|![формат для смелых и курсивом](./media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Смелый URL для соединения|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![формат для смелых URL](./media/qnamaker-concepts-datasources/format-bold-url.png)|
|Курсив URL для ссылки|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![формат для курсивом URL](./media/qnamaker-concepts-datasources/format-url-italics.png)|
|Символы разметки побега|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![формат для курсивом URL](./media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Упорядоченный список|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>В предыдущем примере используется автоматическая нумеровка, встроенная в разметку.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>В предыдущем примере используется явная нумерование.|![формат для заказанный список](./media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Неупорядоченный список|`\n * item1 \n * item2`<br>или диспетчер конфигурации служб<br>`\n - item1 \n - item2`|`This is an unordered list: \n * List item 1 \n * List item 2`|![формат для неупорядоченного списка](./media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Вложенные списки|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Вы можете гнездить упорядоченные и неупорядоченные списки вместе. Вкладка, `\t`указывает уровень отступов элемента ребенка.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![формат для вложенного неупорядоченного списка](./media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![формат для вложенного заказанный список](./media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

«NA Maker не обрабатывает изображение в любом случае. Роль клиентского приложения заключается в визуализации изображения.

Если вы хотите добавить контент с помощью ApIs базы знаний обновления/замены, а содержание/файл содержит HTML-теги, вы можете сохранить HTML в файле, гарантируя, что открытие и закрытие тегов преобразуется в закодированном формате.

| Сохранить HTML  | Представление в запросе API  | Представительство в КБ |
|-----------|---------|-------------------------|
| Да | \&lt;br\&gt; | &lt;Br&gt; |
| Да | \&lt;h3\&gt;header\&lt;/h3\&gt; | &lt;h3&gt;заголовок&lt;/h3&gt; |

Кроме того, CR LF (зран) преобразуются в КБ. LF (n) хранится как есть. Если вы хотите избежать любой последовательности побега, как t или n\\\\\\\\вы можете\\использовать backslash, например: 'r n' и 't'\\

## <a name="next-steps"></a>Дальнейшие действия

Просмотрите [форматы](reference-tsv-format-batch-testing.md)пакетного тестирования файлов .
