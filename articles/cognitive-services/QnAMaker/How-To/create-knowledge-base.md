---
title: Создание базы знаний с помощью QnA Maker
titleSuffix: Azure Cognitive Services
description: Добавление бесед к боту делает его более общительным и приятным. QnA Maker позволяет легко добавлять в базу знаний заранее заданный набор самых интересных бесед. Он может стать основой для бесед бота, а также сэкономить время и затраты на их создание.
services: cognitive-services
author: tulasim88
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: article
ms.date: 09/12/2018
ms.author: tulasim
ms.openlocfilehash: c9d36014bc364d8b016221e6b9ff380b0bd4b8ed
ms.sourcegitcommit: 67abaa44871ab98770b22b29d899ff2f396bdae3
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48854173"
---
# <a name="create-a-knowledge-base"></a>Создание базы знаний

QnA Maker упрощает добавление существующих источников данных при создании базы знаний. Новую базу знаний QnA Maker можно создать, используя такие типы документов:

<!-- added for scanability -->
* страницы часто задаваемых вопросов;
* руководства по продуктам;
* структурированные документы.

## <a name="steps"></a>Действия

1. Войдите на [портал QnA Maker](https://qnamaker.ai) с помощью учетных данных Azure и выберите **Create a knowledge base** (Создать базу знаний).

2. Если служба QnA Maker еще не создана, выберите **Create a QnA service** (Создать службу QnA). 

3. На портале QnA Maker выберите клиент, имя подписки и имя ресурса Azure, связанные со службой QnA Maker, из списков на **шаге 2**. Выберите службу Azure QnA Maker, в которой будет размещаться база знаний.

    ![Установка службы QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

4. Введите имя своей базы знаний, а также источники данных для новой базы знаний.

    ![Настройка источников данных](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Введите **имя** для службы. Поддерживаются повторяющиеся имена, а также специальные знаки.
    - Добавьте URL-адреса для данных, которые необходимо извлечь. Дополнительные сведения о поддерживаемых типах источников приведены [здесь](../Concepts/data-sources-supported.md).
    - Отправьте файлы для данных, которые необходимо извлечь. Ознакомьтесь со [сведениями о ценах](https://aka.ms/qnamaker-pricing), чтобы узнать, сколько документов можно добавить.
    - Если вы хотите добавлять вопросы и ответы вручную, то можете пропустить **шаг 4**, показанный на предыдущем рисунке.

5. Добавьте **беседу** в свою базу знаний. Добавьте к вашему боту поддержку бесед, выбрав одного из трех предварительно определенных персонажей. 

    <!-- TBD: add back in when chit chat how-to is merged
    ![Add chit-chat to KB ](../media/qnamaker-how-to-chitchat/create-kb-chit-chat.png)
    -->

6. Выберите **Create your KB** (Создать базу знаний).

    ![Создание базы знаний](../media/qnamaker-how-to-create-kb/create-kb.png)

7. Извлечение данных может занять несколько минут.

    ![Извлечение](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

8. Если база знаний успешно создана, вы будете перенаправлены на страницу **Knowledge base** (База знаний).

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Добавление персонажа беседы](./chit-chat-knowledge-base.md)
