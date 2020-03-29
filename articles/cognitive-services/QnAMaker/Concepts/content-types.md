---
title: Типы контента - NA Maker
description: Типы содержимого включают в себя множество стандартных структурированных документов, таких как PDF, DOC и TXT.
services: cognitive-services
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: 7c78f9ea261fa636cce50b69524802d0900e9d7b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77650203"
---
# <a name="content-types-of-documents-you-can-add-to-a-knowledge-base"></a>Типы документов, которые можно добавить в базу знаний
Типы содержимого включают в себя множество стандартных структурированных документов, таких как PDF, DOC и TXT.

## <a name="file-and-url-data-types"></a>Типы файлов и URL-адресов

В приведенной ниже таблице представлены типы содержимого и форматы файлов, поддерживаемые QnA Maker.

|Тип источника|Тип содержимого| Примеры|
|--|--|--|
|URL-адрес|Часто задаваемые вопросы<br> (неструктурированные, с разделами или темами на домашней странице)<br>Страницы поддержки <br> (одностраничные руководства, устранение неполадок и т. д.)|[Простая страница вопросов и ответов](https://docs.microsoft.com/azure/cognitive-services/qnamaker/faqs) <br>[Часто задаваемые вопросы о со ссылками](https://www.microsoft.com/en-us/software-download/faq)<br> [Вопросы и ответы с домашней страницей разделов](https://www.microsoft.com/Licensing/servicecenter/Help/Faq.aspx)<br>[Статья поддержки](https://docs.microsoft.com/azure/cognitive-services/qnamaker/concepts/best-practices)|
|PDF/DOC|Часто задаваемые вопросы и ответы<br> Руководство по продукту<br> Брошюры<br> Бумажные документы<br> Политика в отношении флаеров<br> Руководство по поддержке<br> Файл структурированных вопросов и ответов (QnA)<br> и т. д.|**Без мульти-поворота**<br>[Файл структурированных вопросов и ответов (QnA).doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/structured.docx)<br> [Пример руководства по продукту.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/product-manual.pdf)<br> [Пример слабоструктурированного раздела.doc](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/semi-structured.docx)<br> [Пример белой бумаги.pdf](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/white-paper.pdf),<br><br>**Мульти-поворот**:<br>[Поверхность Pro (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/multi-turn.docx)<br>[Преимущества Контосо (docx)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.docx)<br>[Преимущества Контосо (pdf)](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Multiturn-ContosoBenefits.pdf)|
|(Excel)|Файл структурированных вопросов и ответов (QnA)<br> (включая поддержку RTF, HTML)|[Пример часто задаваемых вопросов QnA.xls](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/QnA%20Maker%20Sample%20FAQ.xlsx)|
|КХТ/TSV|Файл структурированных вопросов и ответов (QnA)|[Пример беседы.tsv](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/qna-maker/data-source-formats/Scenario_Responses_Friendly.tsv)|

Если вам нужна аутентификация для исходного кода данных, подумайте о следующих методах, чтобы получить это содержимое в nA Maker:

* Скачать файл вручную и импортировать в NA Maker
* Добавление файла из запотеченного [местоположения Sharepoint](../how-to/add-sharepoint-datasources.md)

## <a name="url-content"></a>Содержимое URL-адреса

Два типа документов могут быть импортированы по **URL** в qnA Maker:

* URL-адреса вопросов и ответов
* URL-адреса поддержки

Каждый тип указывает ожидаемый формат.

## <a name="file-based-content"></a>Содержимое на основе файлов

Вы можете добавить файлы в базу знаний из публичного источника или локальной файловой системы на [портале NA Maker.](https://www.qnamaker.ai)

## <a name="content-format-guidelines"></a>Руководящие принципы формата контента

Узнайте больше о [рекомендациях по формату](../reference-document-format-guidelines.md) для различных файлов.

## <a name="next-steps"></a>Дальнейшие действия

Понять, какая информация хранится в [наборе вопросов и ответов .](question-answer-set.md)