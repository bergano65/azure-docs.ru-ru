---
title: Интерпретируемость модели
titleSuffix: Azure Machine Learning service
description: Узнайте, как объяснить, почему ваша модель делает прогнозы с использованием пакета SDK для Azure Machine Learning. Данное свойство можно использовать во время обучения и вывода, чтобы понять, как модели прогнозов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 05/30/2019
ms.openlocfilehash: 4f1dd0bfc0637c6681b513860a63df2b8c35fe5e
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66430855"
---
# <a name="model-interpretability-with-azure-machine-learning-service"></a>Interpretability модели со службой машинного обучения Azure

В этой статье вы узнаете, как объяснить, почему ваша модель приняли прогнозы как с помощью различных пакетов interpretability пакета SDK Azure Machine Learning Python.

Использование классов и методов в пакете SDK, вы можете получить:
+ Функция значения важности для необработанных и спроектированные функции
+ Interpretability на наборы реальных данных в масштабе, во время обучения и вывода.
+ Интерактивные визуализации, которые могут помочь при обнаружение закономерностей в данных и объяснения во время обучения

На этапе обучения цикла разработки конструкторы моделей и средств оценки можно использовать выходные данные interpretability модели для проверки гипотез и доверительные отношения с другими заинтересованными лицами.  Они также используем аналитику в модель для отладки, проверки поведения модели сопоставляет их целей, а также проверки наличия сдвиг.

В машинном обучении **функции** полей данных, использовать для прогнозирования целевой точки данных. Например для прогнозирования кредитного риска, может использовать поля данных, размер учетной записи и срока действия учетной записи. В этом случае возраста, размер учетной записи и срок действия учетных записей являются **функции**. Показатели важности компонентов сообщит о том, влияние каждого поля данных прогнозы модели. Например возраст сильно можно включить в прогноз Хотя размер учетной записи и возраст существенно не воздействуют на точность прогноза. Этот процесс позволяет по обработке и анализу данных объяснить результирующие прогнозы, таким образом, чтобы заинтересованные лица могли видеть какие точек данных наиболее важны в модели.

С помощью этих средств, способных прояснить моделей машинного обучения **глобально ко всем данным**, или **локально в определенных точках данных** с использованием технологий современных образом-удобное и масштабируемое.

Классы interpretability становятся доступными через несколько пакетов SDK. Узнайте, как [установить пакеты SDK для машинного обучения Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py), главный пакет, содержащий функциональные возможности, поддерживаемые корпорацией Майкрософт.

* `azureml.contrib.explain.model`, предварительного просмотра и экспериментальные функции, которые вы можете повторить.

* `azureml.train.automl.automlexplainer` пакет для интерпретации автоматических моделей машинного обучения.

> [!IMPORTANT]
> Содержимое в `contrib` пространство имен поддерживается не полностью. Как экспериментальные функции становятся более зрелыми, они будут перемещены постепенно главного пространства имен.

## <a name="how-to-interpret-your-model"></a>Способ интерпретации модели

Можно применить interpretability классы и методы понимание глобальных поведение модели или конкретных прогнозов. Первое из них называется глобального объяснение и называется локальной объяснение.

Методы можно также классифицировать в зависимости от метода зависит от модели или определенной модели. Некоторые методы предназначенных для определенного типа модели. Например элемента ФИГУРЫ дерева пояснения применяется только к модели на основе дерева. Некоторые методы обрабатывать модели как черный ящик, например отражения пояснения или пояснения ядра ФИГУРЫ. `explain` Пакет использует эти подходы на основе наборов данных, типы моделей и вариантов использования.

Выходные данные — это набор сведения о том, как данной модели делает прогноза, такие как:
* Показатели важности компонентов относительный глобальных и локальных

* Связь глобальных и локальных компонентов и прогнозирования

### <a name="explainers"></a>Explainers

Существует два набора explainers: Прямой Explainers и Explainers Meta в пакете SDK.

__Прямой explainers__ берутся из встроенной библиотеки. Пакет SDK создает оболочку explainers таким образом, общий API и выходной формат. Если вам удобнее работать напрямую с помощью этих explainers, их можно напрямую вызывать вместо того чтобы использовать общий API и выходной формат. Вот список прямых explainers, доступных в пакете SDK.

* **Древовидная пояснения**: Пояснения дерева элемента ФИГУРЫ, усовершенствующий систему полиномиальному времени быстрый ФИГУРЫ значение оценки от алгоритма деревья и ансамбли деревьев.
* **Глубокое пояснения**: На основе объяснение из ФИГУРЫ, глубокое пояснения «алгоритм высокоскоростной приближения для значений ФИГУРЫ в моделей глубокого обучения, который основан на соединение с DeepLIFT описано в статье NIPS ФИГУРЫ. Поддерживаются TensorFlow моделей и моделей Keras, с помощью серверной частью TensorFlow (предусмотрена также предварительная поддержка PyTorch)».
* **Пояснения ядра**: Пояснения ядра ФИГУРЫ использует специально взвешенного локального линейной регрессии для оценки значений ФИГУРЫ для любой модели.
* **Имитировать пояснения**: Пояснения соответствуют основан на идея моделей глобальных символов-заместителей. Модель глобальных символов-заместителей представляют собой по своей природе интерпретации модель, которая обучается приблизительно как можно точнее прогнозы модели черного прямоугольника. Специалист по обработке данных может интерпретировать модель суррогат для выводов о модели черного прямоугольника.
* **Пояснения ТРАВЯНОЙ** (`contrib`): Исходя из ТРАВЯНОЙ, ТРАВЯНОЙ пояснения алгоритм состояние art локального интерпретации зависит от модели объяснения (ТРАВЯНОЙ) для создания моделей локального суррогат. В отличие от глобальных символов-заместителей моделей ТРАВЯНОЙ посвящена обучения моделей локального суррогат для объяснения отдельные прогнозы.
* **ХАН текст пояснения** (`contrib`): ХАН текст пояснения использует иерархические внимания сеть для получения объяснения модель из текстовых данных для модели текста заданного черного прямоугольника. Обучении модели ХАН суррогат для прогнозируемых выходов модель заданного преподавателя. После обучения глобально на основе совокупности текстов, мы добавили шаг fine-tune для определенного документа, чтобы увеличить точность пояснения. ХАН использует двунаправленный RNN с двумя слоями внимания, предложения и word требуют внимания. После того как DNN обученные на основе модели преподавателя и точно настраивать на определенном документе, можно извлечь word importances слоев внимания. Мы пришли к выводу ХАН быть более точным, чем ТРАВЯНОЙ или ФИГУРЫ для текстовых данных, но более затратным в условия также время на обучение. Тем не менее мы внесли улучшения во время обучения, предоставляя пользователю возможность инициализировать сети с помощью GloVe векторные представления слов, несмотря на то, что он работает медленно. Время обучения можно значительно повысить, запустив ХАН на удаленной виртуальной Машине Azure GPU. Реализация ХАН описан в «Иерархические сетей внимания для классификации документов (Иванов вносите, 2016)» ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

__Meta explainers__ автоматически выбрать подходящий прямой пояснения и создавать лучшие объяснение информацию, на основе заданной модели и наборы данных. Meta explainers использовать все библиотеки (ФИГУРЫ, ТРАВЯНОЙ, имитировал т. д.), которые мы интегрированных или разработаны. Ниже приведены explainers meta, доступных в пакете SDK.

* **Табличные пояснения**: Используется с наборы табличных данных.
* **Текст с пояснениями**: Используется с наборами данных текста.

В дополнение к мета-выбор из прямых explainers, meta explainers разрабатывать дополнительные функции на основе базовой библиотеки и улучшить производительность и масштабируемость по прямой explainers.

В настоящее время `TabularExplainer` использует следующую логику для вызова прямого Explainers:

1. Если это модель на основе дерева, применить `TreeExplainer`, else
2. Если это модель DNN, применить `DeepExplainer`, else
3. Считайте это модель черного прямоугольника и применить `KernelExplainer`

Аналитики, встроенной в `TabularExplainer` будет становятся все более сложными, а также дополнительные библиотеки, интегрированы в пакет SDK мы изучаем преимуществ и недостатков каждого пояснения.

`TabularExplainer` также внесла значительные улучшения в функциях и производительности, через прямой Explainers:

* **Формирование сводных данных набора данных инициализации**. В случаях, когда скорость объяснение является наиболее важным суммировать инициализации набора данных и создать небольшой набор Типичными примерами, что ускоряет объяснение глобальные и локальные.
* **Набор данных оценки выборки**. Если пользователь передает большой набор примеров оценки, но не обязательно фактически все они оцениваются, параметр выборки можно задать значение true, чтобы ускорить глобального объяснение.

Ниже показана связь между двумя наборами direct и meta explainers.

[![В машинном обучении Interpretability архитектуры](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Поддерживаемые модели

Любые модели, которые проходят обучение на наборы данных в Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, или `scipy.sparse.csr_matrix` формата поддерживаются interpretability `explain` пакет SDK.

Описание функции принимать в качестве входных данных и модели и конвейеры. Если модель, модель необходимо реализовать прогнозирующую функцию `predict` или `predict_proba` соответствует соглашению Scikit. Если конвейер (имя сценария конвейера), функция объяснение предполагается, что выполняемый сценарий конвейера возвращает прогноз.

### <a name="local-and-remote-compute-target"></a>Локальная и удаленная целевого объекта вычислений

`explain` Пакет предназначен для работы с оба целевых объекта локальных и удаленных вычислений. Если запускать локально, функции пакет SDK не свяжется с любой службы Azure. Можно удаленно запускать объяснение на вычислений машинного обучения Azure и войдите служб машинного обучения Azure запустите журнал info объяснение. После входа в эту информацию, отчеты и визуализации из объяснение легко доступны на портале рабочей области машинного обучения Azure для анализа пользователем.

## <a name="interpretability-in-training"></a>Interpretability обучение

### <a name="train-and-explain-locally"></a>Обучение и объяснить локально

1. Обучение модели в локальные записные книжки Jupyter.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    # Split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data, breast_cancer_data.target, test_size=0.2, random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Вызовите с пояснениями: Для инициализации объекта пояснения, необходимо передать конструктору пояснения модели и данных для обучения. При необходимости также можно передать в функцию имена и выходные данные класса (если это классификации) которых будет использоваться для принятия более информативные объяснения и визуализации. Вот как можно создать экземпляр объекта с пояснениями с помощью [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py) и [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py) локально. `TabularExplainer` вызывает один из трех explainers под (`TreeExplainer`, `DeepExplainer`, или `KernelExplainer`) и автоматически выбирает наиболее подходящей для варианта использования. Однако вы можете вызывать каждый из его трех базовых explainers напрямую.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```

    или

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel

    # "features" and "classes" fields are optional
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Получите функцию глобального значения важности.

    ```python
    # You can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Значения важности локальной функции: используйте следующие вызовы функции для пояснения отдельного экземпляра или группу экземпляров.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    или

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Обучение и объяснить удаленно

Хотя вы можете обучить на различных целевых объектов вычислений, поддерживаемых службой машинного обучения Azure, в примере в этом разделе показано, как это сделать, используя целевой объект вычислений машинного обучения Azure.

1. Создайте скрипт обучения в локальной записной книжки Jupyter (например, run_explainer.py).

    ```python
    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # Train your model here

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0])
    # upload global and local explanation objects to Run History
    client.upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Следуйте инструкциям на [настроить целевые объекты вычисления для обучения модели](how-to-set-up-training-targets.md#amlcompute) Дополнительные сведения о том, как настроить Azure вычислений для машинного обучения в качестве целевого объекта вычислений и отправить запуска обучения.

3. Скачайте объяснение в вашей локальной записной книжки Jupyter.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    # Get model explanation data
    client = ExplanationClient.from_run(run)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Or you can use the saved run.id to retrive the feature importance values
    client = ExplanationClient.from_run_id(ws, experiment_name, run.id)
    explanation = client.download_model_explanation()
    local_importance_values = explanation.local_importance_values
    expected_values = explanation.expected_values
    # Get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Визуализации

С помощью панели мониторинга визуализации понимать и интерпретировать модель:

### <a name="global-visualizations"></a>Глобальные визуализации

Следующие графики укажите глобальное представление обученной модели вместе с ее прогнозы и пояснения.

|График|Описание|
|----|-----------|
|Исследование данных| Обзор набора данных, а также значения прогноза.|
|Глобальные важности|Глобально показаны важные функции top K (можно настроить K). На этой диаграмме полезно для понимания поведения глобального объекта базовой модели.|
|Описание просмотра|Демонстрирует, как компонент отвечает за изменение в модели прогнозирования значений (или вероятности прогноза значений). |
|Сводка| Использует значения со знаком локальную функцию важности по всем точкам данных, чтобы показать распределение влияние каждого из компонентов на прогнозируемое значение.|

[![Панель мониторинга для визуализации глобального](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Локальные визуализации

Можно щелкнуть любой отдельной точке данных в любое время предыдущего графики для загрузки построения важности локальную функцию для точки данных.

|График|Описание|
|----|-----------|
|Локальный важности|Глобально показаны важные функции top K (можно настроить K). Эта диаграмма позволяет понять поведение локального объекта базовой модели в точке данных.|

[![Локальный информационной панели визуализации](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)

Чтобы загрузить панель мониторинга визуализации, используйте следующий код:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Преобразования необработанных признаков

При необходимости можно передать ваш компонент конвейера преобразования пояснения для получения объяснения с точки зрения необработанных признаков до преобразования (а не реконструированные признаки). Если пропустить этот шаг, пояснения содержатся разъяснения с точки зрения реконструированные признаки.

Поддерживаемые преобразования имеет следующий формат же тот, описанными в [sklearn pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Как правило любые преобразования поддерживаются, пока они работают по одному столбцу и, следовательно, очевидно, что один ко многим.

```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# Assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# Append model to preprocessing pipeline.
# Now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                    ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# Pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1], initialization_examples=x_train, features=dataset_feature_names, classes=dataset_classes, transformations=transformations)
```

## <a name="interpretability-in-inference"></a>Interpretability в вывод

Пояснения могут развертываться вместе с исходной модели и может использоваться для предоставления локального описания во время оценки. Процесс развертывания оценки пояснения похоже на развертывание модели и включает следующие шаги:

1. Создайте объект объяснение:

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, x_test)
   ```

1. Создание оценки пояснения, используя объект объяснение:

   ```python
   scoring_explainer = explainer.create_scoring_explainer(x_test)

   # Pickle scoring explainer
   scoring_explainer_path = scoring_explainer.save('scoring_explainer_deploy')
   ```

1. Настройка и регистрация образа, использующего модель оценки пояснения.

   ```python
   # Register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   run.upload_file('breast_cancer_scoring_explainer.pkl', scoring_explainer_path)
   model = run.register_model(model_name='breast_cancer_scoring_explainer', model_path='breast_cancer_scoring_explainer.pkl')
   print(model.name, model.id, model.version, sep = '\t')
   ```

1. [Необязательно] Получение оценки пояснения из облака и проверить пояснения

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import ScoringExplainer

   # Retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'breast_cancer_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # Load scoring explainer from disk
   scoring_explainer = ScoringExplainer.load(scoring_explainer_model_path)

   # Test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Разверните образ на целевой объект вычислений:

   1. Создание оценки файла (до выполнения этого действия, выполните действия, описанные в [развертывать модели в службе машинного обучения Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) регистрируемый исходная модель прогнозирования)

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # Retrieve the path to the model file using the model name
            # Assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('breast_cancer_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # Get predictions and explanations for each data point
            data = np.array(json.loads(raw_data)['data'])
            # Make prediction
            predictions = original_model.predict(data)
            # Retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # You can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Определение конфигурации развертывания (Эта конфигурация зависит от требований вашей модели. В следующем примере определяется конфигурацию, которая использует одно ядро ЦП и 1 ГБ памяти)

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "breastcancer",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for breast cancer data')
        ```

   1. Создайте файл с зависимостями среды

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)


        myenv = CondaDependencies.create(pip_packages=["azureml-defaults", "azureml-explain-model", "azureml-contrib-explain-model"],
                                        conda_packages=["scikit-learn"])

        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Создание пользовательского dockerfile с помощью g ++ установлен

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Развернуть созданный образ (оценка времени: 5 минут)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # Use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # Use configs and models generated above
        service = Webservice.deploy_from_model(workspace=ws,
                                            name='model-scoring-service',
                                            deployment_config=aciconfig,
                                            models=[scoring_explainer_model, original_model],
                                            image_config=image_config)

        service.wait_for_deployment(show_output=True)
        ```

1. тестирование развертывания

    ```python
    import requests

    # Create data to test service with
    x_list = x_test.tolist()
    examples = x_list[:4]
    input_data = "{\"data\": " + str(examples) + "}"

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Очистка: Для удаления развернутой веб-службы используйте `service.delete()`.

## <a name="interpretability-in-automated-ml"></a>Interpretability в автоматических машинного Обучения

Автоматические машинного обучения содержит пакеты, для интерпретации показатели важности компонентов в моделях, подготовленный автоматически. Кроме того классификации сценарии позволяют получить показатели важности компонентов уровня класса. Существует два метода для обеспечения такого поведения в рамках автоматических машинного обучения:

* Чтобы включить показатели важности компонентов для модели обученных совокупности, используйте [ `explain_model()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) функции.

    ```python
    from azureml.train.automl.automlexplainer import explain_model

    shap_values, expected_values, overall_summary, overall_imp, \
        per_class_summary, per_class_imp = explain_model(fitted_model, X_train, X_test)
    ```

* Чтобы включить показатели важности компонентов для каждого отдельного запуска до обучения, задайте `model_explainability` параметр `True` в `AutoMLConfig` объекта, а также предоставляет данные проверки. Затем с помощью [ `retrieve_model_explanation()` ](https://docs.microsoft.com/python/api/azureml-train-automl/azureml.train.automl.automlexplainer?view=azure-ml-py) функции.

    ```python
    from azureml.train.automl.automlexplainer import retrieve_model_explanation

    shap_values, expected_values, overall_summary, overall_imp, per_class_summary, \
        per_class_imp = retrieve_model_explanation(best_run)
    ```

Дополнительные сведения см. в разделе [руководства](how-to-configure-auto-train.md#explain-the-model-interpretability) о включении функции interpretability в автоматических машинного обучения.

## <a name="next-steps"></a>Дальнейшие действия

Коллекция записных книжек Jupyter, которые демонстрируют приведенным выше инструкциям, см. в разделе [примеры записных книжек Azure Machine Learning Interpretability](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
