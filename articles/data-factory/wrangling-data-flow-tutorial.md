---
title: Начало работы с потоком данных в Azure Data Factory
description: Учебник о том, как готовить данные в Azure Data Factory с помощью потока данных споры
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409011"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Подготовка данных с помощью потока данных, наносимых спорыми

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Создание потока потоков данных споры

Существует два способа создания потока данных в Azure Data Factory. Один из способов заключается в том, чтобы нажать значок плюс и выбрать **поток данных** в панели ресурсов завода.

![Пререканий](media/wrangling-data-flow/tutorial7.png)

Другой метод находится в панели действий холста конвейера. Откройте аккордеон **Move and Transform** и перетащите активность потока **данных** на холст.

В обоих методах, в боковой панели, которая открывается, выберите **Создать новый поток данных** и выбрать **поток данных Wrangling.** Щелкните ОК.

![Пререканий](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Автор спорящий поток данных

Добавьте **набор исходных данных** для потока данных. Можно либо выбрать существующий набор данных, либо создать новый. Вы также можете выбрать набор данных раковины. Вы можете выбрать один или несколько наборов исходных данных, но в это время допускается только одна раковина. Выбор набора данных раковины является необязательным, но требуется по крайней мере один набор исходных данных.

> [!NOTE]
> Только ADLS Gen 2 Делированный текст поддерживается для ограниченного предварительного просмотра. 

![Пререканий](media/wrangling-data-flow/tutorial4.png)

Нажмите **Создать,** чтобы открыть Power Query Online mashup редактор.

![Пререканий](media/wrangling-data-flow/tutorial5.png)

Автор свой спорный поток данных с помощью подготовки данных без кода. Для списка доступных функций [см.](wrangling-data-flow-functions.md)/

![Пререканий](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Запуск и мониторинг потока данных споры

Чтобы выполнить отладку конвейера потока данных, нажмите **"Отладка"** в холсте конвейера. После публикации потока данных **Триггер теперь** выполняет запуск последнего опубликованного конвейера по требованию. Потоки данных могут быть запланированы со всеми существующими триггерами Azure Data Factory.

![Пререканий](media/wrangling-data-flow/tutorial3.png)

Перейдите на вкладку **Monitor,** чтобы визуализировать выход срабатывающего действия потока данных.

![Пререканий](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Дальнейшие действия

Узнайте, как [создать поток картографических данных.](tutorial-data-flow.md)