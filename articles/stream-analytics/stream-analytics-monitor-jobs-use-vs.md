---
title: Мониторинг и управление Azure Stream Analytics с помощью Visual Studio
description: В этой статье объясняется, как с помощью Visual Studio отслеживать задания Azure Stream Analytics и управлять ими.
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: how-to
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: a151d4ce5a2f9860c3b7919496a2edd25cc58ede
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/30/2020
ms.locfileid: "93123631"
---
# <a name="monitor-and-manage-stream-analytics-jobs-with-visual-studio"></a>Мониторинг заданий Stream Analytics и управление ими с помощью Visual Studio

В этой статье рассказывается, как отслеживать задания Stream Analytics в Visual Studio. Инструменты Azure Stream Analytics для Visual Studio обеспечивают возможности мониторинга, похожие на возможности на портале Azure, без необходимости покидать среду IDE. Вы можете начать отслеживать задание, как только **отправите его в Azure** из вашего файла **Script.asaql** , или вы можете отслеживать существующие задания независимо от того, как они были созданы. 

## <a name="job-summary"></a>Сводные данные задания

Вкладки **Сводка по заданию** и **Метрики задания** предоставляют моментальный снимок вашего задания. Вы можете быстро определить состояние задания и информацию о событии.

<img src="./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-job-summary-metrics.png" alt="Stream Analytics job summary and job metrics" width="300px"/> 


## <a name="job-metrics"></a>Метрики задания

Вы можете свернуть вкладку **Сводка по заданию** и щелкнуть вкладку **Метрики задания** , чтобы просмотреть график с важными метриками. Выберите или отмените выбор типов метрик, чтобы добавить метрики в график либо удалить их из него.

![Метрики Azure Stream Analytics в Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-metrics.png)


## <a name="error-monitoring"></a>Отслеживание ошибок

Вы также можете отслеживать ошибки, щелкнув вкладку **​​Ошибки** .

![Ошибки Azure Stream Analytics в Visual Studio](./media/stream-analytics-monitor-jobs-use-vs/stream-analytics-vs-errors.png)


## <a name="get-support"></a>Получение поддержки
Для получения дополнительной помощи воспользуйтесь [страницей вопросов и ответов о Microsoft Azure Stream Analytics](/answers/topics/azure-stream-analytics.html). 

## <a name="next-steps"></a>Дальнейшие действия
* [Введение в Azure Stream Analytics](stream-analytics-introduction.md)
* [Краткое руководство. Создание задания Stream Analytics с использованием инструментов Azure Stream Analytics для Visual Studio](stream-analytics-quick-create-vs.md)
* [Установка инструментов Azure Stream Analytics для Visual Studio](stream-analytics-tools-for-visual-studio-install.md)