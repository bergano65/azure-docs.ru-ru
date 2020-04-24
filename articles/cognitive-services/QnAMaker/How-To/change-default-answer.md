---
title: Получить ответ по умолчанию — QnA Maker
description: Ответ по умолчанию возвращается в случае отсутствия соответствующего вопроса. Может потребоваться изменить ответ по умолчанию из стандартного ответа по умолчанию.
ms.topic: how-to
ms.date: 04/22/2020
ms.openlocfilehash: db5a79ec612a73066ac37365a1815841fafb3862
ms.sourcegitcommit: 086d7c0cf812de709f6848a645edaf97a7324360
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82097104"
---
# <a name="change-default-answer-for-a-qna-maker-resource"></a>Изменить ответ по умолчанию для ресурса QnA Maker

Ответ по умолчанию возвращается в случае отсутствия соответствующего вопроса. Может потребоваться изменить ответ по умолчанию из стандартного ответа по умолчанию.

## <a name="change-default-answer-in-the-azure-portal"></a>Изменить ответ по умолчанию в портал Azure

1. Перейдите на [портал Azure](https://portal.azure.com) и перейдите к группе ресурсов, которая представляет созданный вами сервис QnA Maker.

2. Щелкните, чтобы открыть **службы приложений**.

    ![Доступ к службе приложений для QnA Maker на портале Azure](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Щелкните **Параметры приложения** и укажите в поле **DefaultAnswer** требуемый ответ по умолчанию. Нажмите кнопку **Сохранить**.

    ![Выбор элемента "Параметры приложения" и изменение значения в поле DefaultAnswer для QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Перезапустите службу приложений.

    ![Перезапуск службы приложений QnA Maker после изменения значения в поле DefaultAnswer](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Дальнейшие шаги

* [Создание базы знаний](../How-to/manage-knowledge-bases.md)