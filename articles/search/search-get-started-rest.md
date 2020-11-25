---
title: Краткое руководство. Создание индекса поиска с помощью интерфейсов REST API
titleSuffix: Azure Cognitive Search
description: Из этого краткого руководства вы узнаете, как вызывать интерфейсы REST API Когнитивного поиска Azure с помощью Postman или Visual Studio Code.
zone_pivot_groups: URL-test-interface-rest-apis
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: quickstart
ms.devlang: rest-api
ms.date: 11/17/2020
ms.openlocfilehash: 971ee806d8b5f9a336b40d96d500ce47d2f5166e
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94711399"
---
# <a name="quickstart-create-an-azure-cognitive-search-index-using-rest-apis"></a>Краткое руководство. Создание индекса службы "Когнитивный поиск Azure" с помощью интерфейсов REST API

В этой статье объясняется, как формулировать запросы REST API в интерактивном режиме, используя [интерфейсы REST API Когнитивного поиска Azure](/rest/api/searchservice) и клиент API для отправки и получения запросов. Используя клиент и API и следуя этим инструкциям, вы сможете отправлять запросы и просматривать ответы без написания кода.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

::: zone pivot="url-test-tool-rest-postman"

[!INCLUDE [Send requests using Postman](includes/search-get-started-rest-postman.md)]

::: zone-end

::: zone pivot="url-test-tool-rest-vscode-ext"

[!INCLUDE [Send requests using Visual Studio Code](includes/search-get-started-rest-vscode-ext.md)]

::: zone-end

## <a name="clean-up-resources"></a>Очистка ресурсов

Если вы работаете в своей подписке, по окончании проекта рекомендуем решить, нужны ли вам созданные ресурсы. Работающие ресурсы могут означать лишние затраты. Можно удалить отдельные ресурсы или удалить группу ресурсов, что позволит удалить весь набор ресурсов.

Просматривать ресурсы и управлять ими можно на портале с помощью ссылок **Все ресурсы** или **Группы ресурсов** на панели навигации слева.

При работе с бесплатной версией службы помните о том, что вам доступно максимум три индекса, индексатора и источника данных. Вы можете удалить отдельные элементы на портале, чтобы не превысить лимит. 

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы умеете выполнять основные задачи, вы можете изучить другие вызовы REST API для более сложных функций, таких как индексаторы или [настройка конвейера обогащения данных](cognitive-search-tutorial-blob.md), добавляющих преобразования содержимого в индексирование. Далее мы рекомендуем изучить следующие материалы:

> [!div class="nextstepaction"]
> [Учебник. использованию REST и искусственного интеллекта для создания доступного для поиска содержимого на основе данных больших двоичных объектов Azure](cognitive-search-tutorial-blob.md)