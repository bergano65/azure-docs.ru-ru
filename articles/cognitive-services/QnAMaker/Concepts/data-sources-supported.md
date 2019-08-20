---
title: Поддерживаемые источники данных — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker автоматически извлекает пары "вопрос — ответ" из слабоструктурированного содержимого, такого как страницы вопросов и ответов, руководства по продукции, рекомендации, документы поддержки и политики, которые хранятся в виде веб-страниц, PDF-файлов или файлов документации MS Word. Также содержимое можно добавлять в базу знаний из структурированных файлов с содержимым вопросов и ответов (QnA).
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/16/2019
ms.author: diberry
ms.openlocfilehash: 5175dee24542c716b3d087412864ae7e6f056d18
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615983"
---
# <a name="data-sources-for-qna-maker-content"></a>Источники данных для содержимого QnA Maker

QnA Maker автоматически извлекает пары "вопрос — ответ" из частично структурированного содержимого, такого как страницы вопросов и ответов, руководства по продукции, рекомендации, справочные документы и политики, которые хранятся в виде веб-страниц, PDF-файлов или файлов документации MS Word. Также содержимое можно добавлять в базу знаний из структурированных файлов с содержимым вопросов и ответов (QnA). 

## <a name="data-types"></a>Типы данных

В приведенной ниже таблице представлены типы содержимого и форматы файлов, поддерживаемые QnA Maker.

|Тип источника|Тип содержимого| Примеры|
|--|--|--|
|URL|Часто задаваемые вопросы<br> (неструктурированные, с разделами или темами на домашней странице)<br>Страницы поддержки <br> (одностраничные руководства, устранение неполадок и т. д.)|[Простая страница вопросов и ответов](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Часто задаваемые вопросы о со ссылками](https://www.microsoft.com/software-download/faq)<br> [Вопросы и ответы с домашней страницей разделов](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Статья службы поддержки](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Часто задаваемые вопросы и ответы<br> Руководство по продукту<br> Брошюры<br> Бумажные документы<br> Политика в отношении флаеров<br> Руководство по поддержке<br> Файл структурированных вопросов и ответов (QnA)<br> и т. д.|[Файл структурированных вопросов и ответов (QnA).doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [Пример руководства по продукту.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)<br> [Пример слабоструктурированного раздела.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [Пример технического документа.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf)|
|Excel|Файл структурированных вопросов и ответов (QnA)<br> (включая поддержку RTF, HTML)|[Пример часто задаваемых вопросов QnA.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|TXT/TSV|Файл структурированных вопросов и ответов (QnA)|[Пример беседы.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

## <a name="data-source-locations"></a>Расположение источника данных

Расположения источников данных — это общедоступные **URL-адреса или файлы**, которые не нуждаются в проверке подлинности. 

Если для источника данных требуется проверка подлинности, рассмотрите следующие методы получения данных в QnA Maker:

* [Загрузить файл вручную](#download-file-from-authenticated-data-source-location) и импортировать в QnA Maker
* Файл импорта для [расположения SharePoint](#import-file-from-authenticated-sharepoint) , прошедшего проверку подлинности 

### <a name="download-file-from-authenticated-data-source-location"></a>Скачать файл из расположения источника данных, прошедшего проверку подлинности

Если у вас есть файл с проверкой подлинности (не в расположении с проверкой подлинности SharePoint) или URL-адрес, альтернативным вариантом является загрузка файла с сайта, прошедшего проверку подлинности, на локальный компьютер, а затем Добавление файла с локального компьютера в базу знаний.

### <a name="import-file-from-authenticated-sharepoint"></a>Импорт файла из прошедшего проверку подлинности SharePoint 

[Расположением источников данных SharePoint](../How-to/add-sharepoint-datasources.md) разрешено предоставлять **файлы**, прошедшие проверку подлинности. Ресурсы SharePoint должны быть файлами, а не веб-страницами. Если URL-адрес заканчивается веб-расширением, например **. ASPX**, он не будет импортирован в QnA Maker из SharePoint.


## <a name="faq-urls"></a>URL-адреса вопросов и ответов

QnA Maker может поддерживать веб-страницы вопросов и ответов в трех разных формах: простые страницы вопросов и ответов, страницы вопросов и ответов со ссылками, страницы вопросов и ответов с темами на домашней странице.

### <a name="plain-faq-pages"></a>Простые страницы вопросов и ответов

Это наиболее распространенный тип страниц с вопросами и ответами, в котором ответы содержатся на той же странице, сразу после каждого вопроса. 

Ниже показан пример простой страницы вопросов и ответов.

![Пример простой страницы с вопросами и ответами для базы знаний](../media/qnamaker-concepts-datasources/plain-faq.png) 

 
### <a name="faq-pages-with-links"></a>Страницы вопросов и ответов со ссылками 

В этом типе страниц с вопросами и ответами все вопросы собраны вместе и содержат ссылки на ответы, расположенные либо в разных разделах на той же странице, либо на разных страницах.

Ниже показан пример страницы вопросов и ответов со ссылками на разделы, которые расположенные на той же странице.

 ![Ссылка на раздел на странице с вопросами и ответами для базы знаний](../media/qnamaker-concepts-datasources/sectionlink-faq.png) 


### <a name="faq-pages-with-a-topics-homepage"></a>Страницы вопросов и ответов с темами на домашней странице

У этого типа вопросов и ответов есть домашняя страница с темами, где каждая тема — ссылка на соответствующие вопросы и ответы на другой странице. Здесь QnA Maker сканирует все связанные страницы, чтобы извлечь соответствующие вопросы и ответы.

Ниже приведен пример страницы вопросов и ответов, в которой темы домашней страницы имеют ссылки на разделы вопросов и ответов с разных страниц. 

 ![Прямая ссылка на страницу с вопросами и ответами для базы знаний](../media/qnamaker-concepts-datasources/topics-faq.png) 


### <a name="support-urls"></a>URL-адреса для получения поддержки

QnA Maker может обрабатывать полуструктурированные веб-страницы поддержки, такие как статьи в Интернете, описывающие способы выполнения определенной задачи, способы диагностики и устранения проблемы и рекомендации для процесса. Извлечение лучше всего работает с документами, которые имеют четкую структуру с иерархической структурой заголовков.

> [!NOTE]
> Извлечение для статьи о поддержке — это новая функция, которая находится на ранних стадиях. Она лучше всего подходит для простых страниц, которые хорошо структурированы и не содержат сложные колонтитулы.

![QnA Maker поддерживает извлечение из частично структурированных веб-страниц, где есть четкая структура и иерархические заголовки.](../media/qnamaker-concepts-datasources/support-web-pages-with-heirarchical-structure.png)


## <a name="pdf-doc-files"></a>PDF- или DOC-файлы

QnA Maker может обрабатывать слабоструктурированное содержимое в PDF- или DOC-файле и преобразовывать его в вопросы и ответы. Хороший файл, который можно успешно извлечь, — это тот, где содержимое организовано в некоторой структурированной форме и представлено в четко определенных разделах. Разделы могут быть дополнительно разбиты на подразделы или подтемы. Извлечение лучше всего работает с документами, которые имеют четкую структуру с иерархической структурой заголовков.

QnA Maker идентифицирует разделы, подразделы и отношения в файле на основе визуальных признаков, таких как размер шрифта, начертание шрифта, нумерация, цвета и т.д. Слабоструктурированные PDF- или DOC-файлы могут быть руководствами, часто задаваемыми вопросами, рекомендациями, политиками, брошюрами, объявлениями и многими другими типами файлов. Ниже приведены несколько примеров типов этих файлов.

### <a name="product-manuals"></a>Руководства по продукции

Руководством обычно называются справочные материалы, входящие в комплект поставки продукта. Они помогают пользователям устанавливать, использовать, обслуживать продукт и устранять возможные неполадки. Когда QnA Maker обрабатывает руководство, он извлекает заголовки и подзаголовки в качестве вопросов, а последующее содержимое — в качестве ответов. Пример вы можете найти [здесь](https://download.microsoft.com/download/2/9/B/29B20383-302C-4517-A006-B0186F04BE28/surface-pro-4-user-guide-EN.pdf).

Ниже приведен пример руководства со страницей индексов и иерархическим содержимым

 ![Пример руководства по продукту для базы знаний](../media/qnamaker-concepts-datasources/product-manual.png) 

> [!NOTE]
> Извлечение лучше всего работает с руководствами с отдельным содержанием и (или) страницей индексов и хорошей иерархической структурой заголовков.

### <a name="brochures-guidelines-papers-and-other-files"></a>Брошюры, рекомендации, документы и другие файлы

Многие другие типы документов также могут быть обработаны для создания пар вопросов и ответов при условии, что они имеют четкую структуру и макет. Сюда входит следующее. брошюры, рекомендации, отчеты, технические документы, научные статьи, политики, книги и т. д. Пример вы можете найти [здесь](https://qnamakerstore.blob.core.windows.net/qnamakerdata/docs/Manage%20Azure%20Blob%20Storage.docx).

Ниже приведен пример слабоструктурированного документа без индекса:

 ![Слабоструктурированный документ хранилища BLOB-объектов](../media/qnamaker-concepts-datasources/semi-structured-doc.png) 

### <a name="structured-qna-document"></a>Документ структурированных вопросов и ответов

Формат структурированных "вопросов — ответов" в DOC-файлах представлен в чередующемся формате — по одному вопросу в строке, за которым следует ответ в следующей строке, как показано ниже. 

```text
Question1

Answer1

Question2

Answer2
```

Ниже приведен пример структурированного документа Word вопросов и ответов.

 ![Пример документа структурированных вопросов и ответов для базы знаний](../media/qnamaker-concepts-datasources/structured-qna-doc.png) 

## <a name="structured-txt-tsv-and-xls-files"></a>Структурированные *TXT*, *TSV* и *XLS* файлы

Вопросы и ответы в форме структурированных файлов в формате *TXT*, *TSV* или *XLS* также могут быть загружены в QnA Maker для создания или расширения базы знаний.  Они могут быть либо обычным текстом, либо иметь содержимое в формате RTF или HTML. 

| Вопрос  | Ответ  | Метаданные (1 ключ: 1 значение) |
|-----------|---------|-------------------------|
| Question1 | Answer1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 |      `Key:Value`           |

Все остальные столбцы в исходном файле игнорируются.

### <a name="example-of-structured-excel-file"></a>Пример структурированного файла Excel

Ниже приведен пример структурированных вопросов и ответов файла формата *XLS* с содержимым HTML

 ![Пример структурированных вопросов и ответов в формате Excel для базы знаний](../media/qnamaker-concepts-datasources/structured-qna-xls.png)

### <a name="example-of-alternate-questions-for-single-answer-in-excel-file"></a>Пример альтернативных вопросов для одного ответа в файле Excel

Ниже приведен пример структурированного файла QnA *. xls* с несколькими альтернативными вопросами для одного ответа:

 ![Пример альтернативных вопросов для одного ответа в файле Excel](../media/qnamaker-concepts-datasources/xls-alternate-question-example.png)

После импорта файла пара "вопрос-ответ" находится в базе знаний, как показано ниже.

 ![Снимок экрана с альтернативными вопросами для одного ответа, импортированного в базу знаний](../media/qnamaker-concepts-datasources/xls-alternate-question-example-after-import.png)

## <a name="structured-data-format-through-import"></a>Загрузка данных в структурированном формате через импорт

При импорте базы знаний содержимое существующей базы знаний полностью заменяется. Для импорта нужно предоставить структурированный TSV-файл с информацией об источнике данных. Эти сведения помогают QnA Maker сгруппировать пары "вопрос — ответ" и сопоставить их с конкретным источником данных.

| Вопрос  | Ответ  | Source| Метаданные (1 ключ: 1 значение) |          
|-----------|---------|----|---------------------|
| Question1 | Answer1 | Url1 | <code>Key1:Value1 &#124; Key2:Value2</code> |
| Question2 | Answer2 | Редактирование|    `Key:Value`       |

## <a name="editorially-add-to-knowledge-base"></a>Добавление в базу знаний в режиме редактирования

Если у вас нет готового содержимого для заполнения базы знаний, вы можете добавить вопросы и ответы в режиме редактирования прямо в базу знаний QnA Maker. Узнайте, как [обновить базу знаний](../How-To/edit-knowledge-base.md).

<a href="#formatting-considerations"></a>

## <a name="formatting-considerations"></a>Рекомендации по форматированию

После импорта файла или URL-адреса QnA Maker преобразует и сохраняет содержимое в [формате Markdown](https://en.wikipedia.org/wiki/Markdown). В процессе преобразования в текст добавляются новые строки, например `\n\n`. Знание формата Markdown поможет вам разобраться с преобразованным содержимым и управлять содержанием базы знаний. 

Если вы добавляете или изменяете содержимое непосредственно в своей базе знаний, используйте **Форматирование Markdown** для создания форматированного текста или изменения содержимого формата Markdown, которое уже находится в ответе. QnA Maker поддерживает большую часть формата Markdown, чтобы обеспечить широкие возможности работы с текстом в содержимом. Однако клиентское приложение, например Bot чата, может не поддерживать одинаковый набор форматов Markdown. Важно протестировать отображение ответов клиентского приложения. 

Ниже приведен список форматов Markdown, которые можно использовать в QnA Maker. 

|Цель|Формат|Пример Markdown|Отрисовка<br>как отображается в чате чата|
|--|--|--|--|
Новая строка из двух предложений.|`\n\n`|`How can I create a bot with \n\n QnA Maker?`|![форматировать новую строку между двумя предложениями](../media/qnamaker-concepts-datasources/format-newline.png)|
|Заголовки от H1 до H6, количество `#` определяет, какой заголовок. 1 `#` — это H1.|`\n# text \n## text \n### text \n####text \n#####text` |`## Creating a bot \n ...text.... \n### Important news\n ...text... \n### Related Information\n ....text...`<br><br>`\n# my h1 \n## my h2\n### my h3 \n#### my h4 \n##### my h5`|![формат с заголовками Markdown](../media/qnamaker-concepts-datasources/format-headers.png)<br>![формат с заголовками Markdown от H1 до H5](../media/qnamaker-concepts-datasources/format-h1-h5.png)|
|Курсив |`*text*`|`How do I create a bot with *QnA Maker*?`|![форматировать курсивом](../media/qnamaker-concepts-datasources/format-italics.png)|
|Строка (полужирный)|`**text**`|`How do I create a bot with **QnA Maker**?`|![формат с усиленной маркировкой для полужирного шрифта](../media/qnamaker-concepts-datasources/format-strong.png)|
|URL-адрес для ссылки|`[text](https://www.my.com)`|`How do I create a bot with [QnA Maker](https://www.qnamaker.ai)?`|![формат URL-адреса (гиперссылка)](../media/qnamaker-concepts-datasources/format-url.png)|
|\* URL-адрес для общедоступного образа|`![text](https://www.my.com/image.png)`|`How can I create a bot with ![QnAMaker](https://review.docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/media/qnamaker-how-to-key-management/qnamaker-resource-list.png)`|![формат URL-адреса общедоступного образа ](../media/qnamaker-concepts-datasources/format-image-url.png)|
|Зачеркнутый|`~~text~~`|`some ~~questoins~~ questions need to be asked`|![формат для зачеркивания](../media/qnamaker-concepts-datasources/format-strikethrough.png)|
|Полужирный и курсив|`***text***`|`How can I create a ***QnA Maker*** bot?`|![формат для полужирного шрифта и курсива](../media/qnamaker-concepts-datasources/format-bold-italics.png)|
|Полужирный URL-адрес для ссылки|`[**text**](https://www.my.com)`|`How do I create a bot with [**QnA Maker**](https://www.qnamaker.ai)?`|![формат для URL-адреса полужирного шрифта](../media/qnamaker-concepts-datasources/format-bold-url.png)|
|URL-адрес курсивов для ссылки|`[*text*](https://www.my.com)`|`How do I create a bot with [*QnA Maker*](https://www.qnamaker.ai)?`|![формат URL-адреса курсивов](../media/qnamaker-concepts-datasources/format-url-italics.png)|
|Escape-символы Markdown|`\*text\*`|`How do I create a bot with \*QnA Maker\*?`|![формат URL-адреса курсивов](../media/qnamaker-concepts-datasources/format-escape-markdown-symbols.png)|
|Упорядоченный список|`\n 1. item1 \n 1. item2`|`This is an ordered list: \n 1. List item 1 \n 1. List item 2`<br>В предыдущем примере используется автоматическая нумерация, встроенная в Markdown.<br>`This is an ordered list: \n 1. List item 1 \n 2. List item 2`<br>В предыдущем примере используется явная нумерация.|![формат упорядоченного списка](../media/qnamaker-concepts-datasources/format-ordered-list.png)|
|Неупорядоченный список|`\n * item1 \n * item2`<br>или диспетчер конфигурации служб<br>`\n - item1 \n - item2`|`This is an ordered list: \n * List item 1 \n * List item 2`|![формат неупорядоченного списка](../media/qnamaker-concepts-datasources/format-unordered-list.png)|
|Вложенные списки|`\n * Parent1 \n\t * Child1 \n\t * Child2 \n * Parent2`<br><br>`\n * Parent1 \n\t 1. Child1 \n\t * Child2 \n 1. Parent2`<br><br>Упорядоченные и неупорядоченные списки можно вкладывать вместе. Вкладка `\t`, указывает уровень отступа дочернего элемента.|`This is an unordered list: \n * List item 1 \n\t * Child1 \n\t * Child2 \n * List item 2`<br><br>`This is an ordered nested list: \n 1. Parent1 \n\t 1. Child1 \n\t 1. Child2 \n 1. Parent2`|![формат вложенного неупорядоченного списка](../media/qnamaker-concepts-datasources/format-nested-unordered-list.png)<br>![формат вложенного упорядоченного списка](../media/qnamaker-concepts-datasources/format-nested-ordered-list.png)|

\* QnA Maker не обрабатывает образ каким бы то ни было образом. Это роль клиентского приложения для отрисовки образа. 

## <a name="editing-your-knowledge-base-locally"></a>Локальное изменение базы знаний

После создания базы знаний рекомендуется вносить изменения в текст базы знаний на [портале QnA Maker](https://qnamaker.ai), а не экспортировать и повторно импортировать его через локальные файлы. Тем не менее возможны ситуации, когда вам нужно изменить базу знаний локально. 

Экспортируйте базу знаний со страницы **Параметры**, затем внесите в нее изменения в Microsoft Excel. Если вы решите использовать другое приложение, чтобы изменить экспортированный TSV-файл, приложение может вызвать синтаксические ошибки, так как оно не полностью соответствует TSV. TSV-файлы в Microsoft Excel обычно не вызывают ошибок форматирования. 

Закончив внесение изменений, повторно импортируйте TSV-файл со страницы **Параметры**. Это полностью заменит текущую базу знаний на импортированную. 

## <a name="testing-your-markdown"></a>Тестирование Markdown

Используйте **[CommonMark](https://commonmark.org/help/tutorial/index.html)** для проверки Markdown. Руководство содержит функцию **Попробовать** для быстрой проверки копирования и вставки. 

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Настройка службы QnA Maker](../How-To/set-up-qnamaker-service-azure.md)

## <a name="see-also"></a>См. также 

[Общие сведения о QnA Maker](../Overview/overview.md)