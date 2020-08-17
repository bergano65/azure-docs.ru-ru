---
title: Изменение API с помощью портала Azure | Документация Майкрософт
description: Сведения о том, как изменить API с помощью службы "Управление API" (APIM). Добавление, удаление или переименование операций в экземпляре APIM или изменение Swagger API.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 11/08/2017
ms.author: apimpm
ms.openlocfilehash: 8d3f476f4a6ecf05d63223f3ba2c2b5173e1d0bf
ms.sourcegitcommit: 4e5560887b8f10539d7564eedaff4316adb27e2c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87905165"
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

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Примеры политик службы управления API](policy-samples.md)
> [Преобразование и защита опубликованного API](transform-api.md)