---
title: Добавление беседы в базу знаний QnA Maker
titleSuffix: Azure Cognitive Services
description: Добавление пользовательских бесед к боту при создании базы знаний делает его более общительным и приятным. QnA Maker позволяет легко добавлять в базу знаний заранее заданный набор самых интересных бесед.
services: cognitive-services
author: tulasim88
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: article
ms.date: 01/14/2019
ms.author: tulasim
ms.custom: seodec18
ms.openlocfilehash: d40330f257694e81704bf6fffa1fd2df8ed86c06
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858048"
---
# <a name="add-chit-chat-to-a-knowledge-base"></a>Добавление беседы в базу знаний

Добавление пользовательских бесед к боту делает его более общительным и приятным. Функция беседы в QnA Maker позволяет легко добавлять заранее заданный набор самых интересных бесед в базу знаний. Это может стать основой личности бота и сэкономить время и затраты на ее написание с нуля.  

Этот набор данных содержит около 100 сценариев бесед в трех тонах: профессионала, друга и комика. Выберите персонажа, тон которого больше всего напоминает тон бота. QnA Maker получает пользовательский запрос и пытается сопоставить его с наиболее близкой по теме беседой в разделе "Вопросы и ответы". 

Некоторые примеры разных персонажей: <!-- added quotes so acrolinx doesn't score these sentences -->.
|Пользовательский запрос|Профессионал|Друг|Комик|
|--|--|--|--|
|`You are awesome`|`I aim to serve.`|`That's so nice of you!`|`Flattery. I like it.`|
|`Are you hungry?`|`I don't need to eat.`|`I only do food for thought.`|`Eating would require a lot of things I don't have. Like a digestive system. And silverware.`|
|`Sing a song`|`I'm afraid I'm not musically inclined.`|`La la la, tra la la. I'm awesome at this.`|`You can't handle my dulcet tones.`|
|`Will you marry me?`|`I think it's best if we stick to a professional relationship.`|`Aw, that's sweet.`|`Sure. Take me to city hall. See what happens.`|


> [!NOTE]
> На данный момент беседа доступна только на английском языке. 

## <a name="add-chit-chat-during-kb-creation"></a>Добавление беседы во время создания базы знаний
При создании базы знаний после добавления исходных URL-адресов и файлов есть возможность добавить и беседу. Выберите персонажа для своей беседы. Если вы не хотите добавлять беседу или если в источниках данных уже есть поддержка беседы, выберите **None** (Не требуется). 
   
![Добавление беседы во время создания](../media/qnamaker-how-to-chit-chat/create-kb-chit-chat.png)

## <a name="add-chit-chat-to-an-existing-kb"></a>Добавление беседы в существующую базу знаний
Выберите базу знаний и перейдите на страницу **Параметры**. Здесь есть ссылка на все наборы данных бесед в соответствующем формате **TSV**. Загрузите желаемую личность, а затем отправьте ее как источник файла. Не изменяйте формат или метаданные при загрузке и отправке файла. 
  
![Добавление беседы в существующую базу знаний](../media/qnamaker-how-to-chit-chat/add-chit-chat-dataset.png)

## <a name="edit-your-chit-chat-questions-and-answers"></a>Изменение вопросов и ответов беседы
Во время изменения базы знаний вы увидите новый источник для беседы, зависящий от выбранного персонажа. Теперь вы можете добавлять измененные вопросы или редактировать ответы как и в любом другом источнике. 

![Изменение вопросов и ответов беседы](../media/qnamaker-how-to-chit-chat/edit-chit-chat.png)

## <a name="add-additional-chit-chat-questions-and-answers"></a>Добавление в беседу дополнительных вопросов и ответов
Вы можете добавлять в беседу вопросы и ответы, которых нет в стандартном наборе. Убедитесь, что вы не дублируете пару вопрос-ответ, которая уже включена в набор. При добавлении любая новая пара вопрос-ответ также будет добавлена в **Editorial** (редакционный) источник. Чтобы убедиться в том, что компонент ранжировки будет понимать, что это беседа, добавьте ключ метаданных или пару значений "Editorial: chit-chat", как показано на следующем изображении.
   
![Добавление вопросов и ответов беседы](../media/qnamaker-how-to-chit-chat/add-new-chit-chat.png)

## <a name="delete-chit-chat-from-an-existing-kb"></a>Удаление беседы из существующей базы знаний
Выберите базу знаний и перейдите на страницу **Параметры**. Конкретный источник беседы указан как файл с именем выбранного персонажа. Вы можете удалить его как исходный файл.

![Удаление беседы из базы знаний](../media/qnamaker-how-to-chit-chat/delete-chit-chat.png)

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Импорт базы знаний](../Tutorials/migrate-knowledge-base.md)

## <a name="see-also"></a>См. также 

[Общие сведения о QnA Maker](../Overview/overview.md)
