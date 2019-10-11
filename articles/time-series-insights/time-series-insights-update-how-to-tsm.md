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
ms.date: 09/04/2019
ms.custom: seodec18
ms.openlocfilehash: 64f892f344780d4af58c70935b9b7b68bad9550d
ms.sourcegitcommit: f272ba8ecdbc126d22a596863d49e55bc7b22d37
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/11/2019
ms.locfileid: "72273744"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этом документе описывается работа с моделями временных рядов с использованием предварительной версии службы "Аналитика временных рядов Azure". Здесь подробно рассматриваются несколько распространенных сценариев данных.

Дополнительные сведения об использовании обновления см. в [этой статье](./time-series-insights-update-explorer.md).

## <a name="types"></a>Типы

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Типы**. Сверните панель, чтобы сосредоточиться на типах моделей временных рядов.

    [![Create один тип](media/v2-update-how-to-tsm/portal-one.png)](media/v2-update-how-to-tsm/portal-one.png#lightbox)

1. Выберите **+ добавить**.
1. Введите все сведения, которые относятся к типам, а затем нажмите кнопку **Создать**. Это действие создает типы в среде.

    [@no__t 1Add тип](media/v2-update-how-to-tsm/portal-two.png)](media/v2-update-how-to-tsm/portal-two.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Щелкните **Отправить**.

    [@no__t 1Upload JSON](media/v2-update-how-to-tsm/portal-three.png)](media/v2-update-how-to-tsm/portal-three.png#lightbox)

### <a name="edit-a-single-type"></a>Изменение одного типа

1. Выберите тип, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [@no__t 1Edit тип](media/v2-update-how-to-tsm/portal-four.png)](media/v2-update-how-to-tsm/portal-four.png#lightbox)

### <a name="delete-a-type"></a>Удаление типа

1. Выберите тип и щелкните **Удалить**.
1. Если с типами не связаны экземпляры, они удаляются.

    [@no__t 1Delete тип](media/v2-update-how-to-tsm/portal-five.png)](media/v2-update-how-to-tsm/portal-five.png#lightbox)

## <a name="hierarchies"></a>Иерархии

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Иерархии**. Сверните панель, чтобы сосредоточиться на иерархиях моделей временных рядов.

    [иерархии ![Select](media/v2-update-how-to-tsm/portal-six.png)](media/v2-update-how-to-tsm/portal-six.png#lightbox)

1. Выберите **+ добавить**.

    [@no__t 1Add иерархии](media/v2-update-how-to-tsm/portal-seven.png)](media/v2-update-how-to-tsm/portal-seven.png#lightbox)

1. Выберите **+ Добавить уровень** в правой области.

    [@no__t 1Add уровня](media/v2-update-how-to-tsm/portal-eight.png)](media/v2-update-how-to-tsm/portal-eight.png#lightbox)

1. Введите сведения об иерархии и выберите **Создать**.

    [@no__t 1Create уровня](media/v2-update-how-to-tsm/portal-nine.png)](media/v2-update-how-to-tsm/portal-nine.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Щелкните **Отправить**.

    [иерархии отправки @no__t 1Bulk](media/v2-update-how-to-tsm/portal-ten.png)](media/v2-update-how-to-tsm/portal-ten.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

1. Выберите иерархию, а затем щелкните **Изменить**.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [@no__t 1Edit — одна иерархия](media/v2-update-how-to-tsm/portal-eleven.png)](media/v2-update-how-to-tsm/portal-eleven.png#lightbox)

### <a name="delete-a-hierarchy"></a>Удаление иерархии

1. Выберите иерархию, а затем щелкните **Удалить**. 
1. Если с иерархиями не связаны экземпляры, они удаляются.

    [@no__t 1Delete иерархии](media/v2-update-how-to-tsm/portal-twelve.png)](media/v2-update-how-to-tsm/portal-twelve.png#lightbox)

## <a name="instances"></a>Экземпляры

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите на панель селектора моделей временных рядов и в меню выберите **Экземпляры**. Сверните панель, чтобы сосредоточиться на экземплярах моделей временных рядов.

    [@no__t — 1Create один экземпляр](media/v2-update-how-to-tsm/portal-thirteen.png)](media/v2-update-how-to-tsm/portal-thirteen.png#lightbox)

1. Выберите **Добавить**.

    [![Add экземпляр](media/v2-update-how-to-tsm/portal-fourteen.png)](media/v2-update-how-to-tsm/portal-fourteen.png#lightbox)

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    [![Bulk отправить один или несколько экземпляров](media/v2-update-how-to-tsm/portal-fifteen.png)](media/v2-update-how-to-tsm/portal-fifteen.png#lightbox)

1. Щелкните **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

1. Выберите экземпляр, а затем щелкните **Изменить**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [@no__t — 1Edit один экземпляр](media/v2-update-how-to-tsm/portal-sixteen.png)](media/v2-update-how-to-tsm/portal-sixteen.png#lightbox)

## <a name="next-steps"></a>Следующие шаги

- Дополнительные сведения о моделях временных рядов см. в [этой статье](./time-series-insights-update-tsm.md).

- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).

- Дополнительные сведения о поддерживаемых формах JSON см. в [этом разделе](./time-series-insights-send-events.md#supported-json-shapes) статьи "Отправка событий в среду Time Series Insights через концентратор событий".
