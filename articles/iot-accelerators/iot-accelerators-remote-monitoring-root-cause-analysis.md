---
title: Анализ первопричин с помощью оповещений в Azure | Документация Майкрософт
description: В этом руководстве вы узнаете, как проводить анализ первопричин с помощью оповещений, используя Аналитику временных рядов Azure.
author: aditidugar
ms.author: adugar
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/20/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 70d29359d4a4bcf9f5badbbf0c553d7bed88a02b
ms.sourcegitcommit: 022cf0f3f6a227e09ea1120b09a7f4638c78b3e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/21/2018
ms.locfileid: "52284575"
---
# <a name="tutorial-conduct-a-root-cause-analysis-on-an-alert"></a>Руководство. Анализ первопричин с помощью оповещений

Из этого руководства вы узнаете, как использовать акселератор решения удаленного мониторинга для выявления первопричины. Вы увидите, что оповещение активируется на информационной панели решения для удаленного мониторинга, а затем используйте обозреватель Аналитики временных рядов Azure для выявления первопричины.

В этом руководстве используется два имитированных устройства для грузовых автомобилей, которые отправляют телеметрические данные местоположения, высоты над уровнем моря, скорости и температуры. Грузовиками управляет организация под названием Contoso, и они подключены к акселератору решения для удаленного мониторинга. Как оператору Contoso вам необходимо понять, почему один из грузовиков (delivery-truck-02) отправил оповещение о низкой температуре.

Изучив это руководство, вы:

>[!div class="checklist"]
> * фильтрация устройств на панели мониторинга;
> * просмотр телеметрии в реальном времени;
> * просмотрите данные в обозревателе службы "Аналитика временных рядов";
> * проведете анализ первопричин;
> * создадите новое правило с учетом результатов изучения.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/?WT.mc_id=A261C142F), прежде чем начинать работу.

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="choose-the-devices-to-display"></a>Выбор устройств для отображения

Чтобы выбрать, какие из подключенных устройств будут отображаться на странице **Панель мониторинга**, используйте фильтры. Для отображения только устройств **Грузовик**, выберите встроенный фильтр **Грузовик** в раскрывающемся списке фильтров:

[![Фильтр для грузовиков на панели мониторинга](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-trucks-expanded.png#lightbox)

При применении фильтра на карте и на панели телеметрии отображаются только те грузовики, которые отвечают условиям фильтра на **панели мониторинга**. Вы можете увидеть, что к акселератору решений подключены два грузовика, включая **truck-02**.

## <a name="view-real-time-telemetry"></a>просмотр телеметрии в реальном времени;

На диаграмме на странице **Панель мониторинга** в акселераторе решений представлены данные телеметрии в режиме реального времени. По умолчанию на диаграмме отображается высота над уровнем моря, которая меняется с течением времени:

[![Схема телеметрии по высоте над уровнем моря для грузовика](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/trucks-moving-expanded.png#lightbox)

Чтобы просмотреть данные телеметрии высоты над уровнем моря грузовиков, щелкните **Температура** на **панели телеметрии**. Вы можете увидеть, как значение температуры для обоих грузовиков изменялось за последние 15 минут. Вы также увидите, что на панели оповещений активировано предупреждение о низкой температуре для delivery-truck-02.

[![Информационная панель RM с оповещением о низкой температуре](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/low-temp-alert-expanded.png#lightbox)

## <a name="explore-the-data"></a>Просмотр данных

Чтобы определить причину возникновения оповещения о низкой температуре, откройте данные телеметрии грузовика в обозревателе Аналитики временных рядов. Щелкните любую из ссылок **обозревателя Аналитики временных рядов** на панели мониторинга:

[![Панель мониторинга RM с выделенными ссылками TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/explore-tsi-expanded.png#lightbox)

При запуске обозревателя отображается список всех устройств:

[![Исходное представление обозревателя TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/initial-tsi-view-expanded.png#lightbox)

Отфильтруйте устройства, введя в поле фильтра **delivery-truck** и выбрав **температуру** в качестве **меры** на панели слева:

[![Температура грузовика в обозревателе TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/filter-tsi-temp-expanded.png#lightbox)

Вы увидите то же представление, что и на панели удаленного мониторинга. Теперь вы можете приблизить интервал времени, в пределах которого было вызвано предупреждение:

[![Увеличение обозревателя TSI](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-zoom-expanded.png#lightbox)

Кроме того, вы можете также добавить другие потоки данных телеметрии, поступающие от грузовиков. Нажмите кнопку **Добавить** в левом верхнем углу. Появится новая панель:

[![Обозреватель TSI с новой панелью](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-pane-expanded.png#lightbox)

На новой панели измените имя новой метки на **Устройства**, чтобы оно соответствовало предыдущей. Выберите **высоту над уровнем моря** в качестве **меры** и **iothub-connection-device-id** для **разделения по параметрам**, чтобы добавить данные телеметрии высоты над уровнем моря на представление:

[![Обозреватель TSI с температурой и высотой над уровнем моря](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-altitude-expanded.png#lightbox)

## <a name="diagnose-the-alert"></a>Диагностика оповещения

Если взглянуть на потоки в текущем представлении, вы увидите, что профили высоты над уровнем моря для двух грузовиков отличаются. Кроме того, температура в **delivery-truck-02** падает, когда грузовик достигает большой высоты над уровнем моря. Вы удивитесь, узнав, что грузовики должны были пройти по одному и тому же пути.

Чтобы подтвердить свое подозрение, что грузовики выбрали разные пути движения, добавьте другую панель к боковой панели с помощью кнопки **Добавить**. На новой панели измените имя новой метки на **Устройства**, чтобы оно соответствовало предыдущей. Выберите **долготу** в качестве **Меры** и **iothub-connection-device-id** как значение параметра **Split By** (Разделение по), чтобы добавить телеметрию долготы в представление. Вы можете увидеть, что грузовики действительно совершили разные поездки, посмотрев разницу между **долготами** потоков:

[![Обозреватель TSI с температурой, высотой над уровнем моря и долготой](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/tsi-add-longitude-expanded.png#lightbox)

## <a name="create-a-new-rule"></a>Создание нового правила

Маршруты грузовиков обычно оптимизируются заранее. При этом схемы движения транспорта, погода и другие непредсказуемые события могут вызывать задержки, а выбор маршрута до последней минуты остается за водителем, который принимает решение. Но так как температура ресурсов внутри транспортного средства является решающем значением, следует создать дополнительное правило в своем решении для удаленного мониторинга. Это гарантирует отправку предупреждения, если средняя высота превысит 350 футов за одну минуту:

[![Настройка правила для высоты над уровнем моря на вкладке правил решения удаленного мониторинга](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-inline.png)](./media/iot-accelerators-remote-monitoring-root-cause-analysis/new-rule-altitude-expanded.png#lightbox)

Чтобы научиться создавать и изменять правила, ознакомьтесь с предыдущем руководством по [выявлению проблем с устройствами](iot-accelerators-remote-monitoring-automate.md).

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Дополнительная информация

Из этого руководства вы узнали, как с помощью обозревателя Аналитики временных рядов с акселератором решения для удаленного мониторинга диагностировать первопричину предупреждения. Чтобы узнать, как выявлять и устранять проблемы с подключенными устройствами с помощью акселератора решений, перейдите к следующему руководству.

> [!div class="nextstepaction"]
> [Определение и устранение проблем с устройствами, подключенными к решению для мониторинга, с помощью оповещений устройств](iot-accelerators-remote-monitoring-maintain.md)
