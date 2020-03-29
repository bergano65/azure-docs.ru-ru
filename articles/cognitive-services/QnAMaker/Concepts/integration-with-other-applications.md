---
title: Интеграция с другими приложениями - NA Maker
description: NA Maker интегрируется в клиентские приложения, такие как чат-боты, а также с другими службами обработки естественного языка, такими как Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78300525"
---
# <a name="design-knowledge-base-for-client-applications"></a>База знаний для клиентских приложений

NA Maker интегрируется в клиентские приложения, такие как чат-боты, а также с другими службами обработки естественного языка, такими как Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Интеграция с собеседником

NA Maker интегрируется с разговорными клиентскими приложениями, такими как [Microsoft Bot Framework.](https://dev.botframework.com/) Текст, отправленный в NA Maker, не нуждается в очистке или преобразовании. NA Maker принимает естественные языки и возвращает лучший ответ.

## <a name="create-a-bot-without-writing-any-code"></a>Создайте бота без написания кода

После публикации базы знаний создайте бота со страницы **«Публикация»,** выбрав кнопку **«Создать бот».** Используйте [учебник бота,](../Quickstarts/create-publish-knowledge-base.md) чтобы узнать, что происходит после выбора кнопки.

## <a name="providing-multi-turn-conversations"></a>Обеспечение многоповоротных разговоров

Клиент-бот предоставляет наилучший выбранный ответ из вашей базы знаний, и может предоставить последующие запросы, если ответ является частью набора multi-turn. [Узнайте, как добавить](../how-to/multiturn-conversation.md) многоповоротный вопрос и наборы ответов в свою базу знаний.

## <a name="natural-language-processing"></a>Обработка естественного языка

В то время как компания «NA Maker» обрабатывает вопросы, в которые используется обработка естественного языка, она также может быть использована частью более широкой системы, которая отвечает на вопросы из нескольких баз знаний. Вы можете объединить nA Maker с другой когнитивной службой, Понимание множеством (LUIS), чтобы обеспечить обработку естественного языка, прежде чем перейти к конкретной базе знаний. Узнайте больше о том, когда и как использовать [LUIS и NA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) вместе.

## <a name="next-steps"></a>Дальнейшие действия

Изучите концепции цикла [разработки](development-lifecycle-knowledge-base.md) для создателя NA.