---
title: Работа с панелью мониторинга консоли датчика
description: Панель мониторинга позволяет быстро просматривать состояние безопасности сети. Он предоставляет общий обзор угроз для всей системы на временной шкале, а также сведения о связанных устройствах.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 11/03/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 735b1ce4391598d05a1bf0b4486503092f4de37d
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842919"
---
# <a name="the-dashboard"></a>Панель мониторинга

Панель мониторинга позволяет быстро просматривать состояние безопасности сети. Он предоставляет общий обзор угроз для всей системы на временной шкале, а также сведения о связанных устройствах, в том числе:

- Оповещения с разными уровнями серьезности:

- Критические важное

- Значительно

- Дополнительный номер

- Предупреждения

- Два датчика в центре страницы указывают на количество пакетов в секунду (PPS) и неподтвержденных предупреждений (UA). **PPS** — это количество пакетов, подтвержденных системой в секунду. **UA** — это количество предупреждений, которые еще не были подтверждены.

- Список неподтвержденных оповещений с их описанием.

- Временная шкала с описанием предупреждения.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/dashboard-alert-screen-v2.png" alt-text="Панель мониторинга":::

## <a name="viewing-the-latest-alerts"></a>Просмотр последних оповещений

Счетчик неподтвержденных предупреждений (UA) в центре страницы указывает количество таких оповещений. Чтобы просмотреть список оповещений, выберите **больше предупреждений** в нижней части страницы панели мониторинга или щелкните **оповещения** в боковом меню.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unhandled-alerts-list.png" alt-text="Неподтвержденные предупреждения":::

## <a name="status-boxes"></a>Поля состояния

Каждое поле состояния описывается в этом разделе.

| Поле состояния и датчики | Описание |
| -------------- | -------------- |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/critical-alert-status-box-v2.png" alt-text="Критические оповещения"::: | **Критические оповещения** . в поле в верхней части страницы отображается число критических оповещений. Установите этот флажок, чтобы отображать описания оповещений на временной шкале и в списке под датчиками, если таковые имеются.                              |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/major-alert-status-box-v2.png" alt-text="Основные оповещения"::: | **Основные оповещения** . в поле в правом верхнем углу страницы отображается количество основных оповещений. Установите этот флажок, чтобы отображать описания оповещений на временной шкале и в списке под датчиками, если таковые имеются.                                     |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/minor-alert-status-box-v2.png" alt-text="Незначительные предупреждения"::: | **Незначительные предупреждения** . в поле в левом нижнем углу страницы отображается число дополнительных оповещений. Установите этот флажок, чтобы отображать описания оповещений на временной шкале и в списке под датчиками, если таковые имеются.                                   |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/warnings-alert-status-box-v2.png" alt-text="предупреждающие оповещения;"::: | **Предупреждающие оповещения** . поле в нижней части страницы указывает число предупреждений. Установите этот флажок, чтобы отображать описания оповещений на временной шкале и в списке под датчиками, если таковые имеются.                             |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/all-alert-status-box-v2.png" alt-text="Все оповещения."::: | **Все оповещения** . поле в правом нижнем углу страницы указывает общее число критических, основных, дополнительных и предупреждающих оповещений. Установите этот флажок, чтобы отображать описания оповещений на временной шкале и в списке под датчиками, если таковые имеются. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/packets-per-second-gauge-v2.png" alt-text="Пакетов в секунду"::: | **Пакетов в секунду (PPS)** — метрика PPS является индикатором производительности сети. |
| :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unacknowledged-events-gauge-v2.png" alt-text="Неподтвержденные события (UA)"::: | **Неподтвержденные события** . Эта метрика указывает количество неподтвержденных событий.

## <a name="using-the-timeline"></a>Использование временной шкалы

Оповещения отображаются вдоль вертикальной временной шкалы, содержащей сведения о дате и времени.

График графика отображает:

- критические оповещения;

- Основные оповещения

- Незначительные предупреждения

- предупреждающие оповещения;

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/timeline-of-events.png" alt-text="График временной шкалы":::

## <a name="viewing-alerts"></a>Просмотр оповещений

Щелкните **стрелку вниз в нижней** части окна предупреждения, чтобы отобразить запись оповещения и сведения об устройствах.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/extended-alert-screen.png" alt-text="Сведения об элементах и устройствах оповещений":::

- Выберите устройство или **Показать устройства** , чтобы отобразить карту физического режима. Подсвеченные устройства выделены.

- Выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/excel-icon.png" alt-text="Excel"::: , чтобы экспортировать CSV-файл с предупреждением.

- Только администраторы и аналитики безопасности — выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/approve-all-icon.png" alt-text="подтвердить все"::: , чтобы **подтвердить все** связанные оповещения.

- Выберите запись оповещения, чтобы просмотреть тип и описание оповещения:

- Выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pdf-icon.png" alt-text="PDF":::, чтобы скачать отчет о предупреждениях в PDF-файл.

- Выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/pin-icon.png" alt-text="закрепить":::, чтобы закрепить или открепить оповещение.

- Выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/download-icon.png" alt-text="скачать"::: , чтобы исследовать оповещение, загрузив файл ПКАП, содержащий анализ сетевого протокола.

- Выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/cloud-download-icon.png" alt-text="облако"::: , чтобы скачать отфильтрованный файл ПКАП, содержащий только пакеты, относящиеся к предупреждениям, таким образом уменьшая размер выходного файла и обеспечивая более тщательное анализ. Его можно просмотреть с помощью [Wireshark](https://www.wireshark.org/).

- Выберите :::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/navigate-icon.png" alt-text="навигацию"::: , чтобы перейти к временной шкале события во время выполнения запрошенного оповещения.

- Администраторы и аналитики безопасности — измените состояние предупреждения с неподтвержденного на подтвержденное. Выберите "сведения", чтобы утвердить обнаруженные действия.

:::image type="content" source="media/how-to-work with-the-sensor-console-dashboard/unauthorized-internet-connectivity-detection-v3.png" alt-text="Обнаружены несанкционированные подключения к Интернету":::

## <a name="see-also"></a>См. также раздел

[Работа с оповещениями на датчике](how-to-work-with-alerts-on-your-sensor.md)
