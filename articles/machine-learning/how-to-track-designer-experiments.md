---
title: Метрики журнала в конструкторе (Предварительная версия)
titleSuffix: Azure Machine Learning
description: Отслеживайте эксперименты в конструкторе машинного обучения Azure. Включите ведение журнала с помощью модуля выполнение скрипта Python и просмотрите зарегистрированные результаты в студии.
services: machine-learning
author: likebupt
ms.author: keli19
ms.reviewer: peterlu
ms.service: machine-learning
ms.subservice: core
ms.date: 07/30/2020
ms.topic: conceptual
ms.custom: how-to
ms.openlocfilehash: 20845a6f1238095b40c9b05b5f5d8d85217b6db5
ms.sourcegitcommit: e69bb334ea7e81d49530ebd6c2d3a3a8fa9775c9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/27/2020
ms.locfileid: "88950414"
---
# <a name="enable-logging-in-azure-machine-learning-designer-preview-pipelines"></a>Включение ведения журнала в конвейерах Машинное обучение Azure конструктора (Предварительная версия)
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как добавить код ведения журнала в конвейеры конструктора. Вы также узнаете, как просматривать эти журналы с помощью веб-портала Машинное обучение Azure Studio.

Дополнительные сведения о регистрации метрик с помощью интерфейса SDK см. в статье [мониторинг запуска и метрик эксперимента машинного обучения Azure](how-to-track-experiments.md).

## <a name="enable-logging-with-execute-python-script"></a>Включение ведения журнала с помощью скрипта Python

Используйте модуль __выполнение скрипта Python__ , чтобы включить ведение журнала в конвейерах конструктора. Хотя вы можете записать любое значение с помощью этого рабочего процесса, особенно полезно записывать метрики из модуля " __Анализ модели__ " для мониторинга производительности модели во время выполнения.

В следующем примере показано, как заносить в журнал среднюю квадратную ошибку двух обученных моделей с помощью модулей «оценка модели» и «выполнение скриптов Python».

1. Подключите модуль __выполнения скрипта Python__ к выходным данным модуля __Анализ модели__ .

    ![Подключение модуля "Выполнение скриптов Python" к выходу модуля "Анализ модели"](./media/how-to-track-experiments/designer-logging-pipeline.png)

1. Вставьте следующий код в редактор кода __выполнения скрипта Python__ , чтобы зарегистрировать среднюю абсолютную ошибку для обученной модели. Аналогичный шаблон можно использовать для записи любого другого значения в конструктор:

    ```python
    # dataframe1 contains the values from Evaluate Model
    def azureml_main(dataframe1=None, dataframe2=None):
        print(f'Input pandas.DataFrame #1: {dataframe1}')
    
        from azureml.core import Run
    
        run = Run.get_context()
    
        # Log the mean absolute error to the parent run to see the metric in the run details page.
        # Note: 'run.parent.log()' should not be called multiple times because of performance issues.
        # If repeated calls are necessary, cache 'run.parent' as a local variable and call 'log()' on that variable.

        # Log left output port result of Evaluate Model. This also works when evaluate only 1 model.
        run.parent.log(name='Mean_Absolute_Error (left port)', value=dataframe1['Mean_Absolute_Error'][0])

        # Log right output port result of Evaluate Model.
        run.parent.log(name='Mean_Absolute_Error (right port)', value=dataframe1['Mean_Absolute_Error'][1])
    
        return dataframe1,
    ```
    
В этом коде для записи значений в журнал используется пакет SDK для Машинное обучение Azure Python. Он использует Run. get_context () для получения контекста текущего выполнения. Затем он записывает значения в этот контекст с помощью метода Run. Parent. log (). Он использует `parent` для записи значений в родительский запуск конвейера, а не для запуска модуля.

Дополнительные сведения о том, как использовать пакет SDK для Python для записи значений в журнал, см. [в разделе Включение ведения журнала в учебных курсах машинного обучения Azure](how-to-track-experiments.md).

## <a name="view-logs"></a>Просмотр журналов

После завершения выполнения конвейера на странице эксперименты можно увидеть *Mean_Absolute_Error* .

1. Перейдите к разделу **эксперименты** .
1. Выберите свой эксперимент.
1. Выберите Запуск в эксперименте, который требуется просмотреть.
1. Выберите **Метрики**.

    ![Просмотр метрик выполнения в студии](./media/how-to-track-experiments/experiment-page-metrics-across-runs.png)

## <a name="next-steps"></a>Следующие шаги

В этой статье вы узнали, как использовать журналы в конструкторе. Дальнейшие действия см. в соответствующих статьях:

* Сведения об устранении неполадок конвейеров конструктора см. в разделе [отладка & устранение неполадок конвейеров машинного обучения](how-to-debug-pipelines.md#logging-in-azure-machine-learning-designer-preview).
* Узнайте, как использовать пакет SDK для Python для записи метрик в процесс создания пакета SDK. см. раздел [Включение ведения журнала в Azure ML Training](how-to-track-experiments.md).
