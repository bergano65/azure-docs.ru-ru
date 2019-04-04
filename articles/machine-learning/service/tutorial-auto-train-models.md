---
title: Руководство по регрессионной модели. Автоматическое Машинное обучение
titleSuffix: Azure Machine Learning service
description: Узнайте, как создать модель машинного обучения с помощью автоматического машинного обучения. Машинное обучение Azure может автоматически выполнять предварительную обработку данных, а также выбор алгоритма и гиперпараметров. После этого окончательная модель развертываться в службе "Машинное обучение Azure".
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: nacharya1
ms.author: nilesha
ms.reviewer: trbye
ms.date: 03/29/2019
ms.custom: seodec18
ms.openlocfilehash: 990991eb1ceb5d74c042b42cfa265c75a073e5ef
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670903"
---
# <a name="tutorial-use-automated-machine-learning-to-build-your-regression-model"></a>Руководство по Создание регрессионной модели с помощью автоматического машинного обучения

Это руководство представляет собой **вторую часть серии, состоящей из двух частей**. Из предыдущего руководства вы узнали, как [подготовить данные о такси в Нью-Йорке для моделирования с применением регрессии](tutorial-data-prep.md).

Теперь все готово для создания модели с помощью службы "Машинное обучение Azure". В этой части руководства вы примените подготовленные данные и автоматически создадите модель регрессии для прогнозирования тарифов на такси. С помощью автоматического машинного обучения, которое поддерживается службой, вы можете определить цели и ограничения машинного обучения. Вы просто запускаете процесс автоматического машинного обучения. Затем дождитесь, пока для вас будет выбран алгоритм и настроены гиперпараметры. Метод автоматического машинного обучения позволяет выполнить итерацию множества сочетаний алгоритмов и гиперпараметров, пока не будет найдена лучшая модель на основе выбранных критериев.

![Схема процесса](./media/tutorial-auto-train-models/flow2.png)

В этом руководстве вы выполнили следующие задачи.

> [!div class="checklist"]
> * Настройка среды Python и импорт пакетов SDK.
> * Настройка рабочей области Службы машинного обучения Azure.
> * Автоматическое обучение модели регрессии.
> * Локальный запуск модели с пользовательскими параметрами.
> * Изучение результатов.

Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](https://aka.ms/AMLFree).

>[!NOTE]
> Код в этой статье протестирован с пакетом SDK для Машинного обучения Azure версии 1.0.0.

## <a name="prerequisites"></a>Предварительные требования

Перейдите к разделу [Настройка среды разработки](#start), чтобы ознакомиться с шагами записной книжки, или используйте приведенные ниже инструкции, чтобы получить записную книжку и запустить ее в службе "Записные книжки Azure" или на собственном сервере записных книжек. Чтобы запустить записную книжку, вам потребуется:

* [Выполнение руководства по подготовке данных](tutorial-data-prep.md).
* Сервер записных книжек Python 3.6, на котором установлены следующие компоненты:
    * пакет SDK для Машинного обучения Azure для Python с `automl` и `notebooks`;
    * `matplotlib`
* Пример записной книжки.
* Рабочая область машинного обучения.
* Файл конфигурации для рабочей области в том же каталоге, что и записная книжка.

Получите все необходимые компоненты, перейдя к нужному из разделов, щелкнув соответствующую ссылку ниже.

* Использование [Записных книжек Azure](#azure)
* Использование [собственного сервера записных книжек](#server)

### <a name="azure"></a>Использование Записных книжек Azure: к вашим услугам бесплатные записные книжки на основе Jupyter в облаке Azure

Начать работу с Записными книжками Azure очень просто. [Пакет SDK Машинного обучения Azure для Python](https://aka.ms/aml-sdk) уже установлен и настроен в [Записных книжках Azure](https://notebooks.azure.com/). Службы Azure автоматически управляют установкой и последующими обновлениями.

Выполнив указанные ниже действия, запустите записную книжку **tutorials/regression-part2-automated-ml.ipynb** в проекте **Начало работы**.

[!INCLUDE [aml-azure-notebooks](../../../includes/aml-azure-notebooks.md)]

### <a name="server"></a>Использование собственного сервера записных книжек Jupyter

Чтобы создать локальный сервер Jupyter Notebook на компьютере, выполните следующие действия.  Убедитесь, что в вашей среде устанавливается `matplotlib` и дополнения `automl` и `notebooks`.

[!INCLUDE [aml-your-server](../../../includes/aml-your-server.md)]

Выполнив указанные действия, запустите записную книжку **tutorials/regression-part2-automated-ml.ipynb**.

## <a name="start"></a>Настройка среды разработки

Все настройки для работы по разработке можно сделать в записной книжке Python. Настройка включает следующие действия:

* Установка пакета SDK
* Импорт пакетов Python.
* Настройка рабочей области.

### <a name="install-and-import-packages"></a>Установка и импорт пакетов

Если вы работаете с руководством в собственной среде Python, используйте следующую команду для установки необходимых пакетов.

```shell
pip install azureml-sdk[automl,notebooks] matplotlib
```

Импортируйте пакеты Python, которые необходимы в этом руководстве.

```python
import azureml.core
import pandas as pd
from azureml.core.workspace import Workspace
import logging
import os
```

### <a name="configure-workspace"></a>Настройка рабочей области

В существующей рабочей области создайте объект. Класс [Workspace](https://docs.microsoft.com/en-us/python/api/azureml-core/azureml.core.workspace.workspace?view=azure-ml-py) принимает сведения о подписке и ресурсах Azure. Он также создает облачный ресурс для мониторинга и отслеживания работы модели.

`Workspace.from_config()` считывает файл **aml_config/config.json** и загружает данные в объект `ws`.  `ws` используется в остальном коде в этом руководстве.

Получив объект рабочей области, укажите имя для эксперимента. Создайте и зарегистрируйте локальный каталог в рабочей области. Все выполнения указанного эксперимента записываются в журнал под его именем и на [портале Azure](https://portal.azure.com).


```python
ws = Workspace.from_config()
# choose a name for the run history container in the workspace
experiment_name = 'automated-ml-regression'
# project folder
project_folder = './automated-ml-regression'

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

Используйте объект потока данных, созданный в рамках работы с предыдущим руководством. Таким образом, в первой части этого руководства были очищены данные о такси Нью-Йорка, поэтому их можно использовать в модели машинного обучения. Теперь вы используете различные функции из набора данных и позволяете автоматизированной модели создавать связи между функциями и стоимостью поездки на такси. Откройте и выполните поток данных, а затем проверьте результаты.


```python
import azureml.dataprep as dprep

file_path = os.path.join(os.getcwd(), "dflows.dprep")

package_saved = dprep.Package.open(file_path)
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
      <th>Квантиль уровня 0,1 %</th>
      <th>Квантиль уровня 1 %</th>
      <th>Квантиль уровня 5 %</th>
      <th>Квантиль уровня 25 %</th>
      <th>Квантиль уровня 50 %</th>
      <th>Квантиль уровня 75 %</th>
      <th>Квантиль уровня 95 %</th>
      <th>Квантиль уровня 99 %</th>
      <th>Квантиль уровня 99,9 %</th>
      <th>Среднее значение</th>
      <th>Стандартное отклонение</th>
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
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
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
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
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
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>2,90047</td>
      <td>2,69355</td>
      <td>9,72889</td>
      <td>16</td>
      <td>19,3713</td>
      <td>22,6974</td>
      <td>23</td>
      <td>23</td>
      <td>14,2731</td>
      <td>6,59242</td>
      <td>43,46</td>
      <td>–0,693723</td>
      <td>–0,570403</td>
    </tr>
    <tr>
      <th>pickup_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>4,99701</td>
      <td>4,95833</td>
      <td>14,1528</td>
      <td>29,3832</td>
      <td>44,6825</td>
      <td>56,4444</td>
      <td>58,9909</td>
      <td>59</td>
      <td>29,427</td>
      <td>17,4333</td>
      <td>303,921</td>
      <td>0,0120999</td>
      <td>–1,20981</td>
    </tr>
    <tr>
      <th>pickup_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,28131</td>
      <td>5</td>
      <td>14,7832</td>
      <td>29,9293</td>
      <td>44,725</td>
      <td>56,7573</td>
      <td>59</td>
      <td>59</td>
      <td>29,7443</td>
      <td>17,3595</td>
      <td>301,351</td>
      <td>–0,0252399</td>
      <td>–1,19616</td>
    </tr>
    <tr>
      <th>dropoff_weekday</th>
      <td>FieldType.STRING</td>
      <td>Пятница</td>
      <td>Среда</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
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
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>2,57153</td>
      <td>2</td>
      <td>9,58795</td>
      <td>15,9994</td>
      <td>19,6184</td>
      <td>22,8317</td>
      <td>23</td>
      <td>23</td>
      <td>14,2105</td>
      <td>6,71093</td>
      <td>45,0365</td>
      <td>–0,687292</td>
      <td>–0,61951</td>
    </tr>
    <tr>
      <th>dropoff_minute</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,44383</td>
      <td>4,84694</td>
      <td>14,1036</td>
      <td>28,8365</td>
      <td>44,3102</td>
      <td>56,6892</td>
      <td>59</td>
      <td>59</td>
      <td>29,2907</td>
      <td>17,4108</td>
      <td>303,136</td>
      <td>0,0222514</td>
      <td>–1,2181</td>
    </tr>
    <tr>
      <th>dropoff_second</th>
      <td>FieldType.DECIMAL</td>
      <td>0</td>
      <td>59</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0</td>
      <td>5,07801</td>
      <td>5</td>
      <td>14,5751</td>
      <td>29,5972</td>
      <td>45,4649</td>
      <td>56,2729</td>
      <td>59</td>
      <td>59</td>
      <td>29,772</td>
      <td>17,5337</td>
      <td>307,429</td>
      <td>–0,0212575</td>
      <td>–1,226</td>
    </tr>
    <tr>
      <th>store_forward</th>
      <td>FieldType.STRING</td>
      <td>Нет</td>
      <td>Да</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
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
      <td>–74,0781</td>
      <td>–73,7459</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>–74,0578</td>
      <td>–73,9639</td>
      <td>–73,9656</td>
      <td>–73,9508</td>
      <td>–73,9255</td>
      <td>–73,8529</td>
      <td>–73,8302</td>
      <td>–73,8238</td>
      <td>–73,7697</td>
      <td>–73,9123</td>
      <td>0,0503757</td>
      <td>0,00253771</td>
      <td>0,352172</td>
      <td>–0,923743</td>
    </tr>
    <tr>
      <th>pickup_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5755</td>
      <td>40,8799</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,632</td>
      <td>40,7117</td>
      <td>40,7115</td>
      <td>40,7213</td>
      <td>40,7565</td>
      <td>40,8058</td>
      <td>40,8478</td>
      <td>40,8676</td>
      <td>40,8778</td>
      <td>40,7649</td>
      <td>0,0494674</td>
      <td>0,00244702</td>
      <td>0,205972</td>
      <td>–0,777945</td>
    </tr>
    <tr>
      <th>dropoff_longitude</th>
      <td>FieldType.DECIMAL</td>
      <td>-74,0857</td>
      <td>-73,7209</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>–74,0775</td>
      <td>–73,9875</td>
      <td>–73,9882</td>
      <td>–73,9638</td>
      <td>–73,935</td>
      <td>–73,8755</td>
      <td>–73,8125</td>
      <td>–73,7759</td>
      <td>–73,7327</td>
      <td>–73,9202</td>
      <td>0,0584627</td>
      <td>0,00341789</td>
      <td>0,623622</td>
      <td>–0,262603</td>
    </tr>
    <tr>
      <th>dropoff_latitude</th>
      <td>FieldType.DECIMAL</td>
      <td>40,5835</td>
      <td>40,8797</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>40,5973</td>
      <td>40,6928</td>
      <td>40,6911</td>
      <td>40,7226</td>
      <td>40,7567</td>
      <td>40,7918</td>
      <td>40,8495</td>
      <td>40,868</td>
      <td>40,8787</td>
      <td>40,7583</td>
      <td>0,0517399</td>
      <td>0,00267701</td>
      <td>0,0390404</td>
      <td>–0,203525</td>
    </tr>
    <tr>
      <th>passengers</th>
      <td>FieldType.DECIMAL</td>
      <td>1</td>
      <td>6</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
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
      <td>2,39249</td>
      <td>1,83197</td>
      <td>3,3561</td>
      <td>0,763144</td>
      <td>–1,23467</td>
    </tr>
    <tr>
      <th>distance</th>
      <td>FieldType.DECIMAL</td>
      <td>0,01</td>
      <td>32,34</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0108744</td>
      <td>0,743898</td>
      <td>0,738194</td>
      <td>1,243</td>
      <td>2,40168</td>
      <td>4,74478</td>
      <td>10,5136</td>
      <td>14,9011</td>
      <td>21,8035</td>
      <td>3,5447</td>
      <td>3,2943</td>
      <td>10,8524</td>
      <td>1,91556</td>
      <td>4,99898</td>
    </tr>
    <tr>
      <th>cost</th>
      <td>FieldType.DECIMAL</td>
      <td>0,1</td>
      <td>88</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>6148,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>0,0</td>
      <td>2,33837</td>
      <td>5,00491</td>
      <td>5</td>
      <td>6,93129</td>
      <td>10,524</td>
      <td>17,4811</td>
      <td>33,2343</td>
      <td>50,0093</td>
      <td>63,1753</td>
      <td>13,6843</td>
      <td>9,66571</td>
      <td>93,426</td>
      <td>1,78518</td>
      <td>4,13972</td>
    </tr>
  </tbody>
</table>

Чтобы подготовить данные для эксперимента, добавьте столбцы в `dflow_x` (таким образом они будут считаться признаками создания модели). `dflow_y` определяется как прогнозируемое значение (**стоимость**).

```python
dflow_X = dflow_prepared.keep_columns(['pickup_weekday','pickup_hour', 'distance','passengers', 'vendor'])
dflow_y = dflow_prepared.keep_columns('cost')
```

### <a name="split-the-data-into-train-and-test-sets"></a>Разделение данных на обучающий и тестовый наборы

Теперь разделите данные на обучающий и тестовый наборы с помощью функции `train_test_split` из библиотеки `sklearn`. Эта функция выполняет разделение данных на набор данных X (**функции**) для обучения модели и набор данных Y (**прогнозируемые значения**) для тестирования. Параметр `test_size` определяет процент данных, выделяемых для тестирования. Параметр `random_state` задает начальное значение для случайного генератора, чтобы разделение тестового обучения было детерминированным.

```python
from sklearn.model_selection import train_test_split

x_df = dflow_X.to_pandas_dataframe()
y_df = dflow_y.to_pandas_dataframe()

x_train, x_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=223)
# flatten y_train to 1d array
y_train.values.flatten()
```

Цель этого шага — получить точки данных для проверки готовой модели, которые не использовались для обучения модели, чтобы измерить истинную точность. Другими словами, хорошо обученная модель должна уметь делать точные прогнозы на основе данных, которые она еще не видела. Теперь у вас есть необходимые пакеты и готовые данные для автоматического обучения модели.

## <a name="automatically-train-a-model"></a>Автоматическое обучение модели

Чтобы автоматически обучить модель, выполните следующие действия.
1. Определите параметры для запуска эксперимента. Вложите данные для обучения в конфигурацию и измените параметры, которые управляют процессом обучения.
1. Отправьте эксперимент для настройки модели. После отправки эксперимента процесс повторяется в различных алгоритмах машинного обучения и значениях гиперпараметров в соответствии с заданными вами ограничениями. Он выбирает наиболее подходящую модель путем оптимизации метрики точности.

### <a name="define-settings-for-autogeneration-and-tuning"></a>Определение параметров для автоматического создания и настройки

Определите параметры эксперимента и модели для ее автоматического создания и настройки. Просмотрите полный список параметров [здесь](how-to-configure-auto-train.md). Отправка эксперимента с этими параметрами по умолчанию занимает примерно 10–15 минут, но если вы хотите снизить время выполнения, уменьшите `iterations` или `iteration_timeout_minutes`.


|Свойство| Значение в этом руководстве |ОПИСАНИЕ|
|----|----|---|
|**iteration_timeout_minutes**|10|Максимальная длительность каждой итерации в минутах. Уменьшите это значение, чтобы уменьшить общее время выполнения.|
|**iterations**|30|Число итераций. В каждой итерации новая модель машинного обучения обучается с помощью ваших данных. Это основное значение, которое влияет на общее время выполнения.|
|**primary_metric**| spearman_correlation; | Метрика, который вы хотите оптимизировать. Наиболее подходящая модель будет выбрана на основе этой метрики.|
|**preprocess**| Истина | Используя **True**, эксперимент может предварительно обработать входные данные (обработка отсутствующих данных, преобразование текста в числовой тип и т. д.).|
|**verbosity**| logging.INFO | Определяет уровень ведения журнала.|
|**n_cross_validations**|5|Количество разделений перекрестных проверок, которые нужно выполнить, если данные проверки не указаны.|



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

Используйте определенные вами параметры обучения в качестве параметра для объекта `AutoMLConfig`. Кроме того, укажите свои данные для обучения и тип модели, которая является `regression` в данном случае.

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

Запустите эксперимент, чтобы выполнить его в локальной среде. Передайте в эксперимент определенный объект `automated_ml_config`. Настройте выход `True` для просмотра хода выполнения во время эксперимента:


```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, experiment_name)
local_run = experiment.submit(automated_ml_config, show_output=True)
```

Показанный вывод обновляется в реальном времени по мере выполнения эксперимента. Для каждой итерации вы видите тип модели, длительность выполнения и точность обучения. Поле `BEST` отслеживает лучшую оценку выполнения обучения на основе вашего типа метрики.

    Parent Run ID: AutoML_02778de3-3696-46e9-a71b-521c8fca0651
    *******************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE: A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    *******************************************************************************************

     ITERATION   PIPELINE                                       DURATION      METRIC      BEST
             0   MaxAbsScaler ExtremeRandomTrees                0:00:08       0.9447    0.9447
             1   StandardScalerWrapper GradientBoosting         0:00:09       0.9536    0.9536
             2   StandardScalerWrapper ExtremeRandomTrees       0:00:09       0.8580    0.9536
             3   StandardScalerWrapper RandomForest             0:00:08       0.9147    0.9536
             4   StandardScalerWrapper ExtremeRandomTrees       0:00:45       0.9398    0.9536
             5   MaxAbsScaler LightGBM                          0:00:08       0.9562    0.9562
             6   StandardScalerWrapper ExtremeRandomTrees       0:00:27       0.8282    0.9562
             7   StandardScalerWrapper LightGBM                 0:00:07       0.9421    0.9562
             8   MaxAbsScaler DecisionTree                      0:00:08       0.9526    0.9562
             9   MaxAbsScaler RandomForest                      0:00:09       0.9355    0.9562
            10   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            11   MaxAbsScaler LightGBM                          0:00:09       0.9553    0.9602
            12   MaxAbsScaler DecisionTree                      0:00:07       0.9484    0.9602
            13   MaxAbsScaler LightGBM                          0:00:08       0.9540    0.9602
            14   MaxAbsScaler RandomForest                      0:00:10       0.9365    0.9602
            15   MaxAbsScaler SGD                               0:00:09       0.9602    0.9602
            16   StandardScalerWrapper ExtremeRandomTrees       0:00:49       0.9171    0.9602
            17   SparseNormalizer LightGBM                      0:00:08       0.9191    0.9602
            18   MaxAbsScaler DecisionTree                      0:00:08       0.9402    0.9602
            19   StandardScalerWrapper ElasticNet               0:00:08       0.9603    0.9603
            20   MaxAbsScaler DecisionTree                      0:00:08       0.9513    0.9603
            21   MaxAbsScaler SGD                               0:00:08       0.9603    0.9603
            22   MaxAbsScaler SGD                               0:00:10       0.9602    0.9603
            23   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            24   StandardScalerWrapper ElasticNet               0:00:09       0.9603    0.9603
            25   MaxAbsScaler SGD                               0:00:09       0.9603    0.9603
            26   TruncatedSVDWrapper ElasticNet                 0:00:09       0.9602    0.9603
            27   MaxAbsScaler SGD                               0:00:12       0.9413    0.9603
            28   StandardScalerWrapper ElasticNet               0:00:07       0.9603    0.9603
            29    Ensemble                                      0:00:38       0.9622    0.9622

## <a name="explore-the-results"></a>Просмотр результатов.

Ознакомьтесь с результатами автоматического обучения, воспользовавшись мини-приложением Jupyter или журналом эксперимента.

### <a name="option-1-add-a-jupyter-widget-to-see-results"></a>Вариант 1. Просмотр результатов с помощью мини-приложения Jupyter

Если вы используете записную книжку Jupyter, это мини-приложение позволит просмотреть график и таблицу всех результатов.


```python
from azureml.widgets import RunDetails
RunDetails(local_run).show()
```

![Мини-приложение Jupyter, данные о выполнении](./media/tutorial-auto-train-models/automl-dash-output.png)
![График в мини-приложении Jupyter](./media/tutorial-auto-train-models/automl-chart-output.png)

### <a name="option-2-get-and-examine-all-run-iterations-in-python"></a>Вариант 2. Получение и просмотр всех итераций запуска в Python

Вы можете извлечь журнал каждого эксперимента и просмотреть отдельные метрики для каждой итерации. Проверив RMSE (root_mean_squared_error) для каждого отдельного выполнения модели, вы увидите, что большинство итераций прогнозируют справедливую стоимость такси в разумных границах (3–4 доллара США).

```python
children = list(local_run.get_children())
metricslist = {}
for run in children:
    properties = run.get_properties()
    metrics = {k: v for k, v in run.get_metrics().items() if isinstance(v, float)}
    metricslist[int(properties['iteration'])] = metrics

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
      <th>5</th>
      <th>6</th>
      <th>7</th>
      <th>8</th>
      <th>9</th>
      <th>...</th>
      <th>20</th>
      <th>21</th>
      <th>22</th>
      <th>23</th>
      <th>24</th>
      <th>25</th>
      <th>26</th>
      <th>27</th>
      <th>28</th>
      <th>29</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <th>explained_variance</th>
      <td>0,811037</td>
      <td>0,880553</td>
      <td>0,398582</td>
      <td>0,776040</td>
      <td>0,663869</td>
      <td>0,875911</td>
      <td>0,115632</td>
      <td>0,586905</td>
      <td>0,851911</td>
      <td>0,793964</td>
      <td>...</td>
      <td>0,850023</td>
      <td>0,883603</td>
      <td>0,883704</td>
      <td>0,880797</td>
      <td>0,881564</td>
      <td>0,883708</td>
      <td>0,881826</td>
      <td>0,585377</td>
      <td>0,883123</td>
      <td>0,886817</td>
    </tr>
    <tr>
      <th>mean_absolute_error</th>
      <td>2,189444</td>
      <td>1,500412</td>
      <td>5,480531</td>
      <td>2,626316</td>
      <td>2,973026</td>
      <td>1,550199</td>
      <td>6,383868</td>
      <td>4,414241</td>
      <td>1,743328</td>
      <td>2,294601</td>
      <td>...</td>
      <td>1,797402</td>
      <td>1,415815</td>
      <td>1,418167</td>
      <td>1,578617</td>
      <td>1,559427</td>
      <td>1,413042</td>
      <td>1,551698</td>
      <td>4,069196</td>
      <td>1,505795</td>
      <td>1,430957</td>
    </tr>
    <tr>
      <th>median_absolute_error</th>
      <td>1,438417</td>
      <td>0,850899</td>
      <td>4,579662</td>
      <td>1,765210</td>
      <td>1,594600</td>
      <td>0,869883</td>
      <td>4,266450</td>
      <td>3,627355</td>
      <td>0,954992</td>
      <td>1,361014</td>
      <td>...</td>
      <td>0,973634</td>
      <td>0,774814</td>
      <td>0,797269</td>
      <td>1,147234</td>
      <td>1,116424</td>
      <td>0,783958</td>
      <td>1,098464</td>
      <td>2,709027</td>
      <td>1,003728</td>
      <td>0,851724</td>
    </tr>
    <tr>
      <th>normalized_mean_absolute_error;</th>
      <td>0,024908</td>
      <td>0,017070</td>
      <td>0,062350</td>
      <td>0,029878</td>
      <td>0,033823</td>
      <td>0,017636</td>
      <td>0,072626</td>
      <td>0,050219</td>
      <td>0,019833</td>
      <td>0,026105</td>
      <td>...</td>
      <td>0,020448</td>
      <td>0,016107</td>
      <td>0,016134</td>
      <td>0,017959</td>
      <td>0,017741</td>
      <td>0,016076</td>
      <td>0,017653</td>
      <td>0,046293</td>
      <td>0,017131</td>
      <td>0,016279</td>
    </tr>
    <tr>
      <th>normalized_median_absolute_error</th>
      <td>0,016364</td>
      <td>0,009680</td>
      <td>0,052101</td>
      <td>0,020082</td>
      <td>0,018141</td>
      <td>0,009896</td>
      <td>0,048538</td>
      <td>0,041267</td>
      <td>0,010865</td>
      <td>0,015484</td>
      <td>...</td>
      <td>0,011077</td>
      <td>0,008815</td>
      <td>0,009070</td>
      <td>0,013052</td>
      <td>0,012701</td>
      <td>0,008919</td>
      <td>0,012497</td>
      <td>0,030819</td>
      <td>0,011419</td>
      <td>0,009690</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_error;</th>
      <td>0,047968</td>
      <td>0,037882</td>
      <td>0,085572</td>
      <td>0,052282</td>
      <td>0,065809</td>
      <td>0,038664</td>
      <td>0,109401</td>
      <td>0,071104</td>
      <td>0,042294</td>
      <td>0,049967</td>
      <td>...</td>
      <td>0,042565</td>
      <td>0,037685</td>
      <td>0,037557</td>
      <td>0,037643</td>
      <td>0,037513</td>
      <td>0,037560</td>
      <td>0,037465</td>
      <td>0,072077</td>
      <td>0,037249</td>
      <td>0,036716</td>
    </tr>
    <tr>
      <th>normalized_root_mean_squared_log_error;</th>
      <td>0,055353</td>
      <td>0,045000</td>
      <td>0,110219</td>
      <td>0,065633</td>
      <td>0,063589</td>
      <td>0,044412</td>
      <td>0,123433</td>
      <td>0,092312</td>
      <td>0,046130</td>
      <td>0,055243</td>
      <td>...</td>
      <td>0,046540</td>
      <td>0,041804</td>
      <td>0,041771</td>
      <td>0,045175</td>
      <td>0,044628</td>
      <td>0,041617</td>
      <td>0,044405</td>
      <td>0,079651</td>
      <td>0,042799</td>
      <td>0,041530</td>
    </tr>
    <tr>
      <th>r2_score;</th>
      <td>0,810900</td>
      <td>0,880328</td>
      <td>0,398076</td>
      <td>0,775957</td>
      <td>0,642812</td>
      <td>0,875719</td>
      <td>0,021603</td>
      <td>0,586514</td>
      <td>0,851767</td>
      <td>0,793671</td>
      <td>...</td>
      <td>0,849809</td>
      <td>0,880142</td>
      <td>0,880952</td>
      <td>0,880586</td>
      <td>0,881347</td>
      <td>0,880887</td>
      <td>0,881613</td>
      <td>0,548121</td>
      <td>0,882883</td>
      <td>0,886321</td>
    </tr>
    <tr>
      <th>root_mean_squared_error</th>
      <td>4,216362</td>
      <td>3,329810</td>
      <td>7,521765</td>
      <td>4,595604</td>
      <td>5,784601</td>
      <td>3,398540</td>
      <td>9,616354</td>
      <td>6,250011</td>
      <td>3,717661</td>
      <td>4,392072</td>
      <td>...</td>
      <td>3,741447</td>
      <td>3,312533</td>
      <td>3,301242</td>
      <td>3,308795</td>
      <td>3,297389</td>
      <td>3,301485</td>
      <td>3,293182</td>
      <td>6,335581</td>
      <td>3,274209</td>
      <td>3,227365</td>
    </tr>
    <tr>
      <th>root_mean_squared_log_error</th>
      <td>0,243184</td>
      <td>0,197702</td>
      <td>0,484227</td>
      <td>0,288349</td>
      <td>0,279367</td>
      <td>0,195116</td>
      <td>0,542281</td>
      <td>0,405559</td>
      <td>0,202666</td>
      <td>0,242702</td>
      <td>...</td>
      <td>0,204464</td>
      <td>0,183658</td>
      <td>0,183514</td>
      <td>0,198468</td>
      <td>0,196067</td>
      <td>0,182836</td>
      <td>0,195087</td>
      <td>0,349935</td>
      <td>0,188031</td>
      <td>0,182455</td>
    </tr>
    <tr>
      <th>spearman_correlation;</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,857965</td>
      <td>0,914703</td>
      <td>0,939846</td>
      <td>0,956159</td>
      <td>0,828187</td>
      <td>0,942069</td>
      <td>0,952581</td>
      <td>0,935477</td>
      <td>...</td>
      <td>0,951287</td>
      <td>0,960335</td>
      <td>0,960195</td>
      <td>0,960279</td>
      <td>0,960288</td>
      <td>0,960323</td>
      <td>0,960161</td>
      <td>0,941254</td>
      <td>0,960293</td>
      <td>0,962158</td>
    </tr>
    <tr>
      <th>spearman_correlation_max</th>
      <td>0,944743</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,953618</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>0,956159</td>
      <td>...</td>
      <td>0,960303</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,960335</td>
      <td>0,962158</td>
    </tr>
  </tbody>
</table>
<p>12 строк × 30 столбцов</p>
</div>

## <a name="retrieve-the-best-model"></a>Извлечение наиболее эффективной модели

Выберите наилучший конвейер из итераций. Метод `get_output` в `automl_classifier` возвращает лучшее выполнение и соответствующую модель для последнего соответствующего вызова. Используя перегрузки `get_output`, вы можете извлекать лучшее выполнение и соответствующую ему модель по любой зарегистрированной метрике или по определенной итерации.

```python
best_run, fitted_model = local_run.get_output()
print(best_run)
print(fitted_model)
```

## <a name="test-the-best-model-accuracy"></a>Тестирование точности лучшей модели

Используйте лучшую модель для выполнения прогнозирования в тестовом наборе данных, чтобы прогнозировать тарифы на такси. Функция `predict` использует лучшую модель и прогнозирует значения Y (**стоимость поездки**) из набора данных `x_test`. Выведите первые 10 прогнозируемых значений стоимости из `y_predict`.

```python
y_predict = fitted_model.predict(x_test.values)
print(y_predict[:10])
```

Создайте точечную диаграмму для отображения прогнозируемых значений затрат в сравнении с фактическими значениями затрат. Следующий код использует функцию `distance` в качестве оси X и поездку `cost` как ось Y. Чтобы сравнивать дисперсию прогнозируемых затрат при каждом значении расстояния поездки, первые 100 прогнозируемых и фактических значений затрат создаются в виде отдельных рядов. Просматривая график видно, что отношение расстояния и затрат почти линейное, а прогнозируемые значения затрат в большинстве случаев очень близки к фактической цене для той же длительности поездки.

```python
import matplotlib.pyplot as plt

fig = plt.figure(figsize=(14, 10))
ax1 = fig.add_subplot(111)

distance_vals = [x[4] for x in x_test.values]
y_actual = y_test.values.flatten().tolist()

ax1.scatter(distance_vals[:100], y_predict[:100], s=18, c='b', marker="s", label='Predicted')
ax1.scatter(distance_vals[:100], y_actual[:100], s=18, c='r', marker="o", label='Actual')

ax1.set_xlabel('distance (mi)')
ax1.set_title('Predicted and Actual Cost/Distance')
ax1.set_ylabel('Cost ($)')

plt.legend(loc='upper left', prop={'size': 12})
plt.rcParams.update({'font.size': 14})
plt.show()
```

![Точечная диаграмма с результатами прогнозов](./media/tutorial-auto-train-models/automl-scatter-plot.png)

Вычислите значение `root mean squared error` результатов. Примените кадр данных `y_test`. Преобразуйте его в список, чтобы сравнить с прогнозируемыми значениями. Функция `mean_squared_error` принимает два массива значений и вычисляет значение среднеквадратической погрешности между ними. Квадратный корень из результата позволяет получить оценку ошибку в тех же единицах, что и для переменной y (**стоимость**). Она обозначает, насколько далеки полученные прогнозы тарифов на такси от фактических тарифов:

```python
from sklearn.metrics import mean_squared_error
from math import sqrt

rmse = sqrt(mean_squared_error(y_actual, y_predict))
rmse
```

    3.2204936862688798

Чтобы вычислить MAPE (средняя абсолютная погрешность в процентах), запустите следующий код для полных наборов данных `y_actual` и `y_predict`. Эта метрика вычисляет абсолютное отклонение между каждой парой прогнозируемого и фактического значения, а затем суммирует все отклонения. Эта сумма выражается в процентах от общего количества фактических значений:

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
    0.10545153869569586

    Model Accuracy:
    0.8945484613043041

Из конечных метрик точности прогнозирования вы видите, что модель достаточно хороша в прогнозировании тарифов на такси на основе функций набора данных, обычно в пределах + - 3,00 доллара США. Традиционный процесс разработки модели машинного обучения является очень ресурсоемким и требует значительных предметных знаний и времени для выполнения десятков моделей и сравнения их результатов. Использование автоматического машинного обучения является отличным способом быстро протестировать множество различных моделей для вашего сценария.

## <a name="clean-up-resources"></a>Очистка ресурсов

[!INCLUDE [aml-delete-resource-group](../../../includes/aml-delete-resource-group.md)]

## <a name="next-steps"></a>Дополнительная информация

Изучив это руководство об автоматическом машинном обучении, вы выполнили следующие задачи.

> [!div class="checklist"]
> * Настройка рабочей области и подготовка данных для эксперимента.
> * Обучение с помощью локальной автоматической модели регрессии с пользовательскими параметрами.
> * Просмотр и проверка результатов обучения.

[Разверните модель](tutorial-deploy-models-with-aml.md) с помощью службы "Машинное обучение Azure".
