---
title: Интеграция с другими приложениями — QnA Maker
description: QnA Maker интегрируется в клиентские приложения, такие как чат программы-роботы, а также другие службы обработки естественных языков, такие как Language Understanding (LUIS).
ms.topic: conceptual
ms.date: 01/27/2020
ms.openlocfilehash: 38b275aa2b8cf5768a2bc95634a7ff4892893eda
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78300525"
---
# <a name="design-knowledge-base-for-client-applications"></a>Разработка базы знаний для клиентских приложений

QnA Maker интегрируется в клиентские приложения, такие как чат программы-роботы, а также другие службы обработки естественных языков, такие как Language Understanding (LUIS).

## <a name="integration-with-a-conversational-client"></a>Интеграция с клиентским клиентом

QnA Maker интегрируется с такими клиентскими приложениями, как [Microsoft Bot Framework](https://dev.botframework.com/). Текст, отправленный QnA Maker, не нуждается в очистке или преобразовании. QnA Maker принимает естественные языки и возвращает наилучший ответ.

## <a name="create-a-bot-without-writing-any-code"></a>Создание программы-робота без написания кода

После публикации базы знаний создайте робот на странице **Публикация** , нажав кнопку **создать Bot** . Используйте [учебник по Bot](../Quickstarts/create-publish-knowledge-base.md) , чтобы узнать, что происходит после нажатия кнопки.

## <a name="providing-multi-turn-conversations"></a>Предоставление множественных бесед

Клиент Bot предоставляет наилучший ответ, выбранный в базе знаний, и может предоставлять дополнительные запросы, если ответ входит в состав набора с множественным включением QnA. Узнайте, [как](../how-to/multiturn-conversation.md) добавить несколько наборов вопросов и ответов с несколькими сеансами в базу знаний.

## <a name="natural-language-processing"></a>Обработка естественного языка

Хотя QnA Maker обрабатывает вопросы, использующие обработку на естественном языке, она также может использоваться в составе более крупной системы, которая отвечает на вопросы из нескольких баз знаний. Можно объединить QnA Maker с другой службой, Language Understanding (LUIS), чтобы обеспечить обработку естественного языка перед переходом к определенной базе знаний. Узнайте больше о том, когда и как использовать [Luis и QnA Maker](../../luis/choose-natural-language-processing-service.md?toc=/azure/cognitive-services/qnamaker/toc.json) вместе.

## <a name="next-steps"></a>Дальнейшие действия

Узнайте о [концепциях](development-lifecycle-knowledge-base.md) циклов разработки для QnA Maker.