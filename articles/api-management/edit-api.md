---
title: Изменение API с помощью портала Azure | Документация Майкрософт
description: В этом руководстве показано, как изменить API с помощью службы управления API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 6f1a0cf6025cb3a398ab93320c6fcb69b1e62429
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60558093"
---
# <a name="edit-an-api"></a>Изменение API

Из этого руководства вы узнаете, как изменить API с помощью службы управления API. 

+ Это можно сделать, используя добавление, удаление и переименование в экземпляре службы управления API. 
+ Вы можете изменить Swagger API.

## <a name="prerequisites"></a>Предварительные требования

+ [Создание экземпляра службы управления API Azure](get-started-create-service-instance.md)
+ [Импорт и публикация первого API](import-and-publish.md)

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="edit-an-api-in-apim"></a>Изменение API в службе управления API

![Изменение API](./media/edit-api/edit-api001.png)

1. Щелкните вкладку **Интерфейсы API**.
2. Выберите один из API, которые вы импортировали ранее.
3. Выберите вкладку **Конструктор**.
4. Выберите операцию, которую нужно изменить.
5. Чтобы переименовать операцию, щелкните значок **карандаша** в окне **Интерфейсный сервер**.

## <a name="update-the-swagger"></a>Обновление Swagger

Можно обновить API внутреннего сервера на портале Azure, выполнив следующие действия:

1. Выберите **Все операции**.
2. Щелкните значок карандаша в окне **Интерфейсный сервер**.

    ![Изменение API](./media/edit-api/edit-api002.png)

    Отобразится спецификация Swagger API.

    ![Изменение API](./media/edit-api/edit-api003.png)

3. Обновите Swagger.
4. Нажмите кнопку **Save**(Сохранить).

[!INCLUDE [api-management-define-api-topics.md](../../includes/api-management-define-api-topics.md)]

## <a name="next-steps"></a>Дополнительная информация

> [!div class="nextstepaction"]
> [Примеры политик службы управления API](policy-samples.md)
> [Преобразование и защита опубликованного API](transform-api.md)