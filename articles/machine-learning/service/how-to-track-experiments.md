---
title: Эксперименты машинного обучения & метрики
titleSuffix: Azure Machine Learning
description: Отслеживайте эксперименты машинного обучения Azure и отслеживайте показатели запуска для улучшения процесса создания модели. Добавьте ведение журнала в сценарий обучения и просмотрите записанные результаты выполнения.  Используйте запуск. log, запустите. start_logging или Скриптрунконфиг.
services: machine-learning
author: sdgilley
ms.author: sgilley
ms.reviewer: sgilley
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/05/2019
ms.custom: seodec18
ms.openlocfilehash: 3de34c1da20df17fb5fb65cef28669fb73ff33a5
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74978566"
---
# <a name="monitor-azure-ml-experiment-runs-and-metrics"></a>Мониторинг запусков и метрик экспериментов машинного обучения Azure
[!INCLUDE [applies-to-skus](../../../includes/aml-applies-to-basic-enterprise-sku.md)]

Улучшите процесс создания модели, отслеживая эксперименты и отслеживайте метрики запуска. В этой статье вы узнаете, как добавить код ведения журнала в обучающий сценарий, отправить запуск эксперимента, отслеживать выполнение и проверить результаты в Машинное обучение Azure.

> [!NOTE]
> Машинное обучение Azure также может записывать в журнал данные из других источников во время обучения, такие как автоматические запуски машинного обучения или контейнер DOCKER, выполняющий задание обучения. Эти журналы не документированы. Если возникли проблемы и вы можете обратиться в службу поддержки Майкрософт, они смогут использовать эти журналы во время устранения неполадок.

> [!TIP]
> Сведения в этом документе предназначены главным образом для специалистов по обработке и анализу данных и разработчиков, желающих отслеживать процесс обучения модели. Если вы являетесь администратором, который заинтересован в наблюдении за использованием ресурсов и событиями из машинного обучения Azure, таких как квоты, завершенные обучающие запуски или завершенные развертывания моделей, см. раздел [мониторинг машинное обучение Azure](monitor-azure-machine-learning.md).

## <a name="available-metrics-to-track"></a>Доступные метрики для мониторинга

Следующие метрики можно добавить к выполнению во время обучения эксперимента. Чтобы просмотреть подробный список того, что можно отслеживать при выполнении, ознакомьтесь со [справочной документацией по классу Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|Тип| Функция Python | Заметки|
|----|:----|:----|
|Скалярные значения |Функция:<br>`run.log(name, value, description='')`<br><br>Пример:<br>run.log("accuracy", 0.95) |Запишите числовое или строковое значение в выполнение с заданным именем. Запись метрики в выполнение приводит к тому, что метрика будет сохранена в записи о выполнении в эксперименте.  Одну и ту же метрику можно несколько раз записать в рамках выполнения. Результат будет считаться вектором этой метрики.|
|Списки|Функция:<br>`run.log_list(name, value, description='')`<br><br>Пример:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Запишите список значений в выполнение с заданным именем.|
|Строка|Функция:<br>`run.log_row(name, description=None, **kwargs)`<br>Пример:<br>run.log_row("Y over X", x=1, y=0.4) | С помощью *log_row* создается метрика с несколькими столбцами, как описано в kwargs. Каждый именованный параметр создает столбец с указанным значением.  *log_row* можно вызвать один раз, чтобы записать произвольный кортеж, или несколько раз в цикле, чтобы создать полную таблицу.|
|Таблицы|Функция:<br>`run.log_table(name, value, description='')`<br><br>Пример:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Запишите объект словаря в выполнение с заданным именем. |
|Образы|Функция:<br>`run.log_image(name, path=None, plot=None)`<br><br>Пример:<br>`run.log_image("ROC", plt)` | Запишите изображение в запись о выполнении. Используйте log_image, чтобы записать файл изображения или график matplotlib в выполнение.  Эти изображения будут видимы, и их можно сравнить в записи о выполнении.|
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
  
## <a name="option-1-use-start_logging"></a>Вариант 1. Использование start_logging

**start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.

В следующем примере простая модель sklearn Ridge обучается локально в локальной записной книжке Jupyter. Дополнительные сведения о отправке экспериментов в различные среды см. в разделе [Настройка целевых объектов вычислений для обучения модели с помощью машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

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

2. Добавьте Отслеживание экспериментов с помощью пакета SDK для Машинное обучение Azure и отправьте сохраненную модель в запись запуска эксперимента. Следующий код добавляет теги, журналы и отправляет файл модели в выполнение эксперимента.

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

## <a name="option-2-use-scriptrunconfig"></a>Вариант 2. Использование ScriptRunConfig

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

### <a name="view-activequeued-runs-from-the-browser"></a>Просмотреть активные и поставленные в очереди запуски из браузера

Целевые объекты вычислений, используемые для обучения моделей, являются общим ресурсом. Таким образом, у них может быть несколько запусков в очереди или активной в определенный момент времени. Чтобы просмотреть запуски для определенного целевого объекта вычислений из браузера, выполните следующие действия.

1. В [машинное обучение Azure Studio](https://ml.azure.com/)выберите рабочую область, а затем в левой части страницы выберите пункт __вычислить__ .

1. Выберите __обучающие кластеры__ , чтобы отобразить список целевых объектов вычислений, используемых для обучения. Затем выберите кластер.

    ![Выбор кластера для обучения](./media/how-to-track-experiments/select-training-compute.png)

1. Выберите __запуски__. Отобразится список запусков, использующих этот кластер. Чтобы просмотреть сведения о конкретном выполнении, используйте ссылку в столбце __выполнить__ . Чтобы просмотреть сведения для эксперимента, используйте ссылку в столбце __эксперимент__ .

    ![Выберите запуски для учебного кластера](./media/how-to-track-experiments/show-runs-for-compute.png)
    
    > [!TIP]
    > Запуск может содержать дочерние тесты, поэтому одно задание обучения может привести к созданию нескольких записей.

После завершения выполнения он больше не отображается на этой странице. Чтобы просмотреть сведения о завершенных запусках, перейдите к разделу __эксперименты__ в студии и выберите эксперимент и запустите. Дополнительные сведения см. в разделе " [метрики выполнения запроса](#queryrunmetrics) ".

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

2. **[Для автоматических запусков машинного обучения]** Доступ к диаграммам из предыдущего выполнения. Замените `<<experiment_name>>` именем соответствующего эксперимента:

   ``` 
   from azureml.widgets import RunDetails
   from azureml.core.run import Run

   experiment = Experiment (workspace, <<experiment_name>>)
   run_id = 'autoML_my_runID' #replace with run_ID
   run = Run(experiment, run_id)
   RunDetails(run).show()
   ```

   ![Мини-приложение записной книжки Jupyter для автоматического машинного обучения](./media/how-to-track-experiments/azure-machine-learning-auto-ml-widget.png)


Чтобы просмотреть дополнительные сведения о конвейере, щелкните конвейер, который вы хотите изучить в таблице, и диаграммы будут отображены во всплывающем окне из Машинное обучение Azure Studio.

### <a name="get-log-results-upon-completion"></a>Получение результатов записи по завершении

Обучение модели и мониторинг происходят в фоновом режиме, так что вы можете выполнять другие задачи во время ожидания. Также перед выполнением кода можно подождать, пока модель закончит обучение. При использовании **ScriptRunConfig** можно указать ```run.wait_for_completion(show_output = True)```, чтобы узнать, когда обучение модели будет завершено. Флаг ```show_output``` предоставляет подробные выходные данные. 

<a id="queryrunmetrics"></a>

### <a name="query-run-metrics"></a>Запрос метрик выполнения

Метрики обученной модели можно просмотреть с помощью ```run.get_metrics()```. Теперь можно получить все метрики, которые были зарегистрированы в примере выше, чтобы определить оптимальную модель.

<a name="view-the-experiment-in-the-web-portal"></a>
## <a name="view-the-experiment-in-your-workspace-in-azure-machine-learning-studiohttpsmlazurecom"></a>Просмотр эксперимента в рабочей области в [машинное обучение Azure Studio](https://ml.azure.com)

После завершения эксперимента можно просмотреть записи о выполнении в эксперименте. Доступ к журналу можно получить из [машинное обучение Azure Studio](https://ml.azure.com).

Перейдите на вкладку эксперименты и выберите свой эксперимент. Вы перейдете на панель мониторинга запуска эксперимента, где можно увидеть метрики и диаграммы, регистрируемые для каждого запуска. В этом случае мы регистрируем MSE и альфа-значения.

  ![Сведения о выполнении в Машинное обучение Azure Studio](./media/how-to-track-experiments/experiment-dashboard.png)

Можно выполнить детализацию до определенного запуска, чтобы просмотреть его выходные данные или журналы, или скачать моментальный снимок отправленного эксперимента, чтобы предоставить доступ к папке эксперимента другим пользователям.

### <a name="viewing-charts-in-run-details"></a>Просмотр диаграмм в сведениях о выполнении

Существуют различные способы использования API-интерфейсов ведения журнала для записи различных типов метрик во время выполнения и их просмотра в виде диаграмм в Машинное обучение Azure Studio.

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

## <a name="next-steps"></a>Дальнейшие действия

Попробуйте следующие шаги, чтобы узнать, как использовать пакет SDK службы "Машинное обучение Azure" для Python:

* Пример регистрации оптимальных моделей и их развертывания см. в руководстве [Руководство № 1. Обучение модели классификации изображений с помощью Машинного обучения Azure](tutorial-train-models-with-aml.md).

* Дополнительные сведения о том, как обучать модели PyTorch с помощью службы "Машинное обучение Azure", см. в [этой статье](how-to-train-pytorch.md).
