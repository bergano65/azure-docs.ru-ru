---
title: Руководство 2. Автоматическое машинное обучение модели регрессии с помощью службы "Машинное обучение Azure"
description: Узнайте, как создать модель ML с помощью автоматического машинного обучения.  Машинное обучение Azure может автоматически выполнять предварительную обработку данных, а также выбор алгоритма и гиперпараметров. После этого окончательная модель развертываться с помощью службы "Машинное обучение Azure".
services: machine-learning
ms.service: machine-learning
ms.component: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.date: 12/04/2018
ms.openlocfilehash: 0c4b9c31c4ae8a6a7a7044887c9af051966c745e
ms.sourcegitcommit: b0f39746412c93a48317f985a8365743e5fe1596
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52879537"
---
# <a name="tutorial-2-train-a-regression-model-with-automated-machine-learning"></a>Руководство 2. Автоматическое машинное обучение модели регрессии

Это руководство представляет собой **вторую часть серии, состоящей из двух частей**. Из предыдущего руководства вы узнали, как [подготовить данные о такси в Нью-Йорке для моделирования с применением регрессии](tutorial-data-prep.md).

Теперь все готово для создания модели с помощью службы "Машинное обучение Azure". В этой части руководства вы используете подготовленные данные и автоматически создадите модель регрессии для прогнозирования тарифов на такси. Используя возможности автоматического машинного обучения службы, вы определяете для себя цели и ограничения машинного обучения, запускаете процесс автоматического машинного обучения, а затем разрешаете выбор алгоритма и настройку гиперпараметров. Метод автоматического машинного обучения позволяет выполнить итерацию множества сочетаний алгоритмов и гиперпараметров, пока не будет найдена лучшая модель на основе выбранных критериев.

![Схема процесса](./media/tutorial-auto-train-models/flow2.png)

Из этого руководства вы узнаете, как выполнять следующие задачи:

> [!div class="checklist"]
> * Настройка среды Python и импорт пакетов SDK.
> * Настройка рабочей области службы "Машинное обучение Azure".
> * Автоматическое обучение модели регрессии.
> * Локальный запуск модели с помощью пользовательских параметров.
> * Просмотр результатов.
> * Регистрация наилучшей модели.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://aka.ms/AMLfree), прежде чем начинать работу.

>[!NOTE]
> Код в этой статье протестирован с использованием пакета SDK для службы "Машинное обучение Azure" версии 1.0.0


## <a name="prerequisites"></a>Предварительные требования

> * [Выполнение руководства по подготовке данных](tutorial-data-prep.md).
> * Настроенная среда автоматического машинного обучения, например записные книжки Azure, локальная среда Python или Виртуальная машина для обработки и анализа данных. [Настройте](samples-notebooks.md) автоматическое машинное обучение.

## <a name="get-the-notebook"></a>Получение записной книжки

Для удобства это руководство доступно в формате [Jupyter Notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/tutorials/regression-part2-automated-ml.ipynb). Запустите записную книжку `regression-part2-automated-ml.ipynb` в Записных книжках Azure или на своем сервере Jupyter Notebook.

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-in-azure-notebook.md)]

## <a name="import-packages"></a>Импорт пакетов
Импортируйте пакеты Python, которые необходимы в этом руководстве.


```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
from azureml.train.automl.run import AutoMLRun
import time
import logging

```

## <a name="configure-workspace"></a>Настройка рабочей области

В существующей рабочей области создайте объект. `Workspace` является классом, который принимает сведения о подписке и ресурсах Azure, а также создает облачный ресурс для мониторинга и отслеживания процесса выполнения модели. `Workspace.from_config()` считывает файл **aml_config/config.json** и загружает данные в объект `ws`.  `ws` используется в остальном коде в этом руководстве.

Получив объект рабочей области, укажите имя эксперимента, а также создайте локальный каталог и зарегистрируйте его в рабочей области. Все выполнения указанного эксперимента записываются в журнал под его именем и на [портале Azure](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

import os

output = {}
output['SDK version'] = azureml.core.VERSION
output['Subscription ID'] = ws.subscription_id
output['Workspace'] = ws.name
output['Resource Group'] = ws.resource_group
output['Location'] = ws.location
output['Project Directory'] = project_folder
pd.set_option('display.max_colwidth', -1)
pd.DataFrame(data=output, index=['']).T
```

## <a name="explore-data"></a>Изучение данных

Используйте объект потока данных, созданный в рамках работы с предыдущим руководством. Откройте и выполните поток данных, а затем проверьте результаты.


```python
import azureml.dataprep as dprep
package_saved = dprep.Package.open(".\dflow")
dflow_prepared = package_saved.dataflows[0]
dflow_prepared.get_profile()
```




<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>type</th>
      <th>Min</th>
      <th>Max</th>
      <th>Count</th>
      <th>Отсутствующее количество</th>
      <th>Присутствующее количество</th>
      <th>Процент отсутствующих</th>
      <th>Количество ошибок</th>
      <th>Количество пустых</th>
      <th>0,1 % квантилей</th>
      <th>1 % квантилей</th>
      <th>5 % квантилей</th>
      <th>25 % квантилей</th>
      <th>50 % квантилей</th>
      <th>75 % квантилей</th>
      <th>95 % квантилей</th>
      <th>99 % квантилей</th>
      <th>99,9 % квантилей</th>
      <th>Среднее значение</th>
      <th>стандартное отклонение.</th>
      <th>Variance</th>
      <th>Асимметрия</th>
      <th>Эксцесс</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>vendor</th>
      <td>FieldType.STRING</td>
      <td>1</td>
      <td>VTS</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_weekday</th>
      <td>FieldType.STRING</td>
      <td>Пятница</td>
      <td>Среда</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>3,57523</td>
      <td>3</td>
      <td>9,91106</td>
      <td>15,9327</td>
      <td>19</td>
      <td>22,0225</td>
      <td>23</td>
      <td>23</td>
      <td>14,2326</td>
      <td>6,34926</td>
      <td>40,3131</td>
      <td>-0,693335</td>
      <td>-0,459336</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,32313</td>
      <td>4,92308</td>
      <td>14,2214</td>
      <td>29,5244</td>
      <td>44,6436</td>
      <td>56,3767</td>
      <td>58,9798</td>
      <td>59</td>
      <td>29,4635</td>
      <td>17,4396</td>
      <td>304,14</td>
      <td>0,00440324</td>
      <td>-1,20458</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>4,99286</td>
      <td>4,91954</td>
      <td>14,6121</td>
      <td>29,9239</td>
      <td>44,5221</td>
      <td>56,6792</td>
      <td>59</td>
      <td>59</td>
      <td>29,6225</td>
      <td>17,3868</td>
      <td>302,302</td>
      <td>-0,0227466</td>
      <td>-1,19409</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Пятница</td>
      <td>Среда</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>dropoff_hour</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>23</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>3,23217</td>
      <td>2,93333</td>
      <td>9,92334</td>
      <td>15,9135</td>
      <td>19</td>
      <td>22,2739</td>
      <td>23</td>
      <td>23</td>
      <td>14,1815</td>
      <td>6,45578</td>
      <td>41,677</td>
      <td>-0,691001</td>
      <td>-0,500215</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,1064</td>
      <td>5</td>
      <td>14,2051</td>
      <td>29,079</td>
      <td>44,2937</td>
      <td>56,6338</td>
      <td>58,9984</td>
      <td>59</td>
      <td>29,353</td>
      <td>17,4241</td>
      <td>303,598</td>
      <td>0,0142562</td>
      <td>-1,21531</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,03373</td>
      <td>5</td>
      <td>14,7471</td>
      <td>29,598</td>
      <td>45,3216</td>
      <td>56,1044</td>
      <td>58,9728</td>
      <td>59</td>
      <td>29,7923</td>
      <td>17,481</td>
      <td>305,585</td>
      <td>-0,0281313</td>
      <td>-1,21965</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Нет</td>
      <td>Да</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
      <td></td>
    </tr>
    <tr>
      <th>pickup_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0782</td>
      <td>-73,7365</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,0763</td>
      <td>-73,9625</td>
      <td>-73,9629</td>
      <td>-73,949</td>
      <td>-73,9279</td>
      <td>-73,8667</td>
      <td>-73,8304</td>
      <td>-73,8232</td>
      <td>-73,7698</td>
      <td>-73,9139</td>
      <td>0,0487111</td>
      <td>0,00237277</td>
      <td>0,402697</td>
      <td>-0,613516</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5755</td>
      <td>40,8799</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,6329</td>
      <td>40,7131</td>
      <td>40,7116</td>
      <td>40,7214</td>
      <td>40,7581</td>
      <td>40,8051</td>
      <td>40,8489</td>
      <td>40,8676</td>
      <td>40,8777</td>
      <td>40,7652</td>
      <td>0,0483485</td>
      <td>0,00233758</td>
      <td>0,228088</td>
      <td>-0,598862</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0857</td>
      <td>-73,7209</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>-74,0788</td>
      <td>-73,9856</td>
      <td>-73,9858</td>
      <td>-73,959</td>
      <td>-73,9367</td>
      <td>-73,8848</td>
      <td>-73,8155</td>
      <td>-73,7767</td>
      <td>-73,7335</td>
      <td>-73,9207</td>
      <td>0,055961</td>
      <td>0,00313163</td>
      <td>0,648649</td>
      <td>0,0229141</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5835</td>
      <td>40,8797</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,5977</td>
      <td>40,6954</td>
      <td>40,6951</td>
      <td>40,7275</td>
      <td>40,7582</td>
      <td>40,7884</td>
      <td>40,8504</td>
      <td>40,868</td>
      <td>40,8786</td>
      <td>40,7595</td>
      <td>0,0504621</td>
      <td>0,00254642</td>
      <td>0,0484179</td>
      <td>-0,0368799</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>1</td>
      <td>5</td>
      <td>5</td>
      <td>6</td>
      <td>6</td>
      <td>2,32979</td>
      <td>1,79978</td>
      <td>3,2392</td>
      <td>0,834099</td>
      <td>-1,11111</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>444</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>7059,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>3,01808</td>
      <td>3,0125</td>
      <td>5,91545</td>
      <td>9,49055</td>
      <td>16,5816</td>
      <td>33,5638</td>
      <td>51,9924</td>
      <td>81,1368</td>
      <td>12,9112</td>
      <td>11,6447</td>
      <td>135,599</td>
      <td>8,6842</td>
      <td>269,818</td>
    </tr>
  </tbody>
</table>

Чтобы подготовить данные для эксперимента, добавьте столбцы в `dflow_x` (таким образом они будут считаться признаками создания модели). `dflow_y` определяется как прогнозируемое значение (стоимость).

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday', 'dropoff_latitude', 'dropoff_longitude','pickup_hour','pickup_longitude','pickup_latitude','passengers'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-data-into-train-and-test-sets"></a>Разделение данных на обучающий и тестовый наборы

Теперь разделите данные на обучающий и тестовый наборы с помощью функции `train_test_split` в библиотеке `sklearn`. Эта функция выполняет разделение данных на набор данных X (функции) для обучения модели и набор данных Y (прогнозируемые значения) для тестирования. Параметр `test_size` определяет процент данных, выделяемых для тестирования. Параметр `random_state` задает начальное значение для случайного генератора, чтобы разделение тестового обучения было детерминированным.

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=123)
# flatten y_train to 1d array
y_train.values.flatten()
```

Теперь у вас есть необходимые пакеты и данные готовы для автоматического обучения модели.

## <a name="automatically-train-a-model"></a>Автоматическое обучение модели

Чтобы автоматически обучить модель, сделайте следующее.
1. Определите параметры для запуска эксперимента.
1. Отправьте эксперимент для настройки модели.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Определение параметров для автоматического создания и настройки

Определите параметры эксперимента и модели для ее автоматического создания и настройки. Просмотрите полный список параметров [здесь](how-to-configure-auto-train.md).


|Свойство| Значение в этом руководстве |ОПИСАНИЕ|
|----|----|---|
|**iteration_timeout_minutes**|10|Максимальная длительность итерации в минутах.|
|**iterations**|30|Число итераций. В каждой итерации модель обучается на данных с помощью определенного конвейера.|
|**primary_metric**| spearman_correlation; | Метрика, который вы хотите оптимизировать.|
|**preprocess**| Истина | Если задано значение true, эксперимент может выполнять предварительную обработку входных данных.|
|**verbosity**| logging.INFO | Определяет уровень ведения журнала.|
|**n_cross_validationss**|5|Количество разделений перекрестной проверки.



```python
automl_settings = {
    "iteration_timeout_minutes" : 10,
    "iterations" : 30,
    "primary_metric" : 'spearman_correlation',
    "preprocess" : True,
    "verbosity" : logging.INFO,
    "n_cross_validations": 5
}
```


```python
from azureml.train.automl import AutoMLConfig

# local compute
automated_ml_config = AutoMLConfig(task = 'regression',
                             debug_log = 'automated_ml_errors.log',
                             path = project_folder,
                             X = x_train.values,
                             y = y_train.values.flatten(),
                             **automl_settings)
```

### <a name="train-the-automatic-regression-model"></a>Обучение модели автоматической регрессии

Запустите эксперимент, чтобы выполнить его в локальной среде. Передайте определенный объект `automated_ml_config` в эксперимент и задайте для выходных данных значение `true`, чтобы просмотреть ход выполнения эксперимента.


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

    Parent Run ID: AutoML_83117da4-07e3-473a-b83e-99471bfa9e09
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************
    
     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:21       0.6498    0.6498
             1   MinMaxScaler GradientBoosting                  0:00:22       0.6624    0.6624
             2   StandardScalerWrapper KNN                      0:00:18       0.7267    0.7267
             3   StandardScalerWrapper GradientBoosting         0:00:18       0.5003    0.7267
             4    Ensemble                                      0:00:38       0.6659    0.7267
    

## <a name="explore-the-results"></a>Просмотр результатов.

Ознакомьтесь с результатами автоматического обучения, воспользовавшись мини-приложением Jupyter или журналом эксперимента.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Вариант 1. Просмотр результатов с помощью мини-приложения Jupyter

Используйте мини-приложение Jupyter Notebook для просмотра графика и таблицы всех результатов.


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Сведения о запуске мини-приложения Jupyter](./media/tutorial-auto-train-models/jup-widget-auto.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Вариант 2. Получение и просмотр всех итераций запуска в Python

Кроме того, можно извлечь журнал каждого эксперимента и просмотреть отдельные метрики для каждой итерации.

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

import pandas as pd
rundata = pd.DataFrame(metricslist).sort_index(1)
rundata
```




<div>
<style scoped> .dataframe tbody tr th:only-of-type { vertical-align: middle; }

    .dataframe tbody tr th {
        vertical-align: top;
    }

    .dataframe thead th {
        text-align: right;
    }
</style>
<table border="1" class="dataframe">
  <thead>
    <tr style="text-align: right;">
      <th></th>
      <th>0</th>
      <th>1</th>
      <th>2</th>
      <th>3</th>
      <th>4.</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,113810</td>
      <td>0,093514</td>
      <td>-0,010248</td>
      <td>0,005867</td>
      <td>0,108187</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>7,004893</td>
      <td>6,348354</td>
      <td>6,493000</td>
      <td>7,045597</td>
      <td>6,646850</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>4,834063</td>
      <td>3,503244</td>
      <td>3,321553</td>
      <td>4,349547</td>
      <td>4,389995</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error;</th>
      <td>0,077832</td>
      <td>0,070537</td>
      <td>0,072144</td>
      <td>0,078284</td>
      <td>0,073854</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,053712</td>
      <td>0,038925</td>
      <td>0,036906</td>
      <td>0,048328</td>
      <td>0,048778</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error;</th>
      <td>0,117819</td>
      <td>0,120518</td>
      <td>0,126141</td>
      <td>0,124289</td>
      <td>0,118340</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error;</th>
      <td>0,177689</td>
      <td>0,163360</td>
      <td>0,168101</td>
      <td>0,178250</td>
      <td>0,168685</td>
    </tr>
    <tr>
      <th>r2_score;</th>
      <td>0,104661</td>
      <td>0,064075</td>
      <td>-0,036158</td>
      <td>-0,004403</td>
      <td>0,096976</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>10,603744</td>
      <td>10,846632</td>
      <td>11,352731</td>
      <td>11,185972</td>
      <td>10,650593</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,801531</td>
      <td>0,736896</td>
      <td>0,758279</td>
      <td>0,804062</td>
      <td>0,760913</td>
    </tr>
    <tr>
      <th>spearman_correlation;</th>
      <td>0,549825</td>
      <td>0,562435</td>
      <td>0,526702</td>
      <td>0,500302</td>
      <td>0,565857</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,549825</td>
      <td>0,562435</td>
      <td>0,562435</td>
      <td>0,562435</td>
      <td>0,565857</td>
    </tr>
  </tbody>
</table>
</div>

## <a name="retrieve-the-best-model"></a>Извлечение наиболее эффективной модели

Выберите наилучший конвейер из итераций. Метод `get_output` в `automl_classifier` возвращает лучшее выполнение и соответствующую модель для последнего соответствующего вызова. В `get_output` существуют перегрузки, которые позволяют извлекать лучшее выполнение и соответствующую модель для любой зарегистрированной метрики или определенной итерации.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="register-the-model"></a>Регистрация модели

Зарегистрируйте модель в рабочей области службы "Машинное обучение Azure".


```python
description = 'Automated Machine Learning Model'
tags = None
local_run.register_model(description=description, tags=tags)
local_run.model_id # Use this id to deploy the model as a web service in Azure
```

## <a name="test-the-best-model-accuracy"></a>Тестирование точности лучшей модели

Используйте лучшую модель для выполнения прогнозирования в тестовом наборе данных. Функция `predict` использует лучшую модель и прогнозирует значения Y (стоимость поездки) из набора данных `x_test`. Распечатайте первые 10 прогнозируемых значений стоимости из `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Сравните прогнозируемые значения стоимости с фактическими. Преобразуйте эти значения в список с помощью кадра данных `y_test`, чтобы сравнить их с прогнозируемыми. Функция `mean_absolute_error` принимает два массива значений и вычисляет среднее значение абсолютной погрешности между ними. В этом примере средняя абсолютная погрешность составляет 3,5 %. Это означает, что в среднем модель прогнозирует стоимость с погрешностью в 3,5 % от фактического значения.

```python
from sklearn.metrics import mean_absolute_error

y_actual = y_test.values.flatten().tolist()
mean_absolute_error(y_actual, y_predict)
```

    [ 3.16213051 ]

Чтобы вычислить MAPE (средняя абсолютная погрешность в процентах), запустите следующий код с помощью полных наборов данных `y_actual` и `y_predict`. Эта метрика вычисляет абсолютную разницу между каждым прогнозируемым и фактическим значением, суммирует все различия, а затем выражает эту сумму в процентах от общей суммы фактических значений.

```python
sum_actuals = sum_errors = 0

for actual_val, predict_val in zip(y_actual, y_predict):
    abs_error = actual_val - predict_val
    if abs_error < 0:
        abs_error = abs_error * -1

    sum_errors = sum_errors + abs_error
    sum_actuals = sum_actuals + actual_val

mean_abs_percent_error = sum_errors / sum_actuals
print("Model MAPE:")
print(mean_abs_percent_error)
print()
print("Model Accuracy:")
print(1 - mean_abs_percent_error)
```

    Model MAPE:
    0.22424976634422172

    Model Accuracy:
    0.7757502336557782

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Изучив это руководство об автоматическом машинном обучении, вы:

> [!div class="checklist"]
> * настроили рабочую область и подготовили данные для эксперимента;
> * выполнили обучение с помощью локальной автоматической модели регрессии с пользовательскими параметрами;
> * просмотрели и проверили результаты обучения;
> * зарегистрировали лучшую модель.

[Разверните модель](tutorial-deploy-models-with-aml.md) с помощью службы "Машинное обучение Azure".
