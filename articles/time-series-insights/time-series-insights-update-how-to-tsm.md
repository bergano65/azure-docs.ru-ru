---
title: Моделирование данных с помощью службы "Аналитика временных рядов Azure" (предварительная версия) | Документация Майкрософт
description: Общие сведения о моделировании данных в предварительной версии службы "Аналитика временных рядов".
author: ashannon7
ms.author: dpalled
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/26/2019
ms.custom: seodec18
ms.openlocfilehash: 05faf77d22f77da87e7c22d47473e6debf0f77c8
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67460891"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этом документе описывается работа с моделями временных рядов с использованием предварительной версии службы "Аналитика временных рядов Azure". Здесь подробно рассматриваются несколько распространенных сценариев данных.

Дополнительные сведения об использовании обновления см. в [этой статье](./time-series-insights-update-explorer.md).

## <a name="types"></a>Типы

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Типы**. Сверните панель, чтобы сосредоточиться на типах моделей временных рядов.

    [![Создание одного типа](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Выберите **Добавить**.
1. Введите все сведения, которые относятся к типам, а затем нажмите кнопку **Создать**. Это действие создает типы в среде.

    [![Добавление типа](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Щелкните **Отправить**.

    [![Отправка JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Изменение одного типа

1. Выберите тип, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Изменение типа](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Удаление типа

1. Выберите тип и щелкните **Удалить**.
1. Если с типами не связаны экземпляры, они удаляются.

    [![Удаление типа](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Иерархии

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Иерархии**. Сверните панель, чтобы сосредоточиться на иерархиях моделей временных рядов.

    [![Выберите иерархии](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Выберите **Добавить**.

    [![Добавление иерархии](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Выберите **Добавить уровень** в области справа.

    [![Добавить уровень](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Введите сведения об иерархии и выберите **Создать**.

    [![Создать уровень](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Щелкните **Отправить**.

    [![Массовая отправка иерархий](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

1. Выберите иерархию, а затем щелкните **Изменить**.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Изменение одной иерархии](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Удаление иерархии

1. Выберите иерархию, а затем щелкните **Удалить**. 
1. Если с иерархиями не связаны экземпляры, они удаляются.

    [![Удаление иерархии](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Экземпляры

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Экземпляры**. Сверните панель, чтобы сосредоточиться на экземплярах моделей временных рядов.

    [![Создать единственный экземпляр](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Выберите **Добавить**.

    [![Добавить экземпляр](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    [![Для массовой передачи одного или нескольких экземпляров](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Щелкните **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

1. Выберите экземпляр, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Изменение одного экземпляра](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о моделях временных рядов см. в [этой статье](./time-series-insights-update-tsm.md).

- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).

- Дополнительные сведения о поддерживаемых формах JSON см. в [этом разделе](./time-series-insights-send-events.md#json) статьи "Отправка событий в среду Time Series Insights через концентратор событий".
