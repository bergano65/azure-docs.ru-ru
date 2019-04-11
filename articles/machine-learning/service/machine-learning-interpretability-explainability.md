---
title: Интерпретируемость модели
titleSuffix: Azure Machine Learning service
description: Сведения об использовании Interpretability пакета SDK для Azure Machine Learning, чтобы объяснить, почему ваша модель осуществляет прогнозирование. Данное свойство можно использовать во время обучения и выводов, чтобы понять, как модели прогнозов.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 04/09/2019
ms.openlocfilehash: fbcafb61ecd69f58bb3c14d1b15f36f1b21f2833
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469783"
---
# <a name="azure-machine-learning-interpretability-sdk"></a>Interpretability машинного обучения Azure SDK

В этой статье вы узнаете, как объяснить, почему ваша модель приняли прогнозы сделал с помощью Interpretability пакета SDK для Azure Machine Learning. Описание вашей модели является возможность важно по следующим причинам:

* Клиентов и заинтересованных лиц должны знать **если они могут положиться на прогнозы модели делает**.
* Как специалист по обработке данных, необходимо понять **как запрос к модели, чтобы получать ценные сведения о**. Необходимо также средства для принятия обоснованных решений на **как улучшить модель**.
* Как компания, необходимо понимать **поведение модели с различными ввода дистрибутивов** и **модели поведения во время анализа определенных входных данных**.

Machine learning interpretability важно в два этапа цикла разработки в машинном обучении: **обучения** времени и **выводов** времени:

* Во время **обучения**: Конструкторы моделей и оценивающих требуют interpretability средства для объяснения выходные данные модели заинтересованным лицам, чтобы установить доверие. Они также должны ценные сведения о модели, чтобы они могли отладка модели и принимать решения о том, совпадает ли поведение своих задач. Наконец необходимо убедиться, что модель не настроена.
* Во время **выводов**: Прогнозы должны быть или для использования модели. Например, почему модели запрета ссуды ипотеки или предсказать, что портфелем капиталовложений выполняет подвергается риску?

Interpretability пакета SDK для Azure Machine Learning включает в себя технологии разработанных корпорацией Майкрософт и проверенные сторонних библиотек (например, ФИГУРЫ и ТРАВЯНОЙ). Пакет SDK создает общий API через встроенной библиотеки и интегрирует службы машинного обучения Azure. Используя этот пакет SDK, вы сможете разъяснить моделей машинного обучения **глобально ко всем данным**, или **локально на точку данных** с использованием технологий современных образом-удобное и масштабируемое.

## <a name="how-does-it-work"></a>Как это работает?

Azure Machine Learning Interpretability могут применяться понимание глобальных поведение или конкретных прогнозы модели. Первое из них называется глобального объяснение и называется локальной объяснение.

Azure Machine Learning Interpretability методы можно также классифицировать в зависимости от метода зависит от модели или определенной модели. Некоторые методы предназначенных для определенного типа модели. Например элемента ФИГУРЫ дерева пояснения применяется только к модели на основе дерева. Некоторые методы обрабатывать модели как черный ящик, например отражения пояснения или пояснения ядра ФИГУРЫ. Machine Learning Interpretability пакет SDK использует эти подходы на основе наборов данных, типы моделей и вариантов использования.

Azure Machine Learning Interpretability возвращает набор данных как модель делает прогноза. Сведения включают элементы, такие как:

* Показатели важности компонентов относительный глобальных и локальных
* Связь глобальных и локальных компонентов и прогнозирования

## <a name="architecture"></a>Архитектура

Azure SDK Interpretability обучения машины структурированы в двух пакетов Python:

* [azureml.Explain.model](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py) -основной пакет, который содержит функциональные возможности, которые поддерживаются корпорацией Майкрософт.
* `azureml.contrib.explain.model` — Предварительный просмотр и экспериментальные функции, которые вы можете повторить.

    > [!IMPORTANT]
    > Вещи в contrib поддерживаются не полностью. Как экспериментальные функции становятся более зрелыми, они будут перемещены постепенно главного пакета.

### <a name="explainers"></a>Explainers

Interpretability пакета SDK для Azure Machine Learning образуется два набора explainers: Прямой Explainers и Meta Explainers.

__Прямой explainers__ берутся из встроенной библиотеки. Пакет SDK создает оболочку explainers таким образом, общий API и выходной формат. Вот список прямых explainers, доступных в пакете SDK.

> [!TIP]
> Если вам удобнее работать напрямую с помощью этих explainers, их можно напрямую вызывать вместо того чтобы использовать общий API и выходной формат.

* **Древовидная пояснения**: Пояснения дерева элемента ФИГУРЫ, усовершенствующий систему полиномиальному времени быстрый ФИГУРЫ значение оценки от алгоритма деревья и ансамбли деревьев.
* **Глубокое пояснения**: На основе объяснение из ФИГУРЫ, глубокое пояснения «алгоритм высокоскоростной приближения для значений ФИГУРЫ в моделей глубокого обучения, который основан на соединение с DeepLIFT описано в статье NIPS ФИГУРЫ. Поддерживаются TensorFlow моделей и моделей Keras, с помощью серверной частью TensorFlow (предусмотрена также предварительная поддержка PyTorch)».
* **Пояснения ядра**: Пояснения ядра ФИГУРЫ использует специально взвешенного локального линейной регрессии для оценки значений ФИГУРЫ для любой модели.
* **Имитировать пояснения**: Пояснения соответствуют основан на идея моделей глобальных символов-заместителей. Модель глобальных символов-заместителей представляют собой по своей природе интерпретации модель, которая обучается приблизительно как можно точнее прогнозы модели черного прямоугольника. Специалист по обработке данных может интерпретировать модель суррогат для выводов о модели черного прямоугольника.
* **Пояснения ТРАВЯНОЙ**: Исходя из ТРАВЯНОЙ, ТРАВЯНОЙ пояснения алгоритм состояние art локального интерпретации зависит от модели объяснения (ТРАВЯНОЙ) для создания моделей локального суррогат. В отличие от глобальных символов-заместителей моделей ТРАВЯНОЙ посвящена обучения моделей локального суррогат для объяснения отдельные прогнозы.
* **ХАН текст пояснения**: ХАН текст пояснения использует иерархические внимания сеть для получения объяснения модель из текстовых данных для модели текста заданного черного прямоугольника. Обучении модели ХАН суррогат для прогнозируемых выходов модель заданного преподавателя. После обучения глобально на основе совокупности текстов, мы добавили шаг fine-tune для определенного документа, чтобы увеличить точность пояснения. ХАН использует двунаправленный RNN с двумя слоями внимания, предложения и word требуют внимания. После того как DNN обученные на основе модели преподавателя и точно настраивать на определенном документе, можно извлечь word importances слоев внимания. Мы пришли к выводу ХАН быть более точным, чем ТРАВЯНОЙ или ФИГУРЫ для текстовых данных, но более затратным в условия также время на обучение. Тем не менее мы внесли улучшения во время обучения, предоставляя пользователю возможность инициализировать сети с помощью GloVe векторные представления слов, несмотря на то, что он работает медленно. Время обучения можно значительно повысить, запустив ХАН на удаленной виртуальной Машине Azure GPU. Реализация ХАН описан в «Иерархические сетей внимания для классификации документов (Иванов вносите, 2016)» ([https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf](https://www.cs.cmu.edu/~diyiy/docs/naacl16.pdf)).

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

[![M— Machine Learning Interpretability архитектура](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)

### <a name="models-supported"></a>Поддерживаемые модели

Любые модели, которые проходят обучение на наборы данных в Python `numpy.array`, `pandas.DataFrame`, `iml.datatypes.DenseData`, или `scipy.sparse.csr_matrix` формата поддерживаются с помощью пакета SDK Interpretability машины обучения.

Описание функции принимать в качестве входных данных и модели и конвейеры. Если модель, модель необходимо реализовать прогнозирующую функцию `predict` или `predict_proba` соответствует соглашению Scikit. Если конвейер (имя сценария конвейера), функция объяснение предполагается, что выполняемый сценарий конвейера возвращает прогноз.

### <a name="local-and-remote-compute-target"></a>Локальная и удаленная целевого объекта вычислений

Пакет SDK Interpretability машины обучения предназначен для работы с оба целевых объекта локальных и удаленных вычислений. Если запускать локально, функции пакет SDK не свяжется с любой службы Azure. Можно удаленно запускать объяснение на вычислений машинного обучения Azure и войдите служб машинного обучения Azure запустите журнал info объяснение. После входа в эту информацию, отчеты и визуализации из объяснение легко доступны на портале рабочей области машинного обучения Azure для анализа пользователем.

## <a name="train-and-explain-locally"></a>Обучение и объяснить локально

1. Обучение модели в локальные записные книжки Jupyter. 

    ``` python
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

2. Вызовите с пояснениями: Чтобы инициировать объект пояснения, необходимо передать модели, обучающих данных, компоненты (необязательно), а имена классов выходные данные (если классификации) для пояснения. Вот как можно создать экземпляр объекта с пояснениями с помощью [TabularExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [MimicExplainer](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py), и `LimeExplainer` локально. `TabularExplainer` вызывает один из трех explainers под (`TreeExplainer`, `DeepExplainer`, или `KernelExplainer`) и автоматически выбирает наиболее подходящей для варианта использования. Однако вы можете вызывать каждый из его трех базовых explainers напрямую.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    ```
    или
    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    explainer = MimicExplainer(model, x_train, LGBMExplainableModel, features=breast_cancer_data.feature_names, classes=classes)
    ```

3. Получите функцию глобального значения важности.

    ```python
    # You can use the training data or the test data here. 
    global_explanation = explainer.explain_global(x_train)
    # Sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))
    ```

4. Значения важности локальной функции: используйте следующие вызовы функции для пояснения отдельного экземпляра или группу экземпляров.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```
    или
    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4,:])
    
    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

## <a name="train-and-explain-remotely"></a>Обучение и объяснить удаленно

Хотя вы можете обучить на различных целевых объектов вычислений, поддерживаемых службой машинного обучения Azure, в примере в этом разделе показано, как это сделать с помощью AMLCompute.

1. Создайте скрипт обучения в локальной записной книжки Jupyter (например, run_explainer.py).

    ``` python  
    run = Run.get_context()
    client = ExplanationClient.from_run(run)
    
    # Train your model here

    # explain predictions on your local machine    
    explainer = TabularExplainer(model, x_train, features=breast_cancer_data.feature_names, classes=classes)
    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    # explain local data points (individual instances)
    local_explanation = explainer.explain_local(x_test[0,:])
    # upload global and local explanation objects to Run History
    upload_model_explanation(run, local_explanation, top_k=2, comment='local explanation: top 2 features')
    # Uploading global model explanation data for storage or visualization in webUX
    # The explanation can then be downloaded on any compute
    # Multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # Or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Следуйте инструкциям на [настроить целевые объекты вычисления для обучения модели](how-to-set-up-training-targets.md#amlcompute) Дополнительные сведения о том, как настроить Azure вычислений для машинного обучения в качестве целевого объекта вычислений и отправить запуска обучения.

3. Скачайте объяснение в вашей локальной записной книжки Jupyter. 
    > [!IMPORTANT]
    > Вещи в contrib поддерживаются не полностью. Как экспериментальные функции становятся более зрелыми, они будут перемещены постепенно главного пакета.

    ``` python
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

## <a name="next-steps"></a>Следующие шаги

Коллекция записных книжек Jupyter, которые демонстрируют приведенным выше инструкциям, см. в разделе [примеры записных книжек Azure Machine Learning Interpretability](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).