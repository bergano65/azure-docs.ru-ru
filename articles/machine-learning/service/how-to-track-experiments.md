---
title: Метрики журнала во время учебных запусков
titleSuffix: Azure Machine Learning service
description: Вы можете отслеживать эксперименты и отслеживать метрики, чтобы улучшить процесс создания модели. Узнайте, как добавить ведение журнала в сценарий обучения, как отправить эксперимент, как проверить ход выполнения задания и как просмотреть записанные результаты выполнения.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 07/11/2019
ms.custom: seodec18
ms.openlocfilehash: 0630ca28652b48b3632dbae94c5e16d6adb462c4
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/09/2019
ms.locfileid: "70812290"
---
# <a name="track-machine-learning-training-metrics-with-azure-machine-learning"></a>Мониторинг метрик обучения машинного обучения с помощью Машинное обучение Azure

Улучшите процесс создания модели, отслеживая эксперименты и контрольные метрики. В этой статье вы узнаете, как добавить код ведения журнала в сценарий обучения, отправить запуск эксперимента, наблюдать за выполнением и исследовать результаты в службе Машинное обучение Azure.

> [!NOTE]
> Машинное обучение Azure служба может также записывать в журнал данные из других источников во время обучения, такие как автоматические запуски машинного обучения или контейнер DOCKER, выполняющий задание обучения. Эти журналы не документированы. Если возникли проблемы и вы можете обратиться в службу поддержки Майкрософт, они смогут использовать эти журналы во время устранения неполадок.

## <a name="available-metrics-to-track"></a>Доступные метрики для мониторинга

Следующие метрики можно добавить к выполнению во время обучения эксперимента. Чтобы просмотреть подробный список того, что можно отслеживать при выполнении, ознакомьтесь со [справочной документацией по классу Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Type| Функция Python | Примечания|
|----|:----|:----|
|Скалярные значения |Функция:<br>`run.log(name, value, description='')`<br><br>Пример:<br>run.log("accuracy", 0.95) |Запишите числовое или строковое значение в выполнение с заданным именем. Запись метрики в выполнение приводит к тому, что метрика будет сохранена в записи о выполнении в эксперименте.  Одну и ту же метрику можно несколько раз записать в рамках выполнения. Результат будет считаться вектором этой метрики.|
|Списки|Функция:<br>`run.log_list(name, value, description='')`<br><br>Пример:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Запишите список значений в выполнение с заданным именем.|
|Строка|Функция:<br>`run.log_row(name, description=None, **kwargs)`<br>Пример:<br>run.log_row("Y over X", x=1, y=0.4) | С помощью *log_row* создается метрика с несколькими столбцами, как описано в kwargs. Каждый именованный параметр создает столбец с указанным значением.  *log_row* можно вызвать один раз, чтобы записать произвольный кортеж, или несколько раз в цикле, чтобы создать полную таблицу.|
|Таблица|Функция:<br>`run.log_table(name, value, description='')`<br><br>Пример:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Запишите объект словаря в выполнение с заданным именем. |
|Изображений|Функция:<br>`run.log_image(name, path=None, plot=None)`<br><br>Пример:<br>`run.log_image("ROC", plt)` | Запишите изображение в запись о выполнении. Используйте log_image, чтобы записать файл изображения или график matplotlib в выполнение.  Эти изображения будут видимы, и их можно сравнить в записи о выполнении.|
|Добавление тега к выполнению|Функция:<br>`run.tag(key, value=None)`<br><br>Пример:<br>run.tag("selected", "yes") | Добавление тега к выполнению с использованием строкового ключа и дополнительного значения строки.|
|Отправка файла или каталога|Функция:<br>`run.upload_file(name, path_or_stream)`<br> <br> Пример:<br>run.upload_file("best_model.pkl", "./model.pkl") | Отправка файла в запись о выполнении. Выполняет автоматическую запись файла в указанном каталоге вывода, который по умолчанию имеет значение "./outputs" для большинства типов выполнения.  Используйте upload_file только в том случае, если необходимо отправить дополнительные файлы или не указан выходной каталог. Мы советуем добавлять `outputs` к имени для того, чтобы файл отправлялся в выходной каталог. Вы можете перечислить все файлы, связанные с этой записью о выполнении, вызвав `run.get_file_names()`.|

> [!NOTE]
> Метрики для скалярных значений, списков, строк и таблиц могут быть типа: число с плавающей точкой, целое число или строка.

## <a name="choose-a-logging-option"></a>Выбор параметра ведения журнала

Если вы хотите отслеживать свой эксперимент, необходимо добавить код, чтобы запустить ведение журнала при отправке выполнения. Ниже приведены способы активации отправки выполнений:
* __Run.start_logging__ добавляет функции регистрации в сценарий обучения и запускает интерактивный сеанс регистрации в определенном эксперименте. **start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.
* __ScriptRunConfig__ добавляет функции ведения журнала в сценарий обучения и загружает всю папку сценария с выполнением.  **ScriptRunConfig** является классом для настройки конфигурации выполнений в сценарии. С использованием этого параметра можно добавить код мониторинга, чтобы получать уведомления о завершении или получить визуальное мини-приложение для мониторинга.

## <a name="set-up-the-workspace"></a>Настройка рабочей области
Перед тем, как включать ведение журнала и отправлять эксперимент, необходимо настроить рабочую область.

1. Загрузите рабочую область. Дополнительные сведения о настройке конфигурации рабочей области см. в разделе [файл конфигурации рабочей области](how-to-configure-environment.md#workspace).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace.from_config()
   ```
  
## <a name="option-1-use-start_logging"></a>Вариант 1. Использование start_logging

**start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.

В следующем примере простая модель sklearn Ridge обучается локально в локальной записной книжке Jupyter. Дополнительные сведения об отправке экспериментов в различные среды см. в статье [Выбор и использование целевого объекта вычислений для обучения вашей модели](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Создайте сценарий обучения в локальной записной книжке Jupyter. 

   ```python
   # load diabetes dataset, a well-known small dataset that comes with scikit-learn
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from sklearn.externals import joblib

   X, y = load_diabetes(return_X_y = True)
   columns = ['age', 'gender', 'bmi', 'bp', 's1', 's2', 's3', 's4', 's5', 's6']
   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {
      "train":{"X": X_train, "y": y_train},        
      "test":{"X": X_test, "y": y_test}
   }
   reg = Ridge(alpha = 0.03)
   reg.fit(data['train']['X'], data['train']['y'])
   preds = reg.predict(data['test']['X'])
   print('Mean Squared Error is', mean_squared_error(preds, data['test']['y']))
   joblib.dump(value = reg, filename = 'model.pkl');
   ```

2. Добавьте отслеживание эксперимента с помощью пакета SDK для службы "Машинное обучение Azure" и отправьте сохраненную модель в запись о выполнении для эксперимента. Следующий код добавляет теги, журналы и отправляет файл модели в выполнение эксперимента.

   ```python
    # Get an experiment object from Azure Machine Learning
    experiment = Experiment(workspace=ws, name="train-within-notebook")
    
    # Create a run object in the experiment
    run =  experiment.start_logging()
    # Log the algorithm parameter alpha to the run
    run.log('alpha', 0.03)
    
    # Create, fit, and test the scikit-learn Ridge regression model
    regression_model = Ridge(alpha=0.03)
    regression_model.fit(data['train']['X'], data['train']['y'])
    preds = regression_model.predict(data['test']['X'])
    
    # Output the Mean Squared Error to the notebook and to the run
    print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
    run.log('mse', mean_squared_error(data['test']['y'], preds))
    
    # Save the model to the outputs directory for capture
    model_file_name = 'outputs/model.pkl'
    
    joblib.dump(value = regression_model, filename = model_file_name)
    
    # upload the model file explicitly into artifacts 
    run.upload_file(name = model_file_name, path_or_stream = model_file_name)
    
    # Complete the run
    run.complete()
   ```

    Сценарий оканчивается на ```run.complete()```, что помечает выполнение как завершенное.  Обычно эта функция используется в сценариях интерактивной записной книжки.

## <a name="option-2-use-scriptrunconfig"></a>Вариант 2. Использование ScriptRunConfig

[**Скриптрунконфиг**](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) — это класс для настройки конфигураций для выполнения сценариев. С использованием этого параметра можно добавить код мониторинга, чтобы получать уведомления о завершении или получить визуальное мини-приложение для мониторинга.

В этом примере расширяется указанная выше базовая модель sklearn Ridge. В нем выполняется простая замена параметров по альфа-значениям модели для записи метрик и обученных моделей в выполнениях в рамках эксперимента. Пример запускается локально в управляемой пользователем среде. 

1. Создайте сценарий обучения `train.py`.

   ```python
   # train.py

   import os
   from sklearn.datasets import load_diabetes
   from sklearn.linear_model import Ridge
   from sklearn.metrics import mean_squared_error
   from sklearn.model_selection import train_test_split
   from azureml.core.run import Run
   from sklearn.externals import joblib

   import numpy as np

   #os.makedirs('./outputs', exist_ok = True)

   X, y = load_diabetes(return_X_y = True)

   run = Run.get_context()

   X_train, X_test, y_train, y_test = train_test_split(X, y, test_size = 0.2, random_state = 0)
   data = {"train": {"X": X_train, "y": y_train},
          "test": {"X": X_test, "y": y_test}}

   # list of numbers from 0.0 to 1.0 with a 0.05 interval
   alphas = mylib.get_alphas()

   for alpha in alphas:
      # Use Ridge algorithm to create a regression model
      reg = Ridge(alpha = alpha)
      reg.fit(data["train"]["X"], data["train"]["y"])

      preds = reg.predict(data["test"]["X"])
      mse = mean_squared_error(preds, data["test"]["y"])
      # log the alpha and mse values
      run.log('alpha', alpha)
      run.log('mse', mse)

      model_file_name = 'ridge_{0:.2f}.pkl'.format(alpha)
      # save model in the outputs folder so it automatically get uploaded
      with open(model_file_name, "wb") as file:
          joblib.dump(value = reg, filename = model_file_name)

      # upload the model file explicitly into artifacts 
      run.upload_file(name = model_file_name, path_or_stream = model_file_name)

      # register the model
      #run.register_model(file_name = model_file_name)

      print('alpha is {0:.2f}, and mse is {1:0.2f}'.format(alpha, mse))
  
   ```

2. Сценарий `train.py` ссылается на `mylib.py`, который позволяет получить список альфа-значений для использования в модели ridge.

   ```python
   # mylib.py
  
   import numpy as np

   def get_alphas():
      # list of numbers from 0.0 to 1.0 with a 0.05 interval
      return np.arange(0.0, 1.0, 0.05)
   ```

3. Настройте управляемую пользователем локальную среду.

   ```python
   from azureml.core import Environment
    
   # Editing a run configuration property on-fly.
   user_managed_env = Environment("user-managed-env")
    
   user_managed_env.python.user_managed_dependencies = True
    
   # You can choose a specific Python environment by pointing to a Python path 
   #user_managed_env.python.interpreter_path = '/home/johndoe/miniconda3/envs/myenv/bin/python'
   ```

4. Отправьте сценарий ```train.py``` для выполнения в среде, управляемой пользователем. Вся папка сценария отправляется для обучения, в том числе файл ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
    
   exp = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory='./', script='train.py')
   src.run_config.environment = user_managed_env
   run = exp.submit(src)
   ```

## <a name="manage-a-run"></a>Управление запуском

В статье [Запуск, мониторинг и Отмена обучения выполняется](how-to-manage-runs.md) выделение конкретных рабочих процессов машинное обучение Azure для управления экспериментами.

## <a name="view-run-details"></a>Просмотр сведений о выполнении

### <a name="monitor-run-with-jupyter-notebook-widget"></a>Монитор запуска с мини-приложением записной книжки Jupyter
При использовании метода **скриптрунконфиг** для отправки запусков можно наблюдать за ходом выполнения с помощью мини-приложения [Jupyter](https://docs.microsoft.com/python/api/azureml-widgets/azureml.widgets?view=azure-ml-py). Подобно представлению запуска, мини-приложение является асинхронным и предоставляет обновления в реальном времени каждые 10–15 секунд до завершения задания.

1. Просмотрите мини-приложения Jupyter во время ожидания завершения выполнения.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Снимок экрана. Мини-приложение записной книжки Jupyter](./media/how-to-track-experiments/run-details-widget.png)

Вы также можете получить ссылку на тот же дисплей в рабочей области.

```python
print(run.get_portal_url())
```

2. **[Для автоматических запусков машинного обучения]** Доступ к диаграммам из предыдущего выполнения. Замените `<<experiment_name>>` на имя соответствующего эксперимента:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Мини-приложение записной книжки Jupyter для автоматического машинного обучения](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Чтобы изучить дополнительные сведения о конвейере, щелкните нужный конвейер в таблице. Отобразится всплывающее окно с диаграммами с портала Azure.

### <a name="get-log-results-upon-completion"></a>Получение результатов записи по завершении

Обучение модели и мониторинг происходят в фоновом режиме, так что вы можете выполнять другие задачи во время ожидания. Также перед выполнением кода можно подождать, пока модель закончит обучение. При использовании **ScriptRunConfig** можно указать ```run.wait_for_completion(show_output = True)```, чтобы узнать, когда обучение модели будет завершено. Флаг ```show_output``` предоставляет подробные выходные данные. 


### <a name="query-run-metrics"></a>Запрос метрик выполнения

Метрики обученной модели можно просмотреть с помощью ```run.get_metrics()```. Теперь можно получить все метрики, которые были зарегистрированы в примере выше, чтобы определить оптимальную модель.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-the-azure-portal"></a>Просмотр эксперимента на портале Azure

После завершения эксперимента можно просмотреть записи о выполнении в эксперименте. Доступ к журналу можно получить двумя способами:

* Получить URL-адрес для перехода к выполнению напрямую: ```print(run.get_portal_url())```.
* Просмотреть сведения о выполнении, отправив имя выполнения (в этом случае ```run```). Вы получите имя эксперимента, идентификатор, тип, состояние, страницу сведений, ссылку на портал Azure и ссылку на документацию.

Ссылка для выполнения переведет вас на страницу сведений о выполнении на портале Azure. Здесь отображаются все свойства, отслеживаемые метрики, изображения и диаграммы, которые были зарегистрированы во время эксперимента. В этом случае мы регистрируем MSE и альфа-значения.

  ![Сведения о выполнении на портале Azure](./media/how-to-track-experiments/run-details-page.png)

Вы также можете просмотреть любые выходные данные или журналы выполнения, а также скачать моментальный снимок отправленного эксперимента для предоставления общего доступа к папке эксперимента.

### <a name="viewing-charts-in-run-details"></a>Просмотр диаграмм в сведениях о выполнении

Существуют различные методы использования API ведения журналов для регистрации различных типов метрик во время выполнения и просмотра этих метрик в виде графиков на портале Azure. 

|Значение в журнале|Пример кода| Представление на портале|
|----|----|----|
|Массив числовых значений| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|График для одной переменной|
|Одно повторяющееся числовое значение с тем же именем метрики (например, в цикле for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| График для одной переменной|
|Повторяющиеся строки с двумя числовыми столбцами|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Графики для двух переменных|
|Таблица с двумя числовыми столбцами|`run.log_table(name='Sine Wave', value=sines)`|Графики для двух переменных|


## <a name="example-notebooks"></a>Примеры записных книжек
Основные понятия, описанные в этой статье, демонстрируют следующие записные книжки:
* [how-to-use-azureml/training/train-within-notebook](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-within-notebook)
* [how-to-use-azureml/training/train-on-local](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/train-on-local)
* [how-to-use-azureml/training/logging-api/logging-api.ipynb](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/training/logging-api)

[!INCLUDE [aml-clone-in-azure-notebook](../../../includes/aml-clone-for-examples.md)]

## <a name="next-steps"></a>Следующие шаги

Попробуйте следующие шаги, чтобы узнать, как использовать пакет SDK службы "Машинное обучение Azure" для Python:

* Пример регистрации оптимальных моделей и их развертывания см. в руководстве [Руководство № 1. Обучение модели классификации изображений с помощью Машинного обучения Azure](tutorial-train-models-with-aml.md).

* Дополнительные сведения о том, как обучать модели PyTorch с помощью службы "Машинное обучение Azure", см. в [этой статье](how-to-train-pytorch.md).
