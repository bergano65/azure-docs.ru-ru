---
title: включить файл
description: включить файл
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.date: 05/05/2020
ms.topic: include
ms.author: diberry
ms.openlocfilehash: da9388a3bd5f4d46ec34ed226e3ee23a96b2f494
ms.sourcegitcommit: 46f8457ccb224eb000799ec81ed5b3ea93a6f06f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/28/2020
ms.locfileid: "87374911"
---
## <a name="sign-in-to-luis-portal"></a>Вход на портал LUIS

Новому пользователю LUIS следует выполнить следующие действия:

1. Войдите на [портал LUIS](https://www.luis.ai), выберите свою страну или регион и примите условия использования. Если же вы видите **Мои приложения**, это значит, что ресурс LUIS уже существует и вы можете сразу перейти к созданию приложения. Сведения о поддерживаемых регионах см. в статье о [Регионы создания и публикации и связанные ключи](https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions).

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

1. Отобразится сводка по создаваемому ресурсу. Выберите **Далее**.

    ![Создание ресурса для разработки](../media/sign-in/sign-in-confirm-key-selection.png)

1. Подтвердите введенные данные, выбрав **Продолжить**.

    ![Создание ресурса для разработки](../media/sign-in/sign-in-confirm-continue.png)
