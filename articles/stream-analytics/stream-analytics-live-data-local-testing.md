---
title: Тестирование реальных данных средствами Azure Stream Analytics для Visual Studio
description: Узнайте, как протестировать задание Azure Stream Analytics в локальной среде с помощью потоковой трансляции данных.
services: stream-analytics
author: mamccrea
ms.author: mamccrea
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: ea55d2f96a87503d43a69d288ce85dcff32a39ce
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "61479836"
---
# <a name="test-live-data-locally-using-azure-stream-analytics-tools-for-visual-studio-preview"></a>Тестирование реальных данных в локальной среде с помощью инструментов Azure Stream Analytics для Visual Studio (предварительная версия)

С помощью инструментов Azure Stream Analytics для Visual Studio можно тестировать задания в локальной среде из интегрированной среды разработки с помощью потоковой трансляции событий из концентратора событий Azure, Центра Интернета вещей и хранилища BLOB-объектов. Локальное тестирование реальных данных не заменяет [тестирование производительности и масштабируемости](stream-analytics-streaming-unit-consumption.md), которое можно выполнять в облаке, но вы можете сэкономить время во время функционального тестирования, не отправляя данные в облако каждый раз, когда требуется проверить в задание Stream Analytics. Эта функция находится на этапе предварительной версии и не должна использоваться для производственных рабочих нагрузок.

## <a name="testing-options"></a>Параметры тестирования

Поддерживаются следующие параметры тестирования:

|**Входные данные**  |**Выходные данные**  |**Тип задания**  |
|---------|---------|---------|
|Локальные статические данные   |  Локальные статические данные   |   Облако или Edge |
|Реальные входные данные   |  Локальные статические данные   |   Облако |
|Реальные входные данные   |  Реальные выходные данные   |   Облако |

## <a name="local-testing-with-live-data"></a>Локальное тестирование с помощью реальных данных

1. После создания [облачного проекта Azure Stream Analytics в Visual Studio](stream-analytics-quick-create-vs.md) откройте файл **script.asaql**. При локальном тестировании по умолчанию используются локальные входные и выходные данные.

   ![Локальные входные и выходные данные Azure Stream Analytics в Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-local-input-output.png)

2. Чтобы протестировать реальные данные, из раскрывающегося списка выберите **Use Cloud Input** (Использовать облачные входные данные).

   ![Реальные облачные входные данные Azure Stream Analytics в Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input.png)


3. Задайте **время начала**, чтобы определить, когда задание начнет обработку входных данных. Заданию может потребоваться считать входные данные заранее, чтобы обеспечить точные результаты. Время по умолчанию составляет 30 минут с текущего момента.

   ![Время начала обработки реальных данных Azure Stream Analytics в Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-start-time.png)

4. Щелкните **Run Locally** (Запустить локально). Появится окно консоли, отображающее ход выполнения и метрики задания. Если вы хотите остановить процесс, это можно сделать вручную. 

   ![Окно обработки реальных данных Azure Stream Analytics в Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-process-window.png)

   Выходные данные обновляются каждые три секунды. В окне результатов локального выполнения отображаются первые 500 выходных строк, а выходные файлы помещаются в папку **ASALocalRun**, размещенную по пути к вашему проекту. Выходные файлы можно также открыть, нажав кнопку **Открыть папку результатов** в окне результатов локального выполнения.

   ![Открытие папки результатов обработки реальных данных Azure Stream Analytics в Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-input-open-results-folder.png)

5. Если вы хотите вывести результаты в облачные приемники выходных данных, выберите **Output to Cloud** (Вывод в облако) из второго раскрывающегося списка. Power BI и Azure Data Lake Storage не поддерживаются в качестве приемников выходных данных.

   ![Передача в облако реальных выходных данных Azure Stream Analytics в Visual Studio](./media/stream-analytics-live-data-local-testing/stream-analytics-local-testing-cloud-output.png)
 
## <a name="limitations"></a>Ограничения

* Power BI и Azure Data Lake Storage не поддерживаются в качестве приемников выходных данных из-за ограничений модели аутентификации.

* Для облачных входных данных поддерживаются [политики времени](stream-analytics-out-of-order-and-late-events.md), а для локальных входных данных — нет.

## <a name="next-steps"></a>Дальнейшие действия

* [Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
* [Установка инструментов Azure Stream Analytics для Visual Studio](stream-analytics-tools-for-visual-studio-install.md)
* [Локальное тестирование запросов Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools-local-run.md)
* [Просмотр заданий Azure Stream Analytics с помощью Visual Studio](stream-analytics-vs-tools.md)