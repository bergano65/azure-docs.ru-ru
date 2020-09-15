---
title: Руководство по обучению модели Машинного обучения Azure на Python
titleSuffix: Azure Machine Learning
description: В этом руководстве описаны базовые конструктивные шаблоны Машинного обучения Azure и показано, как обучить простую модель scikit-learn, созданную на основе набора данных о заболеваемости диабетом.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.author: sgilley
author: sdgilley
ms.date: 08/25/2020
ms.custom: devx-track-python
ms.openlocfilehash: 7052617eb83dbd07c2d6938dcbb7a38ba19f3aad
ms.sourcegitcommit: c52e50ea04dfb8d4da0e18735477b80cafccc2cf
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/08/2020
ms.locfileid: "89536238"
---
# <a name="tutorial-train-your-first-ml-model"></a>Руководство по обучению модели Машинного обучения

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Это руководство представляет собой **вторую часть серии, состоящей из двух частей**. В предыдущем руководстве показано, как [создать рабочую область и выбрать среду разработки](tutorial-1st-experiment-sdk-setup.md). В этом руководстве описаны базовые конструктивные шаблоны Машинного обучения Azure и показано, как обучить простую модель scikit-learn, созданную на основе набора данных о заболеваемости диабетом. Изучив это руководство, вы получите практические знания о пакете SDK и сможете перейти к более сложным экспериментам и рабочим процессам.

В этом руководстве вы выполнили следующие задачи.

> [!div class="checklist"]
> * подключение рабочей области и создание эксперимента;
> * загрузка данных и обучение моделей scikit-learn;
> * просмотр результатов обучения в Студии;
> * Извлечение наиболее эффективной модели

## <a name="prerequisites"></a>Предварительные требования

Требуется только одно — выполнить инструкции из первой части этого руководства под названием [Настройка среды и рабочей области](tutorial-1st-experiment-sdk-setup.md).

В этой части руководства описано, как выполнить код в примере записной книжки Jupyter *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb*, открытой в конце первой части. В этой статье рассматривается тот же код, который приведен в записной книжке.

## <a name="open-the-notebook"></a>Открытие записной книжки

1. Войдите в [Студию машинного обучения Azure](https://ml.azure.com/).

1. Откройте записную книжку **tutorial-1st-experiment-sdk-train.ipynb** в своей папке, как показано в [первой части](tutorial-1st-experiment-sdk-setup.md#open).

**Не** создавайте *другую* записную книжку в интерфейсе Jupyter! Записная книжка *tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb* содержит **все данные и код**, необходимые для работы с этим руководством.

## <a name="connect-workspace-and-create-experiment"></a>Подключение рабочей области и создание эксперимента

<!-- nbstart https://raw.githubusercontent.com/Azure/MachineLearningNotebooks/master/tutorials/create-first-ml-experiment/tutorial-1st-experiment-sdk-train.ipynb -->

> [!TIP]
> Содержимое файла _tutorial-1st-experiment-sdk-train.ipynb_. Перейдите в записную книжку Jupyter, чтобы вы могли просматривать его во время выполнения кода. Чтобы выполнить одну ячейку кода в записной книжке, щелкните эту ячейку и нажмите клавиши **SHIFT+ВВОД**. Или запустите всю записную книжку, выбрав **Запустить все** в верхней части панели инструментов.


Импортируйте класс `Workspace` и загрузите сведения о подписке из файла `config.json` с помощью функции `from_config().`. При этом по умолчанию выполняется поиск JSON-файла в текущем каталоге, но вы также можете задать параметр пути, чтобы указать на файл, используя `from_config(path="your/file/path")`. Если эта записная книжка из рабочей области выполняется на облачном сервере записных книжек, это значит, что файл автоматически размещается в корневом каталоге.

Если при выполнении следующего кода будет запрошена дополнительная аутентификация, просто вставьте ссылку в браузере и введите маркер проверки подлинности. Кроме того, при наличии нескольких клиентов, связанных с пользователем, необходимо добавить следующие строки:

```python
from azureml.core.authentication import InteractiveLoginAuthentication
interactive_auth = InteractiveLoginAuthentication(tenant_id="your-tenant-id")
```

Дополнительные сведения о проверке подлинности см. в статье [Проверка подлинности в службе "Машинное обучение Azure"](https://aka.ms/aml-notebook-auth).


```python
from azureml.core import Workspace
ws = Workspace.from_config()
```

Теперь создайте эксперимент в рабочей области. Эксперимент — это еще один базовый облачный ресурс, представляющий собой коллекцию проб (отдельных запусков модели). В этом руководстве предполагается, что вы используете эксперимент для создания запусков модели и отслеживания ее обучения в студии машинного обучения Azure. К параметрам относится ссылка на рабочую область и имя строки для эксперимента.


```python
from azureml.core import Experiment
experiment = Experiment(workspace=ws, name="diabetes-experiment")
```

## <a name="load-data-and-prepare-for-training"></a>Загрузка данных и подготовка к обучению

Для целей этого учебника используется набор данных о диабете. В нем есть такие признаки, как возраст, пол и индекс массы тела для прогнозирования заболеваемости диабетом. Загрузите данные из класса [Azure Open Datasets](https://azure.microsoft.com/services/open-datasets/) и разделите их на обучающие и тестовые наборы с помощью `train_test_split()`. Эта функция разделяет данные так, что для тестирования модели после обучения используются неизвестные модели данные.


```python
from azureml.opendatasets import Diabetes
from sklearn.model_selection import train_test_split

x_df = Diabetes.get_tabular_dataset().to_pandas_dataframe().dropna()
y_df = x_df.pop("Y")

X_train, X_test, y_train, y_test = train_test_split(x_df, y_df, test_size=0.2, random_state=66)
```

## <a name="train-a-model"></a>Обучение модели

Небольшой цикл обучения простой модели scikit-learn можно выполнить локально, но при обучении множества итераций с использованием десятков разных вариантов перестановок признаков и настройки гиперпараметров можно легко запутаться в том, какие модели и каким образом были обучены. Следующий конструктивный шаблон демонстрирует, как с помощью пакета SDK без труда отслеживать обучение в облаке.

Создайте скрипт, который обучает модели гребневой регрессии в цикле с помощью разных значений альфа для гиперпараметров.


```python
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.externals import joblib
import math

alphas = [0.1, 0.2, 0.3, 0.4, 0.5, 0.6, 0.7, 0.8, 0.9, 1.0]

for alpha in alphas:
    run = experiment.start_logging()
    run.log("alpha_value", alpha)
    
    model = Ridge(alpha=alpha)
    model.fit(X=X_train, y=y_train)
    y_pred = model.predict(X=X_test)
    rmse = math.sqrt(mean_squared_error(y_true=y_test, y_pred=y_pred))
    run.log("rmse", rmse)
    
    model_name = "model_alpha_" + str(alpha) + ".pkl"
    filename = "outputs/" + model_name
    
    joblib.dump(value=model, filename=filename)
    run.upload_file(name=model_name, path_or_stream=filename)
    run.complete()
```

Приведенный выше код выполняет следующие задачи:

1. Для каждого значения гиперпараметра альфа в массиве `alphas` создается новый запуск в рамках эксперимента. Значение альфа записывается в журнал, чтобы различать запуски.
1. При каждом запуске создается экземпляр модели, которая обучена для выполнения прогнозирования. Для фактических и спрогнозированных значений рассчитывается среднеквадратичная ошибка, которая записывается в журнал запуска. Таким образом, при запуске используются метаданные, связанные со значением альфа и точностью среднеквадратичной ошибки.
1. Кроме того, перед каждым запуском выполняется сериализация модели, файл которой передается для запуска. Это позволяет скачивать файлы модели из запуска в Студию.
1. В конце каждой итерации запуск завершается путем вызова `run.complete()`.

Завершив обучение, вызовите переменную `experiment`, чтобы получить ссылку на эксперимент в Студии.

```python
experiment
```

<table style="width:100%"><tr><th>Имя</th><th>Рабочая область</th><th>Страница отчета</th><th>Страница документации</th></tr><tr><td>diabetes-experiment</td><td>имя_вашей_рабочей_области</td><td>Ссылка на Студию машинного обучения Azure</td><td>Ссылка на документацию</td></tr></table>

## <a name="view-training-results-in-studio"></a>Просмотр результатов обучения в Студии

**Ссылка на Студию машинного обучения Azure** ведет на главную страницу эксперимента. Здесь отображаются все запуски, выполненные в ходе эксперимента. Все пользовательские значения (в нашем случае это `alpha_value` и `rmse`) становятся полями для каждого запуска. Их также можно использовать для построения диаграмм. Чтобы создать диаграмму с записанной в журнале метрикой, щелкните "Добавить диаграмму" и выберите метрику.

При обучении моделей в масштабе сотен и тысяч отдельных запусков эта страница позволяет удобно просматривать все обученные модели, а также то, как они были обучены и как изменялись ваши уникальные метрики с течением времени.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/experiment-main.png" alt-text="Главная страница эксперимента в Студии":::


Щелкните ссылку с номером запуска в столбце `RUN NUMBER`, чтобы просмотреть страницу для отдельного запуска. На вкладке **Сведения** отображаются подробные сведения о каждом запуске. На вкладке **Выходные данные и журналы** доступен файл `.pkl` для модели, который отправлялся для запуска при каждой итерации обучения. Здесь можно скачать файл модели, чтобы не переобучать ее вручную.

:::image type="content" source="./media/tutorial-1st-experiment-sdk-train/model-download.png" alt-text="Страница сведений о запуске в Студии":::

## <a name="get-the-best-model"></a>Отбор наилучшей модели

Файлы модели из эксперимента можно скачать как в Студии, так и с помощью программных средств. Следующий код последовательно анализирует каждый запуск в эксперименте и оценивает зарегистрированные в журнале метрики и сведения о запуске (которые содержат run_id). Это позволяет отследить запуск с наилучшим результатом. В нашем примере это запуск с наименьшим значением среднеквадратичной ошибки.

```python
minimum_rmse_runid = None
minimum_rmse = None

for run in experiment.get_runs():
    run_metrics = run.get_metrics()
    run_details = run.get_details()
    # each logged metric becomes a key in this returned dict
    run_rmse = run_metrics["rmse"]
    run_id = run_details["runId"]
    
    if minimum_rmse is None:
        minimum_rmse = run_rmse
        minimum_rmse_runid = run_id
    else:
        if run_rmse < minimum_rmse:
            minimum_rmse = run_rmse
            minimum_rmse_runid = run_id

print("Best run_id: " + minimum_rmse_runid)
print("Best run_id rmse: " + str(minimum_rmse))    
```
```output
Best run_id: 864f5ce7-6729-405d-b457-83250da99c80
Best run_id rmse: 57.234760283951765
```

Запуск с наилучшим результатом можно получить по его идентификатору, используя конструктор `Run` вместе с объектом эксперимента. Затем нужно вызвать `get_file_names()`, чтобы просмотреть все доступные для скачивания файлы для этого запуска. В нашем примере вы отправляли один файл для каждого запуска во время обучения.


```python
from azureml.core import Run
best_run = Run(experiment=experiment, run_id=minimum_rmse_runid)
print(best_run.get_file_names())
```

```output
['model_alpha_0.1.pkl']
```

Вызовите `download()` для объекта запуска, указав имя файла модели для скачивания. По умолчанию эта функция сохраняет файлы в текущем каталоге.


```python
best_run.download_file(name="model_alpha_0.1.pkl")
```
<!-- nbend -->

## <a name="clean-up-resources"></a>Очистка ресурсов

Не выполняйте инструкции из этого раздела, если вы собираетесь изучать другие руководства по Машинному обучению Azure.

### <a name="stop-the-compute-instance"></a>Остановка вычислительной операции

[!INCLUDE [aml-stop-server](../../includes/aml-stop-server.md)]

### <a name="delete-everything"></a>Удаление всех ресурсов

[!INCLUDE [aml-delete-resource-group](../../includes/aml-delete-resource-group.md)]

Вы также можете сохранить группу ресурсов, но удалить одну рабочую область. Отобразите свойства рабочей области и нажмите кнопку **Удалить**.

## <a name="next-steps"></a>Дальнейшие действия

В этом руководстве описано, как выполнять следующие задачи:

> [!div class="checklist"]
> * подключение рабочей области и создание эксперимента;
> * загрузка данных и обучение моделей scikit-learn;
> * просмотр результатов обучения в Студии и получение моделей.

[Разверните модель](tutorial-deploy-models-with-aml.md) с помощью службы "Машинное обучение Azure".
См. дополнительные сведения о разработке экспериментов [автоматического машинного обучения](tutorial-auto-train-models.md).
