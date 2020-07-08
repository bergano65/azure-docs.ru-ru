---
title: Получить ответ по умолчанию — QnA Maker
description: Ответ по умолчанию возвращается в случае отсутствия соответствующего вопроса. Может потребоваться изменить ответ по умолчанию из стандартного ответа по умолчанию.
ms.topic: how-to
ms.date: 07/02/2020
ms.openlocfilehash: 005442938167c1bf7927603c44d6f870795cbeee
ms.sourcegitcommit: 93462ccb4dd178ec81115f50455fbad2fa1d79ce
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/06/2020
ms.locfileid: "85979982"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Изменить ответ по умолчанию для ресурса QnA Maker

Ответ по умолчанию для базы знаний должен быть возвращен, если ответ не найден. Если вы используете клиентское приложение, например [службу Azure Bot](https://docs.microsoft.com/azure/bot-service/bot-builder-howto-qna?view=azure-bot-service-4.0&tabs=cs#calling-qna-maker-from-your-bot), оно может также иметь отдельный ответ по умолчанию, указывающий на то, что ответ не соответствует порогу оценки.

## <a name="set-default-answer-when-you-create-knowledge-base"></a>Задать ответ по умолчанию при создании базы знаний

При создании новой базы знаний текст ответа по умолчанию является одним из параметров. Если вы решили не задавать его во время создания, вы можете изменить его позже, выполнив следующую процедуру.

## <a name="change-default-answer-in-qna-maker-portal"></a>Изменить ответ по умолчанию на портале QnA Maker

Ответ по умолчанию для базы знаний возвращается, если из службы QnA Maker не возвращен ответ.

1. Войдите на [портал QnA Maker](https://www.qnamaker.ai/) и выберите базу знаний из списка.
1. На панели навигации выберите **Параметры** .
1. Измените значение **текста ответа по умолчанию** в разделе **Управление базой знаний** .

    :::image type="content" source="../media/qnamaker-concepts-confidencescore/change-default-answer.png" alt-text="Снимок экрана: портал QnA Maker, страница параметров с выделенным текстовым полем ответа по умолчанию.":::

1. Выберите **сохранить и обучить** , чтобы сохранить изменения.

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание базы знаний](../How-to/manage-knowledge-bases.md)