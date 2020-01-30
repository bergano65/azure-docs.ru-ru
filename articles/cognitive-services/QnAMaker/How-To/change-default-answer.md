---
title: Получить ответ по умолчанию — QnA Maker
description: Ответ по умолчанию возвращается в случае отсутствия соответствующего вопроса. Может потребоваться изменить ответ по умолчанию из стандартного ответа по умолчанию.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76843281"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Задать ответ по умолчанию для базы знаний

Ответ по умолчанию возвращается в случае отсутствия соответствующего вопроса. Может потребоваться изменить ответ по умолчанию из стандартного ответа по умолчанию.

## <a name="change-default-answer"></a>Изменение ответа по умолчанию

1. Перейдите на [портал Azure](https://portal.azure.com) и перейдите к группе ресурсов, которая представляет созданный вами сервис QnA Maker.

2. Щелкните, чтобы открыть **службы приложений**.

    ![Доступ к службе приложений для QnA Maker на портале Azure](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Щелкните **Параметры приложения** и укажите в поле **DefaultAnswer** требуемый ответ по умолчанию. Выберите команду **Сохранить**.

    ![Выбор элемента "Параметры приложения" и изменение значения в поле DefaultAnswer для QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Перезапустите службу приложений.

    ![Перезапуск службы приложений QnA Maker после изменения значения в поле DefaultAnswer](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создание программы-робота с QnA Maker и LUIS](../tutorials/integrate-qnamaker-luis.md)