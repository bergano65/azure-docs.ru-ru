---
title: Визуализируйте данные Azure IoT Central на панели мониторинга Power BI | Документация Майкрософт
description: Используйте решение Power BI для Azure IoT Central для визуализации и анализа данных IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 06/09/2019
ms.topic: conceptual
ms.openlocfilehash: dcfabbfe5bf807e8820fa14b6b8234d276578dcc
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880076"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard-preview-features"></a>Визуализация и анализ данных IoT Central Azure на панели мониторинга Power BI (предварительные версии функций)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

*Эта статья предназначена для администраторов.*

![Конвейер решения Power BI](media/howto-connect-powerbi-pnp/iot-continuous-data-export.png)

Используйте решение Power BI для Azure IoT Central, чтобы создать мощную панель мониторинга Power BI для отслеживания производительности своих устройств Интернета вещей. С помощью панели мониторинга Power BI можно выполнять следующие действия.
- Отслеживать количество данных, которые отправляют устройства за период времени
- Сравнивать объем данных телеметрии, состояний и событий
- Определять устройства, имеющие множество измерений
- Наблюдать за историческими тенденциями измерений устройств
- Определять проблемные устройства, которые отправляют множество важных событий

Это решение устанавливает конвейер, который принимает данные в учетной записи хранилища BLOB-объектов Azure из [непрерывного экспорта данных](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Этот поток данных проходит через функции Azure, фабрики данных Azure и базы данных SQL Azure для обработки и преобразования данных. Выходные данные можно визуализировать и проанализировать в отчете Power BI, который можно загрузить в виде PBIX-файла. Все эти ресурсы создаются в подписке Azure, поэтому можно настроить каждый компонент в соответствии с потребностями.

## <a name="get-the-power-bi-solution-for-azure-iot-centralhttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>Скачайте [решение Power BI для Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate) с Microsoft AppSource.

## <a name="prerequisites"></a>Предварительные требования
Для настройки решения требуется следующее:
- Доступ к подписке Azure
- Данные, экспортированные из вашего приложения IoT Central с использованием [непрерывного экспорта данных](howto-export-data-blob-storage-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json). Рекомендуется включать измерения, устройства и потоки шаблонов устройств, чтобы максимально использовать панель мониторинга Power BI.
- Power BI Desktop (последняя версия)
- Power BI Pro (если необходимо совместно использовать панели мониторинга с другими пользователями)

## <a name="reports"></a>Отчеты

Два отчета создаются автоматически. 

Первый отчет показывает исторический обзор измерений, сообщенных устройствами, и сортирует различные типы измерений и устройства, которые отправили наибольшее количество измерений.

![Отчет Power BI страница 1](media/howto-connect-powerbi-pnp/template-page1-hasdata.PNG)

Второй отчет детализирует события и показывает исторический обзор ошибок и предупреждений. Он также показывает, какие устройства сообщают о самом большом количестве событий, а также о событиях с ошибкой и с предупреждениями.

![Отчет Power BI страница 2](media/howto-connect-powerbi-pnp/template-page2-hasdata.PNG)

## <a name="architecture"></a>Архитектура
Доступ ко всем созданным ресурсам можно получить через портал Azure. Они будут находиться в одной группе ресурсов.

![Представление группы ресурсов на портале Azure](media/howto-connect-powerbi-pnp/azure-deployment.PNG)

Ниже описаны особенности и способ использования каждого ресурса.

### <a name="azure-functions"></a>Функции Azure
Приложение "Функции Azure" активируется каждый раз, когда в хранилище BLOB-объектов записывается новый файл. Оно извлекает поля из каждого файла измерений, устройств и шаблонов устройств и заполняет несколько промежуточных таблиц SQL для использования в Фабрике данных Azure.

### <a name="azure-data-factory"></a>Фабрика данных Azure
Фабрика данных Azure подключается к базе данных SQL как связанная служба. Она запускает операции хранимой процедуры, которые обрабатывают данные и сохраняют их в таблицах анализа.

### <a name="azure-sql-database"></a>База данных SQL Azure
Эти таблицы создаются автоматически для заполнения стандартных отчетов. Изучив эти схемы в Power BI, вы сможете создавать собственные визуализации этих данных.

| Имя таблицы |
|------------|
|[analytics].[Measurements]|
|[analytics].[Measurements]|
|[stage].[Measurements]|
|[analytics].[Properties]|
|[analytics].[PropertyDefinitions]|
|[analytics].[PropertyDefinitions]|
|[analytics].[Devices]|
|[analytics].[Devices]|
|[dbo].[date]|
|[dbo].[ChangeTracking]|

## <a name="estimated-costs"></a>Расчетная стоимость

Ниже приведена приблизительная стоимость служб Azure (Функции Azure, Фабрика данных, Azure SQL), которые используются в этом процессе. Все цены указаны в долларах США. Цены зависят от региона, поэтому всегда проверяйте наиболее актуальные цены для каждой службы, чтобы узнать фактическую стоимость.
В шаблоне по умолчанию указаны следующие цены (их можно изменить после настройки).

- Функции Azure: План Службы приложений, уровень S1, 74,40 долл. США в месяц.
- Azure SQL, уровень S1, прибл. 30 долл. США в месяц.

Рекомендуем ознакомиться с различными ценовыми категориями и подобрать оптимальный вариант.

## <a name="resources"></a>Ресурсы

Перейдите в Microsoft AppSource и скачайте [решение Power BI для Azure IoT Central](https://aka.ms/iotcentralpowerbisolutiontemplate).

## <a name="next-steps"></a>Следующие шаги

Теперь, когда вы узнали, как визуализировать данные в Power BI, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Управление устройствами в приложении Azure IoT Central](howto-manage-devices-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)