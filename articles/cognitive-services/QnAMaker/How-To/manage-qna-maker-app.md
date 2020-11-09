---
title: Управление QnA Maker приложением QnA Maker
description: QnA Maker позволяет нескольким пользователям совместно работать с базой знаний. QnA Maker предлагает возможность улучшить качество базы знаний с помощью активного обучения. Один из них может просматривать, принимать или отклонять и добавлять без удаления или изменения существующих вопросов.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.openlocfilehash: 93d9cc871d1cb114f7f08b68eb8ae9d597e228b9
ms.sourcegitcommit: 051908e18ce42b3b5d09822f8cfcac094e1f93c2
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94376491"
---
# <a name="manage-qna-maker-app"></a>Управление приложением QnA Maker

QnA Maker позволяет совместно работать с разными авторами и редакторами содержимого, предлагая возможность ограничить доступ совместной работы на основе роли участника совместной работы.
Узнайте больше об [основных понятиях проверки подлинности QnA Maker для совместной работы](../Concepts/role-based-access-control.md).

Вы также можете улучшить качество базы знаний, предлагая альтернативные вопросы через [активное обучение](../Concepts/active-learning-suggestions.md). Отправки пользователей учитываются и отображаются в списке альтернативных вопросов в виде предложений. Вы можете либо добавить эти предложения в качестве альтернативных вопросов, либо отклонить их.

База знаний не изменяется автоматически. Чтобы изменения вступили в силу, необходимо принять предложения. Так вы добавите новые вопросы, не меняя и не удаляя существующие.

## <a name="add-azure-role-based-access-control-azure-rbac"></a>Добавление управления доступом на основе ролей в Azure (Azure RBAC)

QnA Maker позволяет нескольким пользователям совместно работать над всеми базами знаний в одном и том же QnA Maker ресурсе. Эта функция предоставляется с помощью [управления доступом на основе ролей Azure (Azure RBAC)](../../../active-directory/role-based-access-control-configure.md).

## <a name="access-at-the-qna-maker-resource-level"></a>Доступ на уровне ресурсов QnA Maker

Вы не можете предоставить общий доступ к определенной базе знаний в службе QnA Maker. Если требуется более детализированное управление доступом, рассмотрите возможность распределения баз знаний по различным QnA Maker ресурсам, а затем добавьте роли для каждого ресурса.

## <a name="add-a-role-to-a-resource"></a>Добавление роли в ресурс

### <a name="add-a-user-account-to-the-qna-maker-resource"></a>Добавление учетной записи пользователя в ресурс QnA Maker

В следующих шагах используется роль совместной работы, но любые [роли](../reference-role-based-access-control.md) можно добавить с помощью этих шагов.

1. Войдите на портал [Azure](https://portal.azure.com/) и перейдите к ресурсу QnA Maker.

    ![Список ресурсов QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-resource-list.png)

1. Перейдите на вкладку **Управление доступом (IAM)**.

    ![IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam.png)

1. Выберите **Добавить**.

    ![Добавление IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add.png)

1. Выберите роль из следующего списка:

    |Роль|
    |--|
    |Владелец|
    |Участник|
    |Модуль чтения Cognitive Services QnA Maker|
    |Редактор Cognitive Services QnA Maker|
    |Пользователь служб Cognitive Services|

    :::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-add-role-iam.png" alt-text="QnA Maker Добавление роли IAM.":::

1. Введите адрес электронной почты пользователя и нажмите кнопку " **сохранить** ".

    ![Добавление адреса электронной почты для IAM в QnA Maker](../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-iam-add-email.png)

### <a name="view-qna-maker-knowledge-bases"></a>Просмотр QnA Maker баз знаний

Когда пользователь, которому вы предоставили доступ к QnA Maker службе с журналами на [портале QnA Maker](https://qnamaker.ai), он может просматривать все базы знаний в этой службе в зависимости от их роли.

При выборе базы знаний ее текущая роль на этом QnA Makerном ресурсе отображается рядом с именем базы знаний.

:::image type="content" source="../media/qnamaker-how-to-collaborate-knowledge-base/qnamaker-knowledge-base-role-name.png" alt-text="Снимок экрана базы знаний в режиме редактирования с именем роли в круглых скобках рядом с именем базы знаний в левом верхнем углу веб-страницы.":::

## <a name="upgrade-runtime-version-to-use-active-learning"></a>Обновить версию среды выполнения для использования активного обучения

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker (стабильный выпуск)](#tab/v1)

Активное обучение поддерживается в среде выполнения версии 4.4.0 и выше. Если ваша база знаний создана с помощью более ранней версии, [обновите среду выполнения](set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates), чтобы воспользоваться этой функцией.

# <a name="qna-maker-managed-preview-release"></a>[Управляемый QnA Maker (предварительный выпуск)](#tab/v2)

В QnA Maker Managed (Предварительная версия), так как среда выполнения размещается в самой QnA Makerной службе, нет необходимости обновлять среду выполнения вручную.

---

## <a name="turn-on-active-learning-for-alternate-questions"></a>Включение активного обучения для альтернативных вопросов

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker (стабильный выпуск)](#tab/v1)

По умолчанию активное обучение отключено. Чтобы просматривать предлагаемые вопросы, включите эту функцию. После включения активного обучения необходимо отправить сведения из клиентского приложения в QnA Maker. Дополнительные сведения см. в разделе [архитектурный поток для использования женератеансвер и обучение API-интерфейсов из Bot](improve-knowledge-base.md#architectural-flow-for-using-generateanswer-and-train-apis-from-a-bot).

1. Выберите **опубликовать** , чтобы опубликовать базу знаний. Активные запросы на обучение собираются только из конечной точки прогнозирования API Женератеансвер. Запросы к области тест на портале QnA Maker не влияют на активное обучение.

1. Чтобы включить активное обучение на портале QnA Maker, перейдите в правый верхний угол, выберите свое **имя** и перейдите к [**параметрам службы**](https://www.qnamaker.ai/UserSettings).

    ![Включить альтернативные варианты вопросов активного обучения на странице "Параметры службы". Выберите имя пользователя в верхнем правом меню, а затем выберите параметры службы.](../media/improve-knowledge-base/Endpoint-Keys.png)


1. Найдите службу QnA Maker, а затем активируйте переключатель **Active Learning** (Активное обучение).

    > [!div class="mx-imgBorder"]
    > [![На странице Параметры службы включите функцию активного обучения. Если вы не можете переключить эту функцию, возможно, потребуется обновить службу.](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png)](../media/improve-knowledge-base/turn-active-learning-on-at-service-setting.png#lightbox)
    > [!Note]
    > Точная версия на предыдущем рисунке показана только в качестве примера. Ваша версия может быть другой.
    После включения **активного обучения** база знаний предлагает новые вопросы через регулярные интервалы в соответствии с вопросами, отправленными пользователем. Вы можете отключить **активное обучение** с помощью этого же переключателя.

# <a name="qna-maker-managed-preview-release"></a>[Управляемый QnA Maker (предварительный выпуск)](#tab/v2)

По умолчанию активное обучение включено **в QnA Maker** Managed (Предварительная версия). Чтобы просмотреть предлагаемые альтернативные вопросы, [Используйте параметры просмотра](../How-To/improve-knowledge-base.md#view-suggested-questions) на странице Редактирование.

---

## <a name="review-suggested-alternate-questions"></a>Ознакомьтесь с предлагаемыми альтернативными вопросами

[Ознакомьтесь с дополнительными предлагаемыми вопросами](improve-knowledge-base.md) на странице **редактирования** каждой базы знаний.

## <a name="next-steps"></a>Дальнейшие действия

> [!div class="nextstepaction"]
> [Создание базы знаний](./manage-knowledge-bases.md)