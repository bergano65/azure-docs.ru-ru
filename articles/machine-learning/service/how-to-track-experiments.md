---
title: Метрики журнала во время учебные запуски
titleSuffix: Azure Machine Learning service
description: Узнайте, как добавить ведение журнала в сценарий обучения, отправить эксперимент, проверить прогресс выполняемого задания и просмотреть результаты выполнения. Можно отслеживать ваши эксперименты и отслеживать метрики для усовершенствования процесса создания модели.
services: machine-learning
author: heatherbshapiro
ms.author: hshapiro
ms.service: machine-learning
ms.subservice: core
ms.workload: data-services
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59a801fd149a0f647298d94522f9eefc1ea6eb9f
ms.sourcegitcommit: 5f348bf7d6cf8e074576c73055e17d7036982ddb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/16/2019
ms.locfileid: "59609473"
---
# <a name="log-metrics-during-training-runs-in-azure-machine-learning"></a>Метрики журнала во время обучения выполняется в машинном обучении Azure

В этой статье вы научитесь Добавление ведения журнала в скрипте обучения, отправить выполнения эксперимента, мониторинг выполнения и просмотра результатов выполнения в службе машинного обучения Azure. Улучшения в процессе создания модели, отслеживая экспериментов и метрики мониторинга. 

## <a name="list-of-training-metrics"></a>Список метрик обучения 

Следующие метрики можно добавить к выполнению во время обучения эксперимента. Чтобы просмотреть подробный список того, что можно отслеживать при выполнении, ознакомьтесь со [справочной документацией по классу Run](https://docs.microsoft.com/python/api/azureml-core/azureml.core.run(class)?view=azure-ml-py).

|type| Функция Python | Примечания|
|----|:----|:----|
|Скалярные значения |Функция:<br>`run.log(name, value, description='')`<br><br>Пример:<br>run.log("accuracy", 0.95) |Запишите числовое или строковое значение в выполнение с заданным именем. Запись метрики в выполнение приводит к тому, что метрика будет сохранена в записи о выполнении в эксперименте.  Одну и ту же метрику можно несколько раз записать в рамках выполнения. Результат будет считаться вектором этой метрики.|
|Списки|Функция:<br>`run.log_list(name, value, description='')`<br><br>Пример:<br>run.log_list("accuracies", [0.6, 0.7, 0.87]) | Запишите список значений в выполнение с заданным именем.|
|Строка|Функция:<br>`run.log_row(name, description=None, **kwargs)`<br>Пример:<br>run.log_row("Y over X", x=1, y=0.4) | С помощью *log_row* создается метрика с несколькими столбцами, как описано в kwargs. Каждый именованный параметр создает столбец с указанным значением.  *log_row* можно вызвать один раз, чтобы записать произвольный кортеж, или несколько раз в цикле, чтобы создать полную таблицу.|
|Таблица|Функция:<br>`run.log_table(name, value, description='')`<br><br>Пример:<br>run.log_table("Y over X", {"x":[1, 2, 3], "y":[0.6, 0.7, 0.89]}) | Запишите объект словаря в выполнение с заданным именем. |
|Образы|Функция:<br>`run.log_image(name, path=None, plot=None)`<br><br>Пример:<br>`run.log_image("ROC", plt)` | Запишите изображение в запись о выполнении. Используйте log_image, чтобы записать файл изображения или график matplotlib в выполнение.  Эти изображения будут видимы, и их можно сравнить в записи о выполнении.|
|Добавление тега к выполнению|Функция:<br>`run.tag(key, value=None)`<br><br>Пример:<br>run.tag("selected", "yes") | Добавление тега к выполнению с использованием строкового ключа и дополнительного значения строки.|
|Отправка файла или каталога|Функция:<br>`run.upload_file(name, path_or_stream)`<br> <br> Пример:<br>run.upload_file("best_model.pkl", "./model.pkl") | Отправка файла в запись о выполнении. Выполняет автоматическую запись файла в указанном каталоге вывода, который по умолчанию имеет значение "./outputs" для большинства типов выполнения.  Используйте upload_file только в том случае, если необходимо отправить дополнительные файлы или не указан выходной каталог. Мы советуем добавлять `outputs` к имени для того, чтобы файл отправлялся в выходной каталог. Вы можете перечислить все файлы, связанные с этой записью о выполнении, вызвав `run.get_file_names()`.|

> [!NOTE]
> Метрики для скалярных значений, списков, строк и таблиц могут быть типа: число с плавающей точкой, целое число или строка.

## <a name="start-logging-metrics"></a>Начало ведения журнала метрик

Если вы хотите отслеживать свой эксперимент, необходимо добавить код, чтобы запустить ведение журнала при отправке выполнения. Ниже приведены способы активации отправки выполнений:
* __Run.start_logging__ добавляет функции регистрации в сценарий обучения и запускает интерактивный сеанс регистрации в определенном эксперименте. **start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.
* __ScriptRunConfig__ добавляет функции ведения журнала в сценарий обучения и загружает всю папку сценария с выполнением.  **ScriptRunConfig** является классом для настройки конфигурации выполнений в сценарии. С использованием этого параметра можно добавить код мониторинга, чтобы получать уведомления о завершении или получить визуальное мини-приложение для мониторинга.

## <a name="set-up-the-workspace"></a>Настройка рабочей области
Перед тем, как включать ведение журнала и отправлять эксперимент, необходимо настроить рабочую область.

1. Загрузите рабочую область. Дополнительные сведения о настройке конфигурации рабочей области, выполните действия, описанные в [создать рабочую область службы машинного обучения Azure](setup-create-workspace.md#sdk).

   ```python
   from azureml.core import Experiment, Run, Workspace
   import azureml.core
  
   ws = Workspace(workspace_name = <<workspace_name>>,
               subscription_id = <<subscription_id>>,
               resource_group = <<resource_group>>)
   ```
  
## <a name="option-1-use-startlogging"></a>Вариант 1. Использование start_logging

**start_logging** создает интерактивное выполнение для использования в таких сценариях, как записные книжки. Все метрики, записанные во время сеанса, добавляются в запись о выполнении эксперимента.

В следующем примере простая модель sklearn Ridge обучается локально в локальной записной книжке Jupyter. Дополнительные сведения об отправке экспериментов в различные среды см. в статье [Выбор и использование целевого объекта вычислений для обучения вашей модели](https://docs.microsoft.com/azure/machine-learning/service/how-to-set-up-training-targets).

1. Создайте сценарий обучения в локальной записной книжке Jupyter. 

   ``` python
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
   experiment = Experiment(workspace = ws, name = "train-within-notebook")
  
   # Create a run object in the experiment
   run = experiment.start_logging()# Log the algorithm parameter alpha to the run
   run.log('alpha', 0.03)

   # Create, fit, and test the scikit-learn Ridge regression model
   regression_model = Ridge(alpha=0.03)
   regression_model.fit(data['train']['X'], data['train']['y'])
   preds = regression_model.predict(data['test']['X'])

   # Output the Mean Squared Error to the notebook and to the run
   print('Mean Squared Error is', mean_squared_error(data['test']['y'], preds))
   run.log('mse', mean_squared_error(data['test']['y'], preds))

   # Save the model to the outputs directory for capture
   joblib.dump(value=regression_model, filename='outputs/model.pkl')

   # Take a snapshot of the directory containing this notebook
   run.take_snapshot('./')

   # Complete the run
   run.complete()
  
   ```

Сценарий оканчивается на ```run.complete()```, что помечает выполнение как завершенное.  Обычно эта функция используется в сценариях интерактивной записной книжки.

## <a name="option-2-use-scriptrunconfig"></a>Вариант 2. Использование ScriptRunConfig

[**ScriptRunConfig** ](https://docs.microsoft.com/python/api/azureml-core/azureml.core.scriptrunconfig?view=azure-ml-py) — это класс, для настройки конфигурации для скрипта выполняется. С использованием этого параметра можно добавить код мониторинга, чтобы получать уведомления о завершении или получить визуальное мини-приложение для мониторинга.

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
   from azureml.core.runconfig import RunConfiguration

   # Editing a run configuration property on-fly.
   run_config_user_managed = RunConfiguration()

   run_config_user_managed.environment.python.user_managed_dependencies = True

   # You can choose a specific Python environment by pointing to a Python path 
   #run_config.environment.python.interpreter_path = '/home/user/miniconda3/envs/sdk2/bin/python'
   ```

4. Отправьте сценарий ```train.py``` для выполнения в среде, управляемой пользователем. Вся папка сценария отправляется для обучения, в том числе файл ```mylib.py```.

   ```python
   from azureml.core import ScriptRunConfig
  
   experiment = Experiment(workspace=ws, name="train-on-local")
   src = ScriptRunConfig(source_directory = './', script = 'train.py', run_config = run_config_user_managed)
   run = experiment.submit(src)
   ```

## <a name="manage-a-run"></a>Управление запуска

[Start "," монитор "и" Отмена учебные запуски](how-to-manage-runs.md) статье указаны конкретные рабочие процессы машинного обучения Azure, сведения об управлении экспериментов.

## <a name="view-run-details"></a>Просмотр сведений о выполнении

### <a name="monitor-run-with-jupyter-notebook-widgets"></a>Мониторинг выполнения с помощью мини-приложений записных книжек Jupyter
При использовании метода **ScriptRunConfig** для отправки выполнений можно просмотреть прогресс выполнения с помощью мини-приложения записной книжки Jupyter. Подобно представлению запуска, мини-приложение является асинхронным и предоставляет обновления в реальном времени каждые 10–15 секунд до завершения задания.

1. Просмотрите мини-приложения Jupyter во время ожидания завершения выполнения.

   ```python
   from azureml.widgets import RunDetails
   RunDetails(run).show()
   ```

   ![Снимок экрана. Мини-приложение записной книжки Jupyter](./media/how-to-track-experiments/widgets.PNG)

2. **[Для автоматических запусков машинного обучения]** Доступ к диаграммам из предыдущего выполнения. Замените `<<experiment_name>>` с именем соответствующего эксперимента:

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

После завершения эксперимента можно просмотреть записи о выполнении в эксперименте. Обращаться к журналу можно двумя способами.

* Получить URL-адрес для перехода к выполнению напрямую: ```print(run.get_portal_url())```.
* Просмотреть сведения о выполнении, отправив имя выполнения (в этом случае ```run```). Вы получите имя эксперимента, идентификатор, тип, состояние, страницу сведений, ссылку на портал Azure и ссылку на документацию.

Ссылка для выполнения переведет вас на страницу сведений о выполнении на портале Azure. Здесь отображаются все свойства, отслеживаемые метрики, изображения и диаграммы, которые были зарегистрированы во время эксперимента. В этом случае мы регистрируем MSE и альфа-значения.

  ![Сведения о выполнении на портале Azure](./media/how-to-track-experiments/run-details-page-web.PNG)

Вы также можете просмотреть любые выходные данные или журналы выполнения, а также скачать моментальный снимок отправленного эксперимента для предоставления общего доступа к папке эксперимента.

### <a name="viewing-charts-in-run-details"></a>Просмотр диаграмм в сведениях о выполнении

Существуют различные методы использования API ведения журналов для регистрации различных типов метрик во время выполнения и просмотра этих метрик в виде графиков на портале Azure. 

|Значение в журнале|Пример кода| Представление на портале|
|----|----|----|
|Массив числовых значений| `run.log_list(name='Fibonacci', value=[0, 1, 1, 2, 3, 5, 8, 13, 21, 34, 55, 89])`|График для одной переменной|
|Одно повторяющееся числовое значение с тем же именем метрики (например, в цикле for)| `for i in tqdm(range(-10, 10)):    run.log(name='Sigmoid', value=1 / (1 + np.exp(-i))) angle = i / 2.0`| График для одной переменной|
|Повторяющиеся строки с двумя числовыми столбцами|`run.log_row(name='Cosine Wave', angle=angle, cos=np.cos(angle))   sines['angle'].append(angle)      sines['sine'].append(np.sin(angle))`|Графики для двух переменных|
|Таблица с двумя числовыми столбцами|`run.log_table(name='Sine Wave', value=sines)`|Графики для двух переменных|

<a name="auto"></a>
## <a name="understanding-automated-ml-charts"></a>Основные сведения об автоматических диаграммах ML

После того, как вы отправите автоматическое задание ML в записную книжку, в рабочей области службы машинного обучения появится журнал выполнений этого задания. 

См. также:
+ [Диаграммы и кривые для моделей классификации](#classification)
+ [Диаграммы и графы для моделей регрессии](#regression)
+ [Моделирование возможностей объяснения](#model-explain-ability-and-feature-importance)


### <a name="view-the-run-charts"></a>Просмотр диаграмм выполнения

1. Перейдите в рабочую область. 

1. Выберите **Эксперименты** на крайней левой панели рабочей области.

   ![Снимок экрана с меню эксперимента](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_menu.PNG)

1. Выберите эксперимент, который вас интересует.

   ![Список экспериментов](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_list.PNG)

1. Выберите номер выполнения в таблице.

   ![Запуск эксперимента](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_run.PNG)

1. Выберите в таблице номер итерации для модели, которую вы хотите изучить подробнее.

   ![Модель эксперимента](./media/how-to-track-experiments/azure-machine-learning-auto-ml-experiment_model.PNG)



### <a name="classification"></a>классификация;

Для каждой модели классификации, которую вы создаете с помощью автоматического машинного обучения в Машинном обучении Azure, отображаются следующие диаграммы: 
+ [матрица неточностей](#confusion-matrix);
+ [диаграмма с соотношением полноты и точности](#precision-recall-chart);
+ [ROC-кривые](#roc);
+ [кривая точности прогнозов](#lift-curve);
+ [кривая прироста](#gains-curve);
+ [график калибровки](#calibration-plot).

#### <a name="confusion-matrix"></a>Матрица неточностей

Матрица неточностей используется для описания эффективности модели классификации. Каждая строка сопоставлена с экземпляром реального класса, а каждый столбец — с экземпляром прогнозируемого класса. Матрица неточностей позволяет отслеживать правильно и неправильно классифицированные метки для выбранной модели.

Для решения проблем с классификацией Машинное обучение Azure автоматически предоставляет матрицу неточностей для каждой созданной модели. В каждой матрице неточностей автоматическое ML отображает зеленым цветом правильно классифицированные метки, а красным — неправильно классифицированные метки. Размер каждого круга соответствует количеству выборок для этой ячейки. Кроме того, в смежной линейчатой диаграмме представлены данные о частоте для каждой прогнозируемой и реальной метки. 

Пример 1 Модель классификации с низкой точностью ![Модель классификации с низкой точностью](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix1.PNG)

Пример 2 Модель классификации с высокой (идеальной) точностью ![Модель классификации с высокой точностью](./media/how-to-track-experiments/azure-machine-learning-auto-ml-confusion_matrix2.PNG)


#### <a name="precision-recall-chart"></a>Диаграмма соотношения полноты и точности

С помощью этой диаграммы вы можете сравнить кривые полноты и точности для каждой модели, чтобы определить модель с допустимым соотношением этих параметров для поставленной бизнес-задачи. На этой схеме отображается среднее соотношение полноты и точности на макро-уровне, микро-уровне, а также для всех классов модели.

Термин "точность" здесь обозначает способность классификатора правильно назначить метки всем экземплярам. Термин "полнота" обозначает способность классификатора найти все экземпляры для определенной метки. Кривая соотношения полноты и точности демонстрирует связь между этими двумя параметрами. В идеальном случае модель демонстрирует 100 % точность и полноту.

Пример 1 Модель классификации с низкой точностью и низкой полнотой ![Модель классификации с низкой точностью и низкой полнотой](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall1.PNG)

Пример 2 Модель классификации с близкими к 100 % точностью и полнотой (идеальный вариант) ![Модель классификации с высокой точностью и высокой полнотой](./media/how-to-track-experiments/azure-machine-learning-auto-ml-precision_recall2.PNG)

#### <a name="roc"></a>ROC

Кривая ROC отображает правильно и неправильно классифицированные метки для конкретной модели. Кривая ROC может быть недостаточно информативной, если обучение моделей выполняется по наборам данных с высоким уровнем смещения, так как она не отображает ложноположительные метки.

Пример 1 Модель классификации с небольшим количеством правильных меток и большим количеством неправильных меток![Модель классификации с небольшим количеством правильных меток и большим количеством неправильных меток](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc1.PNG)

Пример 2 Модель классификации с большим количеством правильных меток и небольшим количеством неправильных меток![Модель классификации с большим количеством правильных меток и небольшим количеством неправильных меток](./media/how-to-track-experiments/azure-machine-learning-auto-ml-roc2.PNG)

#### <a name="lift-curve"></a>Диаграмма точности прогнозов

Вы можете сравнить показатели модели, автоматически созданной Машинным обучением Azure, с базовыми показателями, чтобы оценить прирост значений для конкретной модели.

Диаграммы точности прогнозов используются для оценки эффективности модели классификации. Они демонстрируют, насколько улучшаются результаты с применением модели по сравнению с результатами без модели. 

Пример 1 Модель демонстрирует результаты хуже, чем у модели случайного выбора ![Модель классификации с результатами хуже, чем у модели случайного выбора](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve1.PNG)

Пример 2 Модель демонстрирует результаты лучше, чем у модели случайного выбора ![Модель классификации с результатами лучше, чем у модели случайного выбора](./media/how-to-track-experiments/azure-machine-learning-auto-ml-lift_curve2.PNG)

#### <a name="gains-curve"></a>Кривая прироста

Кривая прироста оценивает эффективность модели классификации по каждому сегменту данных. Она демонстрирует отдельно для каждого процентиля из набора данных, насколько ожидаемые результаты лучше, чем у модели случайного выбора.

Использование диаграммы суммарного прироста позволяет выбрать параметры отсечения для классификации по процентному уровню, который соответствующий требуемому приросту для модели. Здесь представлена та же информация, что и на диаграмме точности прогнозов, но в другом режиме отображения.

Пример 1 Модель классификации с минимальным приростом ![Модель классификации с минимальным приростом](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve1.PNG)

Пример 2 Модель классификации с существенным приростом ![Модель классификации с существенным приростом](./media/how-to-track-experiments/azure-machine-learning-auto-ml-gains_curve2.PNG)

#### <a name="calibration-plot"></a>График калибровки

Для всех задач классификации вы можете просмотреть строку калибровки с макро-усреднением, микро-усреднением и для каждого класса в выбранной прогнозной модели. 

График калибровки отображает достоверность прогнозной модели. На нем отображается зависимость между прогнозируемой и фактической вероятностями, где "вероятность" обозначает шанс принадлежности конкретного экземпляра к определенной метке. Хорошо откалиброванная модель дает результаты, близкие к графику y=x, где обеспечивается высокая надежность прогнозов. Модель с высокой степенью достоверности приближается к графику y=0, где присутствует прогнозируемая вероятность, но отсутствует фактическая.

Пример 1 Относительно неплохо откалиброванная модель ![ Относительно неплохо откалиброванная модель](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve1.PNG)

Пример 2 Модель с высокой степенью достоверности ![Модель с высокой степенью достоверности](./media/how-to-track-experiments/azure-machine-learning-auto-ml-calib_curve2.PNG)

### <a name="regression"></a>Регрессия
Для каждой модели регрессии, которую вы создаете с помощью автоматического машинного обучения в Машинном обучении Azure, отображаются следующие графики: 
+ [Прогнозируемые и истинные значения](#pvt)
+ [Гистограмма остатков](#histo)

<a name="pvt"></a>

#### <a name="predicted-vs-true"></a>Прогнозируемые и Да

Прогнозируемые и истинные значения. Эта диаграмма отображает зависимость между прогнозируемым значением для проблемы регрессии. Эта диаграмма позволяет оценить эффективность модели: чем ближе прогнозируемые значения к графику y=x, тем выше точность прогнозной модели.

После каждого запуска для каждой модели регрессии отображается диаграмма прогнозируемых и истинных значений. Для обеспечения конфиденциальности данных значения группируются по ячейкам, размер каждой из которых отображается в виде линейчатой диаграммы в нижней части области диаграммы. Вы можете сравнить результаты прогнозной модели с более светлой областью, которая отображает идеальные значения, требуемые для этой модели, с учетом допусков.

Пример 1 Модель регрессии с низкой точностью прогнозирования ![Модель регрессии с низкой точностью прогнозирования](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression1.PNG)

Пример 2 Модель регрессии с высокой точностью прогнозирования ![Модель регрессии с высокой точностью прогнозирования](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression2.PNG)

<a name="histo"></a>

#### <a name="histogram-of-residuals"></a>Гистограмма остатков

Гистограмма остатков отображает разность между наблюдаемыми и прогнозируемыми значениями y. Чтобы отобразить допуск ошибок с низким смещением, гистограмма остатков должна иметь форму колокола с центром около 0. 

Пример 1 Модель регрессии с заметным смещением ошибок![Модель регрессии с заметным смещением ошибок](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression3.PNG)

Пример 2 Модель регрессии с более равномерным распределением ошибок ![Модель регрессии с более равномерным распределением ошибок](./media/how-to-track-experiments/azure-machine-learning-auto-ml-regression4.PNG)

### <a name="model-explain-ability-and-feature-importance"></a>Возможность объяснить модель и важность компонентов

Важность компонентов дает оценку ценности для каждого компонента, который использовался при построении модели. Вы можете просмотреть оценки важности компонентов для модели в целом, а также отдельно для каждого класса прогнозной модели. Для каждого компонента демонстрируется его важность для каждого класса и для модели в целом.

![Возможность объяснения компонентов](./media/how-to-track-experiments/azure-machine-learning-auto-ml-feature_explain1.PNG)

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
