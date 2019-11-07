---
title: включение файла
description: включение файла
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: include file
ms.service: cognitive-services
ms.subservice: luis
ms.topic: include
ms.date: 10/23/2019
ms.author: diberry
ms.openlocfilehash: 1effd07cef439a6257028549b7b7114b742eb478
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73488743"
---
## <a name="sign-in-to-luis-portal"></a>Вход на портал LUIS

Новому пользователю LUIS следует выполнить следующие действия:

1. Войдите на [портал LUIS](https://www.luis.ai), выберите свою страну и примите условия использования.
1. Последовательно выберите **Create Azure resource** (Создать ресурс Azure) и **Create an authoring resource to migrate your apps to** (Создать ресурс для разработки, в который будут перенесены ваши приложения).

    ![Выбор типа ресурса для разработки в службе "Распознавание речи"](../media/luis-how-to-azure-subscription/sign-in-create-resource.png)

1. Укажите сведения о ресурсе.

    ![Создание ресурса для разработки](../media/migrate-authoring-key/choose-authoring-resource-form.png)

    При **создании ресурса для разработки** укажите следующие сведения: 

    * **Имя ресурса.** Выбранное пользователем имя, используемое в качестве части URL-адреса для запросов конечной точки разработки и прогнозирования.
    * **Клиент.** Клиент, с которым связана подписка Azure. 
    * **Имя подписки.** Подписка, для которой будет выставлен счет за ресурс.
    * **Группа ресурсов.** Выбранное или созданное пользователем имя группы ресурсов. Группы ресурсов позволяют группировать ресурсы Azure для осуществления доступа и управления. 
    * **Расположение.** Выбор расположения зависит от выбранной **группы ресурсов**.
    * **Ценовая категория.** Ценовая категория определяет максимальное количество транзакций в секунду и месяц.

1. Отобразится сводка по создаваемому ресурсу. Щелкните **Далее**.

    ![Создание ресурса для разработки](../media/sign-in/sign-in-confirm-key-selection.png)

1. Подтвердите введенные данные, выбрав **Продолжить**. 

    ![Создание ресурса для разработки](../media/sign-in/sign-in-confirm-continue.png)