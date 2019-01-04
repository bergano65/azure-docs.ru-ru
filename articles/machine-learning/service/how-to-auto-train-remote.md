---
title: Удаленные целевые объекты вычислений автоматического машинного обучения
titleSuffix: Azure Machine Learning service
description: Узнайте, как создавать модели с использованием автоматического машинного обучения на удаленном целевом объекте вычислений (Виртуальная машина для обработки и анализа данных (DSVM)) с помощью службы "Машинное обучение Azure".
services: machine-learning
author: nacharya1
ms.author: nilesha
ms.reviewer: sgilley
ms.service: machine-learning
ms.component: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 18b2b3df2748392b12b60517604478b120871754
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/11/2018
ms.locfileid: "53256066"
---
# <a name="train-models-with-automated-machine-learning-in-the-cloud"></a>Обучение моделей с помощью автоматического машинного обучения в облаке

В службе "Машинное обучение Azure" можно обучить модель для различных типов доступных для управления вычислительных ресурсов. Целевым объектом вычислений может быть локальный компьютер или компьютер в облаке.

Кроме того, вы можете легко увеличить масштаб эксперимента машинного обучения, добавив дополнительные целевые объекты вычислений. Варианты целевых объектов вычислений: Виртуальная машина для обработки и анализа данных Ubuntu или Вычислительная среда Машинного обучения Azure. Виртуальная машина для анализа и обработки данных — это образ виртуальной машины в облаке Microsoft Azure, специально созданный и настроенный для обработки и анализа данных. В нее интегрировано множество популярных средств обработки и анализа данных, а также других инструментов, заранее настроенных и установленных.  

В этой статье рассказывается, как создать модель, используя автоматизированное машинное обучение на Виртуальной машине для обработки и анализа данных.

## <a name="how-does-remote-differ-from-local"></a>Чем удаленный компьютер отличается от локального?

В руководстве по [обучению модели классификации с помощью автоматического машинного обучения](tutorial-auto-train-models.md) объясняется, как использовать локальный компьютер для обучения модели с помощью автоматического машинного обучения.  Рабочий процесс, используемый при локальном обучении, применяется и для удаленных целевых объектов. Тем не менее при использовании удаленного объекта вычислений итерации экспериментов автоматического машинного обучения выполняются асинхронно. Эта функция позволяет отменить конкретную итерацию, просмотреть состояние выполнения или продолжить работать с другими ячейками в записной книжке Jupyter. При удаленном обучении нужно сначала создать удаленный целевой объект вычислений, такой как Azure DSVM.  Затем необходимо настроить удаленный ресурс и отправить на него код.

В этой статье показаны дополнительные действия, необходимые для запуска экспериментов автоматического машинного обучения на удаленной машине DSVM.  Объект рабочей области, `ws`, из учебника используется во всем представленном ниже коде.

```python
ws = Workspace.from_config()
```

## <a name="create-resource"></a>Создайте ресурс

Создайте машину DSVM в рабочей области (`ws`), если она еще не существует. Если DSVM уже была создана, этот код пропускает процесс создания и загружает данные существующих ресурсов в объект `dsvm_compute`.  

**Оценка времени**. Создание виртуальной машины занимает около 5 минут.

```python
from azureml.core.compute import DsvmCompute

dsvm_name = 'mydsvm' #Name your DSVM
try:
    dsvm_compute = DsvmCompute(ws, dsvm_name)
    print('found existing dsvm.')
except:
    print('creating new dsvm.')
    # Below is using a VM of SKU Standard_D2_v2 which is 2 core machine. You can check Azure virtual machines documentation for additional SKUs of VMs.
    dsvm_config = DsvmCompute.provisioning_configuration(vm_size = "Standard_D2_v2")
    dsvm_compute = DsvmCompute.create(ws, name = dsvm_name, provisioning_configuration = dsvm_config)
    dsvm_compute.wait_for_completion(show_output = True)
```

Теперь можно использовать объект `dsvm_compute` в качестве целевого объекта удаленных вычислений.

На имя DSVM налагаются следующие ограничения:
+ Должно быть короче 64 символов.  
+ Не может содержать ни один из следующих символов: `\` ~ ! @ # $ % ^ & * ( ) = + _ [ ] { } \\\\ | ; : \' \\" , < > / ?.`

>[!Warning]
>Если создание заканчивается неудачей с сообщением о возможности приобретения Azure Marketplace:
>    1. Перейдите на [портал Azure](https://portal.azure.com).
>    1. Начните создание DSVM. 
>    1. Выберите "Требуется создать программным способом", чтобы разрешить программное создание.
>    1. Выйдите без фактического создания виртуальной машины.
>    1. Повторно выполните код создания.

Этот код не создает имя пользователя или пароль для подготовленной DSVM. Если вы хотите напрямую подключиться к виртуальной машине, перейдите на [портал Azure](https://portal.azure.com) для создания учетных данных.  

### <a name="attach-existing-linux-dsvm"></a>Подключение имеющейся DSVM Linux

Вы также можете подключить имеющуюся DSVM в качестве целевого объекта вычислений. В этом примере используется имеющаяся DSVM и не создается новый ресурс.

> [!NOTE]
>
> В следующем коде используется целевой класс `RemoteCompute` для подключения имеющейся виртуальной машины в качестве целевого объекта вычислений.
> Класс `DsvmCompute` будет считаться устаревшим в будущих выпусках, и предпочтение будет отдаваться этому конструктивному шаблону.

Выполните следующий код для создания целевого объекта вычислений из имеющейся DSVM Linux.

```python
from azureml.core.compute import ComputeTarget, RemoteCompute 

attach_config = RemoteCompute.attach_configuration(username='<username>',
                                                   address='<ip_adress_or_fqdn>',
                                                   ssh_port=22,
                                                   private_key_file='./.ssh/id_rsa')
compute_target = ComputeTarget.attach(workspace=ws,
                                      name='attached_vm',
                                      attach_configuration=attach_config)

compute_target.wait_for_completion(show_output=True)
```

Теперь можно использовать объект `compute_target` в качестве целевого объекта удаленных вычислений.

## <a name="access-data-using-getdata-file"></a>Доступ к данным с помощью файла get_data

Предоставьте удаленному ресурсу доступ к обучающим данным. Для экспериментов автоматического машинного обучения на удаленном объекте вычислений необходимо извлечь данные с помощью функции `get_data()`.  

Для предоставления доступа необходимо выполнить следующие действия:
+ Создайте файл get_data.py, содержащий функцию `get_data()`. 
* Поместите этот файл в каталог, доступный как абсолютный путь. 

Код для чтения данных из хранилища BLOB-объектов или локальном диске можно инкапсулировать в файле get_data.py. В следующем примере кода данные поступают из пакета sklearn.

>[!Warning]
>В случае удаленного объекта вычислений необходимо использовать `get_data()`, где выполняется преобразование данных. Если необходимо установить дополнительные библиотеки для преобразований данных как часть get_data (), нужно выполнить дополнительные шаги. Перейдите к [записной книжке auto-ml-dataprep](https://aka.ms/aml-auto-ml-data-prep ) для получения подробных сведений.


```python
# Create a project_folder if it doesn't exist
if not os.path.exists(project_folder):
    os.makedirs(project_folder)

#Write the get_data file.
%%writefile $project_folder/get_data.py

from sklearn import datasets
from scipy import sparse
import numpy as np

def get_data():
    
    digits = datasets.load_digits()
    X_digits = digits.data[10:,:]
    y_digits = digits.target[10:]

    return { "X" : X_digits, "y" : y_digits }
```

## <a name="configure-experiment"></a>Настройка эксперимента

Задайте значения для `AutoMLConfig`.  (См. [полный список параметров](how-to-configure-auto-train.md#configure-experiment) и их возможные значения.)

В параметрах `run_configuration` присваивается объекту `run_config`, содержащему параметры и конфигурацию машины DSVM.  

```python
from azureml.train.automl import AutoMLConfig
import time
import logging

automl_settings = {
    "name": "AutoML_Demo_Experiment_{0}".format(time.time()),
    "iteration_timeout_minutes": 10,
    "iterations": 20,
    "n_cross_validations": 5,
    "primary_metric": 'AUC_weighted',
    "preprocess": False,
    "max_concurrent_iterations": 10,
    "verbosity": logging.INFO
}

automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                            )
```

### <a name="enable-model-explanations"></a>Включение описаний модели

Задайте дополнительный параметр `model_explainability` в конструкторе `AutoMLConfig`. Кроме того, объект проверки кадра данных нужно передать в качестве параметра `X_valid` для использования функции объясняемости модели.

```python
automl_config = AutoMLConfig(task='classification',
                             debug_log='automl_errors.log',
                             path=project_folder,
                             compute_target = dsvm_compute,
                             data_script=project_folder + "/get_data.py",
                             **automl_settings,
                             model_explainability=True,
                             X_valid = X_test
                            )
```

## <a name="submit-training-experiment"></a>Отправка обучающего эксперимента

Теперь отправьте конфигурацию для автоматического выбора алгоритма, гиперпараметров и обучения модели.

```python
from azureml.core.experiment import Experiment
experiment=Experiment(ws, 'automl_remote')
remote_run = experiment.submit(automl_config, show_output=True)
```

Результат будет выглядеть примерно так:

    Running on remote compute: mydsvmParent Run ID: AutoML_015ffe76-c331-406d-9bfd-0fd42d8ab7f6
    ***********************************************************************************************
    ITERATION: The iteration being evaluated.
    PIPELINE:  A summary description of the pipeline being evaluated.
    DURATION: Time taken for the current iteration.
    METRIC: The result of computing score on the fitted pipeline.
    BEST: The best observed score thus far.
    ***********************************************************************************************
    
     ITERATION     PIPELINE                               DURATION                METRIC      BEST
             2      Standardize SGD classifier            0:02:36                  0.954     0.954
             7      Normalizer DT                         0:02:22                  0.161     0.954
             0      Scale MaxAbs 1 extra trees            0:02:45                  0.936     0.954
             4      Robust Scaler SGD classifier          0:02:24                  0.867     0.954
             1      Normalizer kNN                        0:02:44                  0.984     0.984
             9      Normalizer extra trees                0:03:15                  0.834     0.984
             5      Robust Scaler DT                      0:02:18                  0.736     0.984
             8      Standardize kNN                       0:02:05                  0.981     0.984
             6      Standardize SVM                       0:02:18                  0.984     0.984
            10      Scale MaxAbs 1 DT                     0:02:18                  0.077     0.984
            11      Standardize SGD classifier            0:02:24                  0.863     0.984
             3      Standardize gradient boosting         0:03:03                  0.971     0.984
            12      Robust Scaler logistic regression     0:02:32                  0.955     0.984
            14      Scale MaxAbs 1 SVM                    0:02:15                  0.989     0.989
            13      Scale MaxAbs 1 gradient boosting      0:02:15                  0.971     0.989
            15      Robust Scaler kNN                     0:02:28                  0.904     0.989
            17      Standardize kNN                       0:02:22                  0.974     0.989
            16      Scale 0/1 gradient boosting           0:02:18                  0.968     0.989
            18      Scale 0/1 extra trees                 0:02:18                  0.828     0.989
            19      Robust Scaler kNN                     0:02:32                  0.983     0.989


## <a name="explore-results"></a>Изучите результаты.

Для просмотра графика и таблицы результатов можно использовать то же мини-приложение Jupyter, которое использовалось в [учебнике](tutorial-auto-train-models.md#explore-the-results).

```python
from azureml.widgets import RunDetails
RunDetails(remote_run).show()
```
Вот как выглядит статическое изображение мини-приложения.  В записной книжке можно щелкнуть любую строку таблицы, чтобы просмотреть свойства прогона и файлы журналов для этого прогона.   Для просмотра графика каждой доступной метрики для каждой итерации можно также использовать раскрывающийся список над графиком.

![таблица мини-приложения](./media/how-to-auto-train-remote/table.png)
![график мини-приложения](./media/how-to-auto-train-remote/plot.png)

Мини-приложение показывает URL-адрес, который можно использовать для просмотра и изучения данных отдельного прогона.
 
### <a name="view-logs"></a>Просмотр журналов

Найдите журналы на DSVM в каталоге `/tmp/azureml_run/{iterationid}/azureml-logs`.

## <a name="best-model-explanation"></a>Лучшее объяснение модели

Извлечение данных описания модели позволяет просмотреть подробные данные о моделях для повышения прозрачности компонентов, выполняемых на внутреннем сервере. В этом примере вы выполняете описания модели только для самой подходящей модели. При выполнении для всех моделей в конвейере это займет значительное время. Описание модели включает следующее:

* shap_values: информация объяснения, созданная shap lib.
* expected_values: ожидаемое значение модели, применяемое к набору данных X_train.
* overall_summary: значения важности функций на уровне модели, отсортированные в порядке убывания.
* overall_imp: имена компонентов, отсортированные так же, как и в overall_summary.
* per_class_summary: значения важности функций на уровне класса, отсортированные в порядке убывания. Доступно только в случае классификации.
* per_class_imp: имена компонентов, отсортированных в порядке, описанном в per_class_summary. Доступно только в случае классификации.

Используйте следующий код, чтобы выбрать лучший конвейер из итераций. Метод `get_output` возвращает лучшее выполнение и соответствующую модель для последнего соответствующего вызова.

```python
best_run, fitted_model = remote_run.get_output()
```

Импортируйте функцию `retrieve_model_explanation` и выполните оптимальную модель.

```python
from azureml.train.automl.automlexplainer import retrieve_model_explanation

shap_values, expected_values, overall_summary, overall_imp, per_class_summary, per_class_imp = \
    retrieve_model_explanation(best_run)
```

Выведите результаты для переменных описания `best_run`, которые вы хотите просмотреть.

```python
print(overall_summary)
print(overall_imp)
print(per_class_summary)
print(per_class_imp)
```

Вывод результатов переменных описаний сводки `best_run` в следующих выходных данных:

![Выходные данные консоли объясняемости модели](./media/how-to-auto-train-remote/expl-print.png)

Вы также можете визуализировать важность функции с помощью пользовательского интерфейса мини-приложения, а также пользовательского веб-интерфейса на портале Azure в рабочей области.

![Пользовательский интерфейс объясняемости модели](./media/how-to-auto-train-remote/model-exp.png)

## <a name="example"></a>Пример

В записной книжке [how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/automated-machine-learning/remote-execution/auto-ml-remote-execution.ipynb) демонстрируются основные понятия этой статьи. 

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Дополнительная информация

Узнайте, [как настроить параметры автоматического обучения](how-to-configure-auto-train.md)
