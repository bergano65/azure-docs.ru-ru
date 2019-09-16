---
title: База знаний на языке, отличном от английского, — QnA Maker
titleSuffix: Azure Cognitive Services
description: QnA Maker поддерживает содержимое базы знаний на различных языках. При этом каждая служба QnA Maker должна быть зарезервирована для одного языка. Первая созданная база знаний, связанная с определенной службой QnA Maker, задает язык для этой службы.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: diberry
ms.openlocfilehash: 5e50c814fef24aa799549d055ad6496f5bdf05e0
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70961477"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Языковая поддержка содержимого базы знаний для QnA Maker

QnA Maker поддерживает содержимое базы знаний на различных языках. При этом каждая служба QnA Maker должна быть зарезервирована для одного языка. Первая созданная база знаний, связанная с определенной службой QnA Maker, задает язык для этой службы. Список поддерживаемых языков см. [здесь](../Overview/languages-supported.md).

Язык автоматическое распознается из извлекаемого содержимого источников данных. После создания новой службы QnA Maker и новой базы знаний в этой службе можно проверить, правильно ли задан язык.

1. Перейдите на [портал Azure](https://portal.azure.com/).

1. Щелкните **Группы ресурсов** и перейдите к группе ресурсов, в которой развернута служба QnA Maker, и выберите ресурс **Поиск Azure**.

    ![Выбор ресурса "Поиск Azure"](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Выберите **индексы**, а затем выберите индекс **тесткб** . Это первый созданный индекс поиска Azure, который содержит сохраненное содержимое всех баз знаний в этой службе. 

1. Выберите **поля** , чтобы просмотреть поля в индексе.

1. В столбце `questions` _анализатора_ полей и `answer` задан конкретный язык. Этот язык был автоматически обнаружен на этапе создания базы знаний из импортированных файлов и URL-адресов. После создания ресурса изменить этот язык нельзя.

    ![Выбранный флажок "Анализатор"](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Create a QnA bot with Azure Bot Service](../Tutorials/create-qna-bot.md) (Создание бота QnA с помощью службы Azure Bot)
