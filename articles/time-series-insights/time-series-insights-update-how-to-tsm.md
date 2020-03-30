---
title: Моделирование данных в средах предварительного просмотра - Исследования серии времени Azure Документы Майкрософт
description: Узнайте о моделировании данных в обзоре Azure Time Series Insights Preview.
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 02/18/2020
ms.custom: seodec18
ms.openlocfilehash: 39ebbf99ad31cce20eabc20fbdc056c889235713
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470757"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этой статье описывается, как работать с моделью временных рядов в Azure Time Series Insights Preview. Здесь подробно рассматриваются несколько распространенных сценариев данных.

> [!TIP]
> * Читайте о [модели серии предварительного](time-series-insights-update-tsm.md)просмотра .
> * Подробнее о навигации по uI Preview в [Azure Time Series Исследования Предварительный исследователь](./time-series-insights-update-explorer.md).

## <a name="instances"></a>Экземпляры

Исследователь Azure Time Series Insights поддерживает операции Instance **CREATE,** **READ,** **UPDATE**и **DELETE** в браузере. 

Для начала выберите представление **модели** из **обзора** исследования Time Series Insights.

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите к панели селектора модели time Series и выберите **инстанции** из меню. Будут отображаться все экземпляры, связанные с выбранной средой Time Series Insights.

    [![Создайте единый экземпляр, сначала выбрав Инстанции.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Выберите **+ Добавить**.

    [![Добавьте экземпляр, выбрав кнопку «Добавить».](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

> [!TIP]
> Вы можете сохранить свои экземпляры на рабочем столе в JSON. Загруженный файл JSON может быть загружен следующими шагами.

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    [![Массовая загрузка экземпляров через JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Выберите команду **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

1. Выберите экземпляр и выберите значок **для отсылки** или **значка карандаша.** 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Отсеяните один экземпляр.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Удаление экземпляра

1. Выберите экземпляр и выберите значок **«удалить** или **мусорное ведро».**

   [![Удалите экземпляр, выбрав Удалить.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Подтвердите удаление, выбрав **Удаление.**

> [!NOTE]
> Экземпляр должен успешно пройти проверку поля для удаления.

## <a name="hierarchies"></a>Иерархии

Исследователь Azure Time Series Insights поддерживает операции Hierarchy **CREATE,** **READ,** **UPDATE**и **DELETE** в браузере. 

Для начала выберите представление **модели** из **обзора** исследования Time Series Insights.

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель селекторов модели серии времени и выберите **иерархии** из меню. Будут отображаться все иерархии, связанные с выбранной средой Time Series Insights.

    [![Создайте иерархию через панель.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Выберите **+ Добавить**.

    [![Иерархия и кнопка добавления.](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Выберите **уровень добавления** в правую панель.

    [![Добавьте уровень в иерархию.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Введите сведения об иерархии и выберите **Сохранить**.

    [![Укажите детали иерархии.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

> [!TIP]
> Иерархии можно сохранить на рабочем столе в JSON. Загруженный файл JSON может быть загружен следующими шагами.

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Выберите команду **Отправить**.

    [![Выбор для массовой загрузки иерархий.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

1. Выберите иерархию и выберите **значок** **для отображать** или карандаш.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Выбор для редактирования одной иерархии.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Удаление иерархии

1. Выберите иерархию и выберите значок **ячейки удаления** или **мусора.** 

    [![Удалите иерархию, выбрав кнопку «Удалить».](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Подтвердите удаление, выбрав **Удаление.**

## <a name="types"></a>Типы

Исследователь Azure Time Series Insights поддерживает операции Type **CREATE,** **READ,** **UPDATE**и **DELETE** в браузере. 

Для начала выберите представление **модели** из **обзора** исследования Time Series Insights.

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель селекторов модели time Series и выберите **типы** из меню. Будут отображаться все типы, связанные с выбранной средой Time Series Insights.

    [![Типы типов таймсерий.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Выберите **и добавьте** для отображения **Добавить новый тип** всплывающих модально.
1. Введите свойства и переменные для вашего типа. После ввода выберите **Сохранить**. 

    [![Настройки конфигурации для добавления типа.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

> [!TIP]
> Вы можете сохранить ваши типы на рабочем столе в JSON. Загруженный файл JSON может быть загружен следующими шагами.

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Выберите команду **Отправить**.

    [![Массовые типы загрузки параметров.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Изменение одного типа

1. Выберите тип и выберите **значок** **для отображать** или карандаш.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![Отображите тип в панели.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Удаление типа

1. Выберите тип и выберите значок **«удалить** или **мусорное ведро».** .

   [![Удалите тип, выбрав Удалить.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Подтвердите удаление, выбрав **Удаление.**

## <a name="next-steps"></a>Дальнейшие действия

- Для получения дополнительной информации о модели временных рядов, прочитайте [моделирование данных](./time-series-insights-update-tsm.md).

- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).

- Чтобы узнать о поддерживаемых формах JSON, прочитайте [поддерживаемые формы JSON.](./time-series-insights-send-events.md#supported-json-shapes)
