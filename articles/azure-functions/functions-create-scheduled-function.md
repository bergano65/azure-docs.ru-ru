---
title: Создание в Azure функции, активируемой по таймеру
description: Узнайте, как создать в Azure функцию, которая выполняется по определенному расписанию.
ms.assetid: ba50ee47-58e0-4972-b67b-828f2dc48701
ms.topic: how-to
ms.date: 04/16/2020
ms.custom: mvc, cc996988-fb4f-47
ms.openlocfilehash: be539efdb66b0a9bda583960484f40fae1e18235
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/12/2020
ms.locfileid: "83123501"
---
# <a name="create-a-function-in-azure-that-is-triggered-by-a-timer"></a>Создание в Azure функции, активируемой по таймеру

Узнайте, как создать [независимую от сервера](https://azure.microsoft.com/solutions/serverless/) функцию, которая выполняется на основе определенного расписания с помощью Функций Azure.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этим руководством сделайте следующее:

+ Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

## <a name="create-an-azure-function-app"></a>Создание приложения-функции Azure

[!INCLUDE [Create function app Azure portal](../../includes/functions-create-function-app-portal.md)]

Новое приложение-функция готово к использованию. Затем вы создадите функцию в новом приложении-функции.

:::image type="content" source="./media/functions-create-scheduled-function/function-app-create-success.png" alt-text="Приложение-функция успешно создана." border="true":::

<a name="create-function"></a>

## <a name="create-a-timer-triggered-function"></a>Создание функции, активируемой по таймеру

1. В приложении-функции выберите **Функции**, а затем выберите **+ Добавить**. 

   :::image type="content" source="./media/functions-create-scheduled-function/function-add-function.png" alt-text="Добавьте функцию на портале Azure." border="true":::

1. Выберите шаблон **Триггер таймера**. 

    :::image type="content" source="./media/functions-create-scheduled-function/function-select-timer-trigger.png" alt-text="Выберите триггер таймера на портале Azure." border="true":::

1. Настройте новый триггер с параметрами, как указано в таблице под изображением, и нажмите **Создать функцию**.

    :::image type="content" source="./media/functions-create-scheduled-function/function-configure-timer-trigger.png" alt-text="Выберите триггер таймера на портале Azure." border="true":::
    
    | Параметр | Рекомендуемое значение | Описание |
    |---|---|---|
    | **имя**; | По умолчанию | Определяет имя функции, активируемой по таймеру. |
    | **Расписание** | 0 \*/1 \* \* \* \* | [Выражение CRON](functions-bindings-timer.md#ncrontab-expressions) с шестью полями, в котором запланировано ежеминутное выполнение функции. |

## <a name="test-the-function"></a>Проверка функции

1. В функции выберите **Код + тест** и разверните журналы.

    :::image type="content" source="./media/functions-create-scheduled-function/function-test-timer-trigger.png" alt-text="Протестируйте триггер таймера на портале Azure." border="true":::

1. Проверьте выполнение, просмотрев записанные в журналах сведения.

    :::image type="content" source="./media/functions-create-scheduled-function/function-view-timer-logs.png" alt-text="Просмотрите триггер таймера на портале Azure." border="true":::

Теперь потребуется изменить расписание функции, чтобы она выполнялась раз в час, а не раз в минуту.

## <a name="update-the-timer-schedule"></a>Обновление расписания таймера

1. В функции выберите **Интеграция**. Здесь вы определяете входные и выходные привязки для вашей функции, а также задаете расписание. 

1. Выберите **Таймер (myTimer)** .

    :::image type="content" source="./media/functions-create-scheduled-function/function-update-timer-schedule.png" alt-text="Обновите расписание таймера на портале Azure." border="true":::

1. Измените значение **Расписание** на `0 0 */1 * * *`, а затем нажмите **Сохранить**.  

    :::image type="content" source="./media/functions-create-scheduled-function/function-edit-timer-schedule.png" alt-text="Обновление расписания таймера функций на портале Azure." border="true":::

Теперь функция будет выполняться в начале каждого часа.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [Next steps note](../../includes/functions-quickstart-cleanup.md)]

## <a name="next-steps"></a>Дальнейшие действия

Вы создали функцию, которая выполняется на основе расписания. Дополнительные сведения о триггерах см.в статье [Триггеры таймера для службы "Функции Azure"](functions-bindings-timer.md).

[!INCLUDE [Next steps note](../../includes/functions-quickstart-next-steps.md)]
