---
title: Краткое руководство. Создание базы знаний с помощью QnA Maker
titleSuffix: Azure Cognitive Services
description: Используйте портал службы API QnA Maker, чтобы добавить или создать базу знаний с беседой. Ваше приложение станет более привлекательным. Добавьте в базу знаний заранее заданный набор самых интересных бесед. Он может стать основой для бесед бота, а также сэкономить время и затраты на их создание.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 09/04/2019
ms.author: diberry
ms.custom: seodec18
ms.openlocfilehash: 798bbb725d4764c5ec7a1d69770e9508af0fdf5a
ms.sourcegitcommit: aebe5a10fa828733bbfb95296d400f4bc579533c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70376212"
---
# <a name="quickstart-create-a-knowledge-base-using-the-qna-maker-api-service-portal"></a>Краткое руководство. Создание базы знаний с помощью портала службы API QnA Maker

Портал службы API QnA Maker упрощает добавление существующих источников данных при создании базы знаний. Новую базу знаний QnA Maker можно создать, используя такие типы документов:

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

1. Укажите **имя** службы, например `my first kb`. Поддерживаются повторяющиеся имена, а также специальные знаки.

1. Добавьте страницу устранения неполадок с QnA Maker как URL-адрес `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`, а затем выберите `+ Add URL`. Дополнительные сведения о поддерживаемых типах источников приведены [здесь](../Concepts/data-sources-supported.md). Для работы с этим кратким руководством вам **не нужно отправлять файлы** для извлекаемых данных. Ознакомьтесь со [сведениями о ценах](https://aka.ms/qnamaker-pricing), чтобы узнать, сколько документов можно добавить.

1. Добавьте **_профессиональную_ беседу** в свою базу знаний. 

1. Выберите **Create your KB** (Создать базу знаний).

    ![Создание базы знаний](../media/qnamaker-how-to-create-kb/create-kb.png)

1. Извлечение данных может занять несколько минут.

    ![Извлечение](../media/qnamaker-how-to-create-kb/hang-tight-extraction.png)

1. По завершении вы будете перенаправлены на страницу **базы знаний**.

## <a name="clean-up-resources"></a>Очистка ресурсов

Когда вы закончите работу с базой знаний, удалите ее на портале QnA Maker.

## <a name="next-steps"></a>Дополнительная информация

Для экономии затрат вы можете [предоставить общий доступ](set-up-qnamaker-service-azure.md#share-existing-services-with-qna-maker) к некоторым (не всем) ресурсам Azure, созданным для QnA Maker.

> [!div class="nextstepaction"]
> [Добавление вопросов с использованием метаданных](../quickstarts/add-question-metadata-portal.md)
