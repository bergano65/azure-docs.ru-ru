---
title: Визуализируйте данные Azure IoT Central на панели мониторинга Power BI | Документация Майкрософт
description: Используйте шаблон решения Azure IoT Central Analytics Power BI для визуализации и анализа данных IoT Central.
ms.service: iot-central
services: iot-central
author: viv-liu
ms.author: viviali
ms.date: 07/16/2018
ms.topic: conceptual
ms.openlocfilehash: 6f8bed8aba43d77cdfad49f4fe62a4c7aa2c5ce3
ms.sourcegitcommit: ce526d13cd826b6f3e2d80558ea2e289d034d48f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2018
ms.locfileid: "46368075"
---
# <a name="visualize-and-analyze-your-azure-iot-central-data-in-a-power-bi-dashboard"></a>Визуализация и анализ данных Azure IoT Central на панели мониторинга Power BI

*Этот раздел предназначен для администраторов.*

![Конвейер шаблона решения Power BI](media/howto-connect-powerbi/iot-continuous-data-export.png)

Используйте шаблон решения Azure IoT Central Analytics Power BI для создания мощной панель мониторинга Power BI для мониторинга производительности устройств Интернета вещей. С помощью панели мониторинга Power BI можно выполнять следующие действия.
- Отслеживать количество данных, которые отправляют устройства за период времени
- Сравнивать объем данных телеметрии, состояний и событий
- Определять устройства, имеющие множество измерений
- Наблюдать за историческими тенденциями измерений устройств
- Определять проблемные устройства, которые отправляют множество важных событий

Этот шаблон решения устанавливает конвейер, который принимает данные в учетной записи хранения больших двоичных объектов Azure из [непрерывного экспорта данных](howto-export-data.md). Этот поток данных проходит через функции Azure, фабрики данных Azure и базы данных SQL Azure для обработки и преобразования данных. Выходные данные можно визуализировать и проанализировать в отчете Power BI, который можно загрузить в виде PBIX-файла. Все эти ресурсы создаются в подписке Azure, поэтому можно настроить каждый компонент в соответствии с потребностями. Этот шаблон решения полностью открыт, поэтому можно получить дополнительные сведения об архитектуре и расширить решение, посетив [репозиторий GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="get-the-azure-iot-central-analytics-solution-templatehttpsakamsiotcentralpowerbisolutiontemplate-from-microsoft-appsource"></a>[Шаблон решения аналитики Azure IoT Central Analytics от Microsoft AppSource](https://aka.ms/iotcentralpowerbisolutiontemplate)

## <a name="prerequisites"></a>Предварительные требования
Для настройки шаблона требуется следующее.
- Доступ к подписке Azure
- Данные экспортированы с использованием [непрерывного экспорта данных](howto-export-data.md) из вашего приложения IoT Central. Рекомендуется включать измерения, устройства и потоки шаблонов устройств, чтобы максимально использовать панель мониторинга Power BI.
- Power BI Desktop (последняя версия)
- Power BI Pro (если необходимо совместно использовать панели мониторинга с другими пользователями)

## <a name="reports"></a>Отчеты

Два отчета создаются автоматически. 

Первый отчет показывает исторический обзор измерений, сообщенных устройствами, и сортирует различные типы измерений и устройства, которые отправили наибольшее количество измерений.

![Отчет Power BI страница 1](media/howto-connect-powerbi/template-page1-hasdata.PNG)

Второй отчет детализирует события и показывает исторический обзор ошибок и предупреждений. Он также показывает, какие устройства сообщают о самом большом количестве событий, а также о событиях с ошибкой и с предупреждениями.

![Отчет Power BI страница 2](media/howto-connect-powerbi/template-page2-hasdata.PNG)

## <a name="resources"></a>Ресурсы

Посетите AppSource, чтобы получить [шаблон решения Azure IoT Central Analytics](https://aka.ms/iotcentralpowerbisolutiontemplate).

Дополнительные сведения об архитектуре и расширении решения см. в [репозитории GitHub](https://aka.ms/iotcentralgithubpowerbisolutiontemplate).

## <a name="next-steps"></a>Дополнительная информация

Теперь, когда вы узнали, как визуализировать данные в Power BI, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Управление устройствами в приложении Azure IoT Central](howto-manage-devices.md)
