---
title: Моделирование данных в средах предварительного просмотра — служба "аналитика временных рядов Azure" | Документация Майкрософт
description: Сведения о моделировании данных в предварительной версии службы "аналитика временных рядов Azure".
author: deepakpalled
ms.author: dpalled
manager: cshankar
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 12/20/2019
ms.custom: seodec18
ms.openlocfilehash: 591233154ab79b9493d77030727462bae68ea7d5
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76152363"
---
# <a name="data-modeling-in-azure-time-series-insights-preview"></a>Моделирование данных в предварительной версии службы "Аналитика временных рядов"

В этой статье описывается работа с моделью временных рядов в предварительной версии службы "аналитика временных рядов Azure". Здесь подробно рассматриваются несколько распространенных сценариев данных.

> [!TIP]
> * Ознакомьтесь с [моделью временных рядов](time-series-insights-update-tsm.md)предварительной версии.
> * Дополнительные сведения о навигации по пользовательскому интерфейсу просмотра в [обозревателе предварительного просмотра службы "аналитика временных рядов Azure](./time-series-insights-update-explorer.md)".

## <a name="instances"></a>Экземпляры

Обозреватель службы "аналитика временных рядов Azure" поддерживает операции **создания**, **чтения**, **обновления**и **удаления** экземпляров в браузере. 

Чтобы начать, выберите представление **модели** в представлении **анализ** обозревателя аналитики временных рядов.

### <a name="create-a-single-instance"></a>Создание одного экземпляра

1. Перейдите на панель выбора моделей временных рядов и выберите в меню пункт **экземпляры** . Будут отображены все экземпляры, связанные с выбранной средой "аналитика временных рядов".

    [![создать один экземпляр, сначала выбрав экземпляры.](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-instances-panel.png#lightbox)

1. Щелкните **+ Добавить**.

    [![добавить экземпляр, нажав кнопку + Добавить.](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-add-instance.png#lightbox)

1. Введите сведения об экземпляре, выберите связь типа и иерархии, а затем щелкните **Создать**.

### <a name="bulk-upload-one-or-more-instances"></a>Массовая отправка одного или нескольких экземпляров

> [!TIP]
> Вы можете сохранить экземпляры на рабочем столе в формате JSON. Скачанный файл JSON затем можно отправить, выполнив следующие действия.

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные экземпляра.

    [![ная передача экземпляров с помощью JSON.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-instances.png#lightbox)

1. Щелкните **Отправить**.

### <a name="edit-a-single-instance"></a>Изменение одного экземпляра

1. Выберите экземпляр и щелкните значок **редактирования** или **карандаша**. 
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![изменить один экземпляр.](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-instance.png#lightbox)

### <a name="delete-an-instance"></a>Удаление экземпляра

1. Выберите экземпляр и щелкните значок **Удалить** или **мусорную ячейку**.

   [![удалить экземпляр, выбрав Удалить.](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-instance.png#lightbox)

1. Подтвердите удаление, выбрав **Удалить**.

> [!NOTE]
> Экземпляр должен успешно пройти проверку поля для удаления.

## <a name="hierarchies"></a>Иерархии

Обозреватель службы "аналитика временных рядов Azure" поддерживает операции **создания**, **чтения**, **обновления**и **удаления** иерархии в браузере. 

Чтобы начать, выберите представление **модели** в представлении **анализ** обозревателя аналитики временных рядов.

### <a name="create-a-single-hierarchy"></a>Создание одной иерархии

1. Перейдите на панель выбора моделей временных рядов и выберите в меню пункт **иерархии** . Будут отображены все иерархии, связанные с выбранной средой "аналитика временных рядов".

    [![создать иерархию с помощью панели.](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-hierarchy-panel.png#lightbox)

1. Щелкните **+ Добавить**.

    [Кнопка "![иерархия + добавить".](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-hierarchy.png#lightbox)

1. Выберите **+ Добавить уровень** в правой области.

    [![добавить уровень в иерархию.](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png)](media/v2-update-how-to-tsm/how-to-tsm-save-hierarchy-levels.png#lightbox)

1. Введите сведения об иерархии и нажмите кнопку **сохранить**.

    [![указать сведения об иерархии.](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png)](media/v2-update-how-to-tsm/how-to-tsm-add-hierarchy-level.png#lightbox)

### <a name="bulk-upload-one-or-more-hierarchies"></a>Массовая отправка одной или нескольких иерархий

> [!TIP]
> Вы можете сохранить иерархии на рабочем столе в формате JSON. Скачанный файл JSON затем можно отправить, выполнив следующие действия.

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные иерархии.
1. Щелкните **Отправить**.

    [![выбор для выполнения групповой отправки иерархий.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-hierarchies.png#lightbox)

### <a name="edit-a-single-hierarchy"></a>Изменение одной иерархии

1. Выберите иерархию и щелкните значок **редактирования** или **карандаша**.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![выбора для редактирования одной иерархии.](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-hierarchy.png#lightbox)

### <a name="delete-a-hierarchy"></a>Удаление иерархии

1. Выберите иерархию и щелкните значок **Удалить** или **мусорную ячейку**. 

    [![удалить иерархию, нажав кнопку Удалить.](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-hierarchy.png#lightbox)

1. Подтвердите удаление, выбрав **Удалить**.

## <a name="types"></a>Типы

Обозреватель службы "аналитика временных рядов Azure" поддерживает операции **создания**, **чтения**, **обновления**и **удаления** в браузере. 

Чтобы начать, выберите представление **модели** в представлении **анализ** обозревателя аналитики временных рядов.

### <a name="create-a-single-type"></a>Создание одного типа

1. Перейдите на панель выбора моделей временных рядов и выберите в меню пункт **типы** . Будут отображены все типы, связанные с выбранной средой "аналитика временных рядов".

    [![панели Типы моделей временных рядов.](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png)](media/v2-update-how-to-tsm/how-to-tsm-type-panel.png#lightbox)

1. Выберите **+ Добавить** , чтобы отобразить всплывающее окно **Добавить новый тип** модальным.
1. Введите свойства и переменные для типа. После входа нажмите кнопку **сохранить**. 

    [![параметры конфигурации для добавления типа.](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png)](media/v2-update-how-to-tsm/how-to-tsm-add-new-type.png#lightbox)

### <a name="bulk-upload-one-or-more-types"></a>Массовая отправка одного или нескольких типов

> [!TIP]
> Вы можете сохранить типы на рабочем столе в формате JSON. Скачанный файл JSON затем можно отправить, выполнив следующие действия.

1. Выберите **Отправка файла JSON**.
1. Выберите файл, который содержит полезные данные типа.
1. Щелкните **Отправить**.

    [![параметров отправки типов с массовыми типами.](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png)](media/v2-update-how-to-tsm/how-to-tsm-bulk-upload-types-json.png#lightbox)

### <a name="edit-a-single-type"></a>Изменение одного типа

1. Выберите тип и щелкните значок **редактирования** или **карандаша**.
1. Внесите необходимые изменения и выберите **Сохранить**.

    [![изменить тип на панели.](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png)](media/v2-update-how-to-tsm/how-to-tsm-edit-type.png#lightbox)

### <a name="delete-a-type"></a>Удаление типа

1. Выберите тип и щелкните значок **Удалить** или **мусорную ячейку**. .

   [![удалить тип, выбрав Удалить.](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png)](media/v2-update-how-to-tsm/how-to-tsm-delete-type.png#lightbox)

1. Подтвердите удаление, выбрав **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

- Дополнительные сведения о модели временных рядов см. в статье [моделирование данных](./time-series-insights-update-tsm.md).

- Дополнительные сведения о возможностях предварительной версии см. в статье [Visualize data in the explorer Preview](./time-series-insights-update-explorer.md) (Визуализация данных в обозревателе предварительной версии службы).

- Дополнительные сведения о поддерживаемых фигурах JSON см. в статье [поддерживаемые фигуры JSON](./time-series-insights-send-events.md#supported-json-shapes).
