---
title: Моделирование данных с помощью службы "Аналитика временных рядов Azure" (предварительная версия) | Документация Майкрософт
description: Общие сведения о моделировании данных в предварительной версии службы "Аналитика временных рядов".
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 05/07/2019
ms.custom: seodec18
ms.openlocfilehash: 1c8886cada80c02e99782159099aa626da35fc50
ms.sourcegitcommit: e6d53649bfb37d01335b6bcfb9de88ac50af23bd
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65466615"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этом документе описывается работа с моделями временных рядов с использованием предварительной версии службы "Аналитика временных рядов Azure". Здесь подробно рассматриваются несколько распространенных сценариев данных.

Дополнительные сведения об использовании обновления см. в [этой статье](./time-series-insights-update-explorer.md).

## <a name="types"></a>Типы

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Типы**. Сверните панель, чтобы сосредоточиться на типах моделей временных рядов.

    [![Создание одного типа](media/v2-update-how-to-tsm/portal_one.png)](media/v2-update-how-to-tsm/portal_one.png#lightbox)

1. Выберите **Добавить**.
1. Введите все сведения, которые относятся к типам, а затем нажмите кнопку **Создать**. Это действие создает типы в среде.

    [![Добавление типа](media/v2-update-how-to-tsm/portal_two.png)](media/v2-update-how-to-tsm/portal_two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Щелкните **Отправить**.

    [![Отправка JSON](media/v2-update-how-to-tsm/portal_three.png)](media/v2-update-how-to-tsm/portal_three.png#lightbox)

### <a name="edit-a-single-type"></a>Изменение одного типа

1. Выберите тип, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Изменение типа](media/v2-update-how-to-tsm/portal_four.png)](media/v2-update-how-to-tsm/portal_four.png#lightbox)

### <a name="delete-a-type"></a>Удаление типа

1. Выберите тип и щелкните **Удалить**.
1. Если с типами не связаны экземпляры, они удаляются.

    [![Удаление типа](media/v2-update-how-to-tsm/portal_five.png)](media/v2-update-how-to-tsm/portal_five.png#lightbox)

## <a name="hierarchies"></a>Иерархии

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Иерархии**. Сверните панель, чтобы сосредоточиться на иерархиях моделей временных рядов.

    [![Выберите иерархии](media/v2-update-how-to-tsm/portal_six.png)](media/v2-update-how-to-tsm/portal_six.png#lightbox)

1. Выберите **Добавить**.

    [![Добавление иерархии](media/v2-update-how-to-tsm/portal_seven.png)](media/v2-update-how-to-tsm/portal_seven.png#lightbox)

1. Выберите **Добавить уровень** в области справа.

    [![Добавить уровень](media/v2-update-how-to-tsm/portal_eight.png)](media/v2-update-how-to-tsm/portal_eight.png#lightbox)

1. Введите сведения об иерархии и выберите **Создать**.

    [![Создать уровень](media/v2-update-how-to-tsm/portal_nine.png)](media/v2-update-how-to-tsm/portal_nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Щелкните **Отправить**.

    [![Массовая отправка иерархий](media/v2-update-how-to-tsm/portal_ten.png)](media/v2-update-how-to-tsm/portal_ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

1. Выберите иерархию, а затем щелкните **Изменить**.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Изменение одной иерархии](media/v2-update-how-to-tsm/portal_eleven.png)](media/v2-update-how-to-tsm/portal_eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Удаление иерархии

1. Выберите иерархию, а затем щелкните **Удалить**. 
1. Если с иерархиями не связаны экземпляры, они удаляются.

    [![Удаление иерархии](media/v2-update-how-to-tsm/portal_twelve.png)](media/v2-update-how-to-tsm/portal_twelve.png#lightbox)

## <a name="instances"></a>Экземпляры

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Экземпляры**. Сверните панель, чтобы сосредоточиться на экземплярах моделей временных рядов.

    [![Создать единственный экземпляр](media/v2-update-how-to-tsm/portal_thirteen.png)](media/v2-update-how-to-tsm/portal_thirteen.png#lightbox)

1. Выберите **Добавить**.

    [![Добавить экземпляр](media/v2-update-how-to-tsm/portal_fourteen.png)](media/v2-update-how-to-tsm/portal_fourteen.png#lightbox)

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    [![Для массовой передачи одного или нескольких экземпляров](media/v2-update-how-to-tsm/portal_fifteen.png)](media/v2-update-how-to-tsm/portal_fifteen.png#lightbox)

1. Щелкните **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

1. Выберите экземпляр, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Изменение одного экземпляра](media/v2-update-how-to-tsm/portal_sixteen.png)](media/v2-update-how-to-tsm/portal_sixteen.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о моделях временных рядов см. в [этой статье](./time-series-insights-update-tsm.md).

- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).

- Дополнительные сведения о поддерживаемых формах JSON см. в [этом разделе](./time-series-insights-send-events.md#json) статьи "Отправка событий в среду Time Series Insights через концентратор событий".
