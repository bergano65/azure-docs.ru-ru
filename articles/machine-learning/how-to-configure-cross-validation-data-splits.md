---
title: Настройка перекрестной проверки и разбиения данных в автоматизированных экспериментах машинного обучения
titleSuffix: Azure Machine Learning
description: Узнайте, как настроить перекрестную проверку и разбиение набора данных для автоматизированных экспериментов машинного обучения.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.custom: how-to
ms.author: cesardl
author: CESARDELATORRE
ms.reviewer: nibaccam
ms.date: 06/16/2020
ms.openlocfilehash: 7a7b603efe376250607b4a48ff3ef2833f40a2bd
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89650728"
---
# <a name="configure-data-splits-and-cross-validation-in-automated-machine-learning"></a>Настройка разбиения данных и перекрестной проверки в автоматизированном машинном обучении

В этой статье вы узнаете о различных вариантах настройки разбиения данных для обучения и проверки, а также перекрестной проверки для автоматизированного машинного обучения, Аутомл и экспериментов.

В Машинное обучение Azure при использовании Аутомл для создания нескольких моделей машинного обучения каждому дочернему выполнению необходимо проверить связанную модель, вычисляя метрики качества для этой модели, например точность или AUC с весом. Эти метрики рассчитываются путем сравнения прогнозов, производимых с каждой моделью, с реальными метками прошлых наблюдений в данных проверки. 

Эксперименты Аутомл выполняют проверку модели автоматически. В следующих разделах описано, как можно дополнительно настроить параметры проверки с помощью [пакета SDK для машинное обучение Azure Python](https://docs.microsoft.com/python/api/overview/azure/ml/?view=azure-ml-py&preserve-view=true). 

Сведения о низком коде или работе без кода см. [в статье Создание автоматических экспериментов машинного обучения в машинное обучение Azure Studio](how-to-use-automated-ml-for-ml-models.md). 

> [!NOTE]
> В настоящее время Studio поддерживает разбиение данных для обучения и проверки, а также параметры перекрестной проверки, но не поддерживает указание отдельных файлов данных для набора проверки. 

## <a name="prerequisites"></a>Предварительные требования

Для этой статьи требуется:

* Рабочая область машинного обучения Azure. Дополнительные сведения см. в [инструкциях по созданию рабочей области Машинного обучения Azure](how-to-manage-workspace.md).

* Знакомство с настройкой автоматизированного эксперимента машинного обучения с помощью пакета SDK для Машинное обучение Azure. Следуйте инструкциям [учебника](tutorial-auto-train-models.md) или [практического руководства](how-to-configure-auto-train.md), чтобы ознакомиться с основными конструктивными шаблонами экспериментов автоматизированного машинного обучения.

* Анализ данных о перекрестной проверке и обучении и проверке разбивается как основные понятия ML. Подробное описание

    * [О обучении, проверке и тестовых наборах в Машинное обучение](https://towardsdatascience.com/train-validation-and-test-sets-72cb40cba9e7)

    * [Основные сведения о перекрестной проверке](https://towardsdatascience.com/understanding-cross-validation-419dbd47e9bd)

## <a name="default--data-splits-and-cross-validation"></a>Разбиение данных по умолчанию и перекрестная проверка

Используйте объект [аутомлконфиг](https://docs.microsoft.com/python/api/azureml-train-automl-client/azureml.train.automl.automlconfig.automlconfig?view=azure-ml-py&preserve-view=true) для определения параметров эксперимента и обучения. В следующем фрагменте кода обратите внимание, что определены только обязательные параметры, которые являются параметрами для `n_cross_validation` или `validation_ data` **не** включены.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'Class'
                            )
```

Если параметр или не задан явным образом `validation_data` `n_cross_validation` , аутомл применяет методы по умолчанию в зависимости от числа строк в указанном наборе данных `training_data` :

|&nbsp;Размер данных для обучения &nbsp;| Метод проверки |
|---|-----|
|**Более &nbsp; &nbsp; 20 000 &nbsp; строк**| Применяется разбиение данных по обучению и проверке. Значение по умолчанию — использовать 10% начального набора данных для обучения в качестве набора проверки. В свою очередь, этот набор проверки используется для вычисления метрик.
|**Меньше &nbsp; &nbsp; 20 000 &nbsp; строк**| Применяется подход перекрестной проверки. Количество сверток по умолчанию зависит от количества строк. <br> **Если набор данных имеет менее 1 000 строк**, то используется 10 сверток. <br> **Если строки находятся в диапазоне от 1 000 до 20 000**, то используются три свертывания.

## <a name="provide-validation-data"></a>Предоставление данных проверки

В этом случае можно либо начать с одного файла данных и разделить его на обучающие и проверочные наборы, либо предоставить отдельный файл данных для набора проверки. В любом случае `validation_data` параметр в `AutoMLConfig` объекте назначает, какие данные следует использовать в качестве набора проверки. Этот параметр принимает только наборы данных в форме [машинное обучение Azureного набора данных](how-to-create-register-datasets.md) или Pandas.   

В следующем примере кода явно определяется, какая часть предоставленных данных в `dataset` используется для обучения и проверки.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

training_data, validation_data = dataset.random_split(percentage=0.8, seed=1)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = training_data,
                             validation_data = validation_data,
                             label_column_name = 'Class'
                            )
```

## <a name="provide-validation-set-size"></a>Задать размер набора проверки

В этом случае для эксперимента предоставляется только один набор данных. То есть `validation_data` параметр **не** указан, а предоставленный набор данных присваивается  `training_data` параметру.  В `AutoMLConfig` объекте можно задать `validation_size` параметр для удержания части обучающих данных для проверки. Это означает, что проверочный набор будет разбиваться по Аутомл из первоначально `training_data` предоставленного. Это значение должно находиться в диапазоне от 0,0 до 1,0, не включительно (например, 0,2 означает, что 20% данных будут храниться для данных проверки).

См. Следующий пример кода:

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             validation_size = 0.2,
                             label_column_name = 'Class'
                            )
```

## <a name="set-the-number-of-cross-validations"></a>Задание числа перекрестных проверок

Чтобы выполнить перекрестную проверку, включите `n_cross_validations` параметр и присвойте ему значение. Этот параметр задает количество выполняемых перекрестных проверок на основе того же числа сверток.

В следующем коде определяется пять сверток для перекрестной проверки. Таким образом, пять различных обученияов, каждое обучение с использованием 4/5 данных и каждая проверка с использованием 1/5 данных с разными контрольными данными каждый раз.

В результате метрики рассчитываются со средним значением 5 метрик проверки.

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/creditcard.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             n_cross_validations = 5
                             label_column_name = 'Class'
                            )
```

## <a name="specify-custom-cross-validation-data-folds"></a>Выбор нестандартных данных для перекрестной проверки

Вы также можете предоставить собственные данные перекрестной проверки (ОПС). Это считается более сложным сценарием, поскольку вы указываете, какие столбцы следует разделить и использовать для проверки.  Включите настраиваемые столбцы с разделением КП в обучающие данные и укажите столбцы, заполнив имена столбцов в `cv_split_column_names` параметре. Каждый столбец представляет одну разбивку перекрестной проверки и заполняется целыми значениями 1 или 0, где 1 означает, что строка должна использоваться для обучения, а значение 0 указывает, что строка должна использоваться для проверки.

В следующем фрагменте кода содержатся данные по маркетинговым маркетингу с двумя столбцами разбиения на столбцы "cv1" и "CV2".

```python
data = "https://automlsamplenotebookdata.blob.core.windows.net/automl-sample-notebook-data/bankmarketing_with_cv.csv"

dataset = Dataset.Tabular.from_delimited_files(data)

automl_config = AutoMLConfig(compute_target = aml_remote_compute,
                             task = 'classification',
                             primary_metric = 'AUC_weighted',
                             training_data = dataset,
                             label_column_name = 'y',
                             cv_split_column_names = ['cv1', 'cv2']
                            )
```

> [!NOTE]
> Чтобы использовать `cv_split_column_names` с `training_data` и `label_column_name` , обновите пакет SDK для машинное обучение Azure Python версии 1.6.0 или более поздней. Сведения о предыдущих версиях пакета SDK см. в разделе Использование `cv_splits_indices` , но обратите внимание, что он используется `X` только с `y` входными данными набора данных и. 

## <a name="next-steps"></a>Дальнейшие действия

* [Предотвращение несбалансированных данных и перегонка](concept-manage-ml-pitfalls.md).
* [Руководство. Использование автоматизированного машинного обучения для прогнозирования годуного в такси фрагмента данных](tutorial-auto-train-models.md#split-the-data-into-train-and-test-sets).
* [Автоматическое обучение модели прогнозирования временных рядов](how-to-auto-train-forecast.md).
