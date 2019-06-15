---
title: Базы знаний — QnA Maker
titleSuffix: Azure Cognitive Services
description: База знаний QnA Maker состоит из наборов пар вопросов и ответов (QnA) и дополнительных метаданных, связанных с каждой парой QnA.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/04/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: 02111ac90fe97ddaddbd41ad42410e7e76f1c405
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61379357"
---
# <a name="what-is-a-qna-maker-knowledge-base"></a>Что представляет собой база знаний QnA Maker?

База знаний QnA Maker состоит из наборов пар вопросов и ответов (QnA) и дополнительных метаданных, связанных с каждой парой QnA.

## <a name="key-knowledge-base-concepts"></a>Ключевые понятия базы знаний

* **Вопросы**. Вопрос содержит текст, который наилучшим образом представляет пользовательский запрос. 
* **Ответы**. Ответ, который возвращается, когда пользовательский запрос сопоставляется с соответствующим вопросом.  
* **Метаданные**. Метаданные — это теги, которые связаны с парой QnA и отображаются в виде пар "ключ — значение". Теги метаданных используются для фильтрации пар QnA и ограничения набора, в котором выполняется сопоставление запросов.

Единственный QnA, представленный числовым идентификатором QnA, имеет несколько вариантов вопроса (альтернативные вопросы), которые сопоставляются с одним ответом. Кроме того, каждой такой пары может иметь несколько полей метаданные, связанные с ней: один ключ и одно значение.

![Базы знаний QnA Maker](../media/qnamaker-concepts-knowledgebase/knowledgebase.png) 

## <a name="knowledge-base-content-format"></a>Формат содержимого базы знаний

При приеме содержимого в базу знаний QnA Maker пытается преобразовать контент в markdown. Подробнее о форматах markdown, понятных большинству чат-клиентов, см. в [этом](https://aka.ms/qnamaker-docs-markdown-support) блоге.

Поля метаданных состоят из пар "ключ — значение", разделенных двоеточием **(Продукт:Уничтожитель)** . Ключ и значение должны быть только текстом. Ключ метаданных не должен содержать пробелов. Метаданные поддерживает только одно значение каждого ключа.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Жизненный цикл базы знаний](./development-lifecycle-knowledge-base.md)

## <a name="see-also"></a>См. также

[Общие сведения о QnA Maker](../Overview/overview.md)
