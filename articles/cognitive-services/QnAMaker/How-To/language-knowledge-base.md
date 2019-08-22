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
ms.date: 08/20/2019
ms.author: diberry
ms.openlocfilehash: 63eb13dd131fcc1c424c02fdac10f531cc9f0282
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69876622"
---
# <a name="language-support-of-knowledge-base-content-for-qna-maker"></a>Языковая поддержка содержимого базы знаний для QnA Maker

QnA Maker поддерживает содержимое базы знаний на различных языках. При этом каждая служба QnA Maker должна быть зарезервирована для одного языка. Первая созданная база знаний, связанная с определенной службой QnA Maker, задает язык для этой службы. Список поддерживаемых языков см. [здесь](../Overview/languages-supported.md).

Язык автоматическое распознается из извлекаемого содержимого источников данных. После создания новой службы QnA Maker и новой базы знаний в этой службе можно проверить, правильно ли задан язык.

1. Перейдите на [портал Azure](https://portal.azure.com/).

1. Щелкните **Группы ресурсов** и перейдите к группе ресурсов, в которой развернута служба QnA Maker, и выберите ресурс **Поиск Azure**.

    ![Выбор ресурса "Поиск Azure"](../media/qnamaker-how-to-language-kb/select-azsearch.png)

1. Выберите индекс **testkb**. Этот индекс поиска Azure всегда создается первым. Он содержит сохраненное содержимое всех баз знаний в этой службе. 

    ![Выбор Test KB (Тестовая база знаний)](../media/qnamaker-how-to-language-kb/select-testkb.png)

1. В разделе Выбор **полей** отображаются сведения о _тесткб_ .

    ![Выбрать поля](../media/qnamaker-how-to-language-kb/selectfields.png)

1. Установите флажок **Анализатор** для просмотра сведений о языке.

    ![Выбор флажка "Анализатор"](../media/qnamaker-how-to-language-kb/select-analyzer.png)

1. Следует убедиться, что _анализатор_ настроен на конкретный язык. Этот язык был автоматически обнаружен на этапе создания базы знаний из импортированных файлов и URL-адресов. После создания ресурса изменить этот язык нельзя.

    ![Выбранный флажок "Анализатор"](../media/qnamaker-how-to-language-kb/selected-analyzer.png)

## <a name="next-steps"></a>Следующие шаги

> [!div class="nextstepaction"]
> [Create a QnA bot with Azure Bot Service](../Tutorials/create-qna-bot.md) (Создание бота QnA с помощью службы Azure Bot)
