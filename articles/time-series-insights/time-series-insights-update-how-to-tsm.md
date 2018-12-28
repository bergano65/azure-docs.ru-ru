---
title: Моделирование данных в службе "Аналитика временных рядов". Моделирование данных в предварительной версии службы "Аналитика временных рядов" | Документация Майкрософт
description: Общие сведения о моделировании данных в предварительной версии службы "Аналитика временных рядов".
author: ashannon7
ms.author: anshan
ms.workload: big-data
manager: cshankar
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/10/2018
ms.custom: seodec18
ms.openlocfilehash: e68bc01d2c0781333454fa753992d0136fac0c06
ms.sourcegitcommit: 7fd404885ecab8ed0c942d81cb889f69ed69a146
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/12/2018
ms.locfileid: "53269096"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этом документе описывается работа с моделями временных рядов с использованием предварительной версии службы "Аналитика временных рядов Azure". Здесь подробно рассматриваются несколько распространенных сценариев данных.

Дополнительные сведения об использовании обновления см. в [этой статье](./time-series-insights-update-explorer.md).

## <a name="types"></a>Типы

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Типы**. Сверните панель, чтобы сосредоточиться на типах моделей временных рядов.

    ![Portal_one][1]

1. Выберите **Добавить**.
1. Введите все сведения, которые относятся к типам, а затем нажмите кнопку **Создать**. Это действие создает типы в среде.

    ![Portal_two][2]

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Щелкните **Отправить**.

    ![Portal_three][3]

### <a name="edit-a-single-type"></a>Изменение одного типа

Выберите тип, а затем щелкните **Изменить**. Внесите необходимые изменения и выберите **Сохранить**.

![Portal_four][4]

### <a name="delete-a-type"></a>Удаление типа

Выберите тип и щелкните **Удалить**. Если с типами не связаны экземпляры, они удаляются.

![Portal_five][5]

## <a name="hierarchies"></a>Иерархии

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Иерархии**. Сверните панель, чтобы сосредоточиться на иерархиях моделей временных рядов.

    ![Portal_six][6]

1. Выберите **Добавить**.

    ![Portal_seven][7]

1. Выберите **Добавить уровень** в области справа.

    ![Portal_eight][8]

1. Введите сведения об иерархии и выберите **Создать**.

    ![Portal_nine][9]

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Щелкните **Отправить**.

    ![Portal_ten][10]

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

Выберите иерархию, а затем щелкните **Изменить**. Внесите необходимые изменения и выберите **Сохранить**.

![Portal_eleven][11]

### <a name="delete-a-hierarchy"></a>Удаление иерархии

Выберите иерархию, а затем щелкните **Удалить**. Если с иерархиями не связаны экземпляры, они удаляются.

![Portal_twelve][12]

## <a name="instances"></a>Экземпляры

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Экземпляры**. Сверните панель, чтобы сосредоточиться на экземплярах моделей временных рядов.

    ![Portal_thirteen][13]

1. Выберите **Добавить**.

    ![Portal_fourteen][14]

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    ![Portal_fifteen][15]

1. Щелкните **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

Выберите экземпляр, а затем щелкните **Изменить**. Внесите необходимые изменения и выберите **Сохранить**.

![Portal_sixteen][16]

### <a name="delete-an-instance"></a>Удаление экземпляра

Выберите экземпляр и щелкните **Удалить**. Если с экземплярами не связаны события, они удаляются.

## <a name="next-steps"></a>Дополнительная информация

- Дополнительные сведения о моделях временных рядов см. в [этой статье](./time-series-insights-update-tsm.md).
- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).
- Дополнительные сведения о поддерживаемых формах JSON см. в [этом разделе](./time-series-insights-send-events.md#json) статьи "Отправка событий в среду Time Series Insights через концентратор событий".

<!-- Images -->
[1]: media/v2-update-how-to-tsm/portal_one.png
[2]: media/v2-update-how-to-tsm/portal_two.png
[3]: media/v2-update-how-to-tsm/portal_three.png
[4]: media/v2-update-how-to-tsm/portal_four.png
[5]: media/v2-update-how-to-tsm/portal_five.png
[6]: media/v2-update-how-to-tsm/portal_six.png
[7]: media/v2-update-how-to-tsm/portal_seven.png
[8]: media/v2-update-how-to-tsm/portal_eight.png
[9]: media/v2-update-how-to-tsm/portal_nine.png
[10]: media/v2-update-how-to-tsm/portal_ten.png
[11]: media/v2-update-how-to-tsm/portal_eleven.png
[12]: media/v2-update-how-to-tsm/portal_twelve.png
[13]: media/v2-update-how-to-tsm/portal_thirteen.png
[14]: media/v2-update-how-to-tsm/portal_fourteen.png
[15]: media/v2-update-how-to-tsm/portal_fifteen.png
[16]: media/v2-update-how-to-tsm/portal_sixteen.png