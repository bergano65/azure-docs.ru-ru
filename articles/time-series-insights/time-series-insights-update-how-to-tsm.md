---
title: Моделирование данных с помощью службы "Аналитика временных рядов Azure" (предварительная версия) | Документация Майкрософт
description: Общие сведения о моделировании данных в предварительной версии службы "Аналитика временных рядов".
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 10/29/2019
ms.custom: seodec18
ms.openlocfilehash: 81c2c2af78f5f066e1b27e14fa774df04d7c5868
ms.sourcegitcommit: f7f70c9bd6c2253860e346245d6e2d8a85e8a91b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73063984"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этой статье описывается работа с моделью временных рядов в предварительной версии службы "аналитика временных рядов Azure". Здесь подробно рассматриваются несколько распространенных сценариев данных.

Дополнительные сведения об использовании обновления см. в [этой статье](./time-series-insights-update-explorer.md).

## <a name="types"></a>Типы

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель выбора моделей временных рядов и выберите в меню пункт **типы** . Сверните панель, чтобы сосредоточиться на типах моделей временных рядов.

    [область "типы" ![](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Щелкните **+ Добавить**.
1. Введите все сведения, относящиеся к типам, и нажмите кнопку **создать**. Это действие создает типы в среде.

    [![выбора для добавления типа](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Щелкните **Отправить**.

    [![выборки для передачи одного или нескольких типов](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Изменение одного типа

1. Выберите тип, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![выбора для редактирования типа](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Удаление типа

1. Выберите тип и щелкните **Удалить**.
1. Если с типами не связаны экземпляры, они удаляются.

    [![кнопки "Удалить"](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Иерархии

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель выбора моделей временных рядов и выберите в меню пункт **иерархии** . Сверните панель, чтобы сосредоточиться на иерархиях модели временных рядов.

    [область "иерархии !["](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Щелкните **+ Добавить**.

    [Кнопка "Добавить" ![](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Выберите **+ Добавить уровень** в правой области.

    [Кнопка "Добавить уровень" ![](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Введите сведения об иерархии и выберите **Создать**.

    [![сведения о иерархии управляющих и кнопку "создать"](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Щелкните **Отправить**.

    [![выбор для выполнения групповой отправки иерархий](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

1. Выберите иерархию, а затем щелкните **Изменить**.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![выбора для редактирования одной иерархии](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Удаление иерархии

1. Выберите иерархию и нажмите кнопку **Удалить**. 
1. Если с иерархиями не связаны экземпляры, они удаляются.

    [![кнопки "Удалить"](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Экземпляры

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите на панель выбора моделей временных рядов и выберите в меню пункт **экземпляры** . Сверните панель, чтобы сосредоточиться на экземплярах модели временных рядов.

    [область "экземпляры" ![](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Выберите **Добавить**.

    [![выбора для добавления экземпляра](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    [![выбор для выполнения групповой отправки одного или нескольких экземпляров](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Щелкните **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

1. Выберите экземпляр, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![выбора для редактирования одного экземпляра](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о модели временных рядов см. в статье [моделирование данных](./time-series-insights-update-tsm.md).

- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).

- Дополнительные сведения о поддерживаемых фигурах JSON см. в статье [поддерживаемые фигуры JSON](./time-series-insights-send-events.md#supported-json-shapes).
