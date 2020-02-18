---
title: Параметры преобразования конструктора Azure Monitor представлений в книги
description: ''
author: austonli
ms.author: aul
ms.service: azure-monitor
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f04ebc1a4a53825709479ca3f1dc7ce1245fc67f
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77171557"
---
# <a name="azure-monitor-view-designer-to-workbooks-conversion-options"></a>Параметры преобразования конструктора Azure Monitor представлений в книги
[Конструктор представлений](view-designer.md) — это функция Azure Monitor, которая позволяет создавать пользовательские представления, помогающие визуализировать данные в рабочей области log Analytics, с диаграммами, списками и временными шкалами. Они выводятся и заменяются книгами, которые предоставляют дополнительные функциональные возможности. В этой статье сравниваются фундаментальные понятия между двумя вариантами и преобразования представлений в книги.

## <a name="basic-workbook-designs"></a>Основные макеты книг

Конструктор представлений имеет фиксированный статический стиль представления, а книги позволяют свободно включать и изменять способ представления данных. На рисунках ниже показаны два примера того, как можно упорядочивать книги при преобразовании представлений.

[Вертикальная книга](view-designer-conversion-examples.md#vertical)
![вертикальный](media/view-designer-conversion-options/view-designer-vertical.png)

[Книга с вкладками](view-designer-conversion-examples.md#tabbed) ![
вкладка "распределение типов данных"](media/view-designer-conversion-options/distribution-tab.png)
![типов данных на вкладке "время"](media/view-designer-conversion-options/over-time-tab.png)

## <a name="tile-conversion"></a>Преобразование плиток
Конструктор представлений использует функцию плитки с обзором для представления и суммирования общего состояния. Они представлены в виде семи плиток, от чисел до диаграмм. В книгах пользователи могут создавать аналогичные визуализации и закреплять их в исходном стиле плиток обзора. 

![Коллекции](media/view-designer-conversion-options/overview.png)


## <a name="view-dashboard-conversion"></a>Просмотр преобразования панели мониторинга
Плитки конструктора представлений обычно состоят из двух разделов: визуализации и списка, которые соответствуют данным из визуализации, например плитку **кольца & списка** .

![Кольцо](media/view-designer-conversion-options/donut-example.png)

С помощью книг можно разрешить пользователю выбрать один или оба раздела представления. Запросы составления в книгах являются простым процессом в два этапа. Во-первых, данные формируются из запроса, а во-вторых, данные подготавливаются к просмотру как визуализация.  Ниже приведен пример того, как это представление будет воссоздано в книгах.

![Convert](media/view-designer-conversion-options/convert-donut.png)


## <a name="next-steps"></a>Дальнейшие действия
- [Доступ к книгам & разрешения](view-designer-conversion-access.md)