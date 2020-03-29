---
title: Получить ответ по умолчанию - NA Maker
description: Ответ по умолчанию возвращается, когда нет совпадения с вопросом. Вы можете изменить ответ по умолчанию со стандартного ответа по умолчанию.
ms.topic: conceptual
ms.date: 01/10/2020
ms.openlocfilehash: fae5c38fd64435a3fae56862bad04e000916e88b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76843281"
---
# <a name="set-default-answer-for-a-knowledge-base"></a>Установите ответ по умолчанию для базы знаний

Ответ по умолчанию возвращается, когда нет совпадения с вопросом. Вы можете изменить ответ по умолчанию со стандартного ответа по умолчанию.

## <a name="change-default-answer"></a>Изменение ответа по умолчанию

1. Перейдите на [портал Azure](https://portal.azure.com) и перейдите к группе ресурсов, которая представляет созданный вами сервис QnA Maker.

2. Щелкните, чтобы открыть **службы приложений**.

    ![Доступ к службе приложений для QnA Maker на портале Azure](../media/qnamaker-concepts-confidencescore/set-default-response.png)

3. Щелкните **Параметры приложения** и укажите в поле **DefaultAnswer** требуемый ответ по умолчанию. Нажмите **Сохранить**.

    ![Выбор элемента "Параметры приложения" и изменение значения в поле DefaultAnswer для QnA Maker](../media/qnamaker-concepts-confidencescore/change-response.png)

4. Перезапустите службу приложений.

    ![Перезапуск службы приложений QnA Maker после изменения значения в поле DefaultAnswer](../media/qnamaker-faq/qnamaker-appservice-restart.png)

## <a name="next-steps"></a>Дальнейшие действия

* [Создайте бота с помощью «NA Maker» и LUIS](../tutorials/integrate-qnamaker-luis.md)