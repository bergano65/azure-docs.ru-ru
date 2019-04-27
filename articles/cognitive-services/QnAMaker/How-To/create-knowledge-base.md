---
title: Создание базы знаний
titleSuffix: QnA Maker API - Azure Cognitive Services
description: На портале службы API службы QnA Maker, чтобы добавить создании базы знаний с chit чата. Ваше приложение станет более привлекательным. Добавьте в базу знаний заранее заданный набор самых интересных бесед. Он может стать основой для бесед бота, а также сэкономить время и затраты на их создание.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 03/25/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: ae278ac3e0fd9f93e080da8f52b9728e9c22a436
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "61373574"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Краткое руководство. Создание базы знаний с помощью портала службы API службы QnA Maker

На портале службы API службы QnA Maker упрощает добавление существующих источников данных, при создании базы знаний. Новую базу знаний QnA Maker можно создать, используя такие типы документов:

<!-- added for scanability -->
* страницы часто задаваемых вопросов;
* руководства по продуктам;
* структурированные документы.

Добавьте персонажа для беседы, чтобы сделать базу знаний более привлекательной для пользователей.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу. 

## <a name="create-a-new-knowledge-base"></a>Создание базы знаний

1. Войдите на [портал QnA Maker](https://qnamaker.ai) с помощью учетных данных Azure и выберите **Create a knowledge base** (Создать базу знаний).

1. Если служба QnA Maker еще не создана, выберите **Create a QnA service** (Создать службу QnA). 

1. На портале QnA Maker выберите клиент, имя подписки и имя ресурса Azure, связанные со службой QnA Maker, из списков на **шаге 2**. Выберите службу Azure QnA Maker, в которой будет размещаться база знаний.

    ![Установка службы QnA](../media/qnamaker-how-to-create-kb/setup-qna-resource.png)

1. Введите имя своей базы знаний, а также источники данных для новой базы знаний.

    ![Настройка источников данных](../media/qnamaker-how-to-create-kb/set-data-sources.png)

    - Введите **имя** для службы. Поддерживаются повторяющиеся имена, а также специальные знаки.
    - Добавьте URL-адреса для данных, которые необходимо извлечь. Дополнительные сведения о поддерживаемых типах источников приведены [здесь](../Concepts/data-sources-supported.md).
    - Отправьте файлы для данных, которые необходимо извлечь. Ознакомьтесь со [сведениями о ценах](https://aka.ms/qnamaker-pricing), чтобы узнать, сколько документов можно добавить.
    - Если вы хотите добавлять вопросы и ответы вручную, то можете пропустить **шаг 4**, показанный на предыдущем рисунке.

1. Добавьте **беседу** в свою базу знаний. Добавьте к вашему боту поддержку бесед, выбрав одного из трех персонажей. 

    ![Добавление беседы в базу знаний](../media/qnamaker-how-to-create-kb/create-kb-chit-chat.png)

1. Выберите **Create your KB** (Создать базу знаний).

    ![Создание базы знаний](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Извлечение данных может занять несколько минут.

    ![Извлечение](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. Если база знаний успешно создана, вы будете перенаправлены на страницу **Knowledge base** (База знаний).

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы закончите работу с базой знаний, удалите ее на портале QnA Maker.

## <a name="next-steps"></a>Дальнейшие действия

Для меры по экономии затрат, вы можете [совместно использовать](upgrade-qnamaker-service.md?#share-existing-services-with-qna-maker) некоторых, но не все ресурсы Azure, созданные для QnA Maker.

> [!div class="nextstepaction"]
> [Добавление персонажа беседы](./chit-chat-knowledge-base.md)
