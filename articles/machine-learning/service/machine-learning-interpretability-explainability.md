---
title: Интерпретируемость модели
titleSuffix: Azure Machine Learning
description: Узнайте, как объяснить, почему модель выполняет прогнозы с помощью пакета SDK для Машинное обучение Azure. Его можно использовать во время обучения и вывода, чтобы понять, как модель выполняет прогнозы.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: mesameki
author: mesameki
ms.reviewer: larryfr
ms.date: 06/21/2019
ms.openlocfilehash: 6b825e61542dabc92baf482ede6c93edc486e059
ms.sourcegitcommit: e97a0b4ffcb529691942fc75e7de919bc02b06ff
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/15/2019
ms.locfileid: "71002359"
---
# <a name="model-interpretability-with-azure-machine-learning"></a>Интерпретируемость модели с помощью Машинное обучение Azure

В этой статье вы узнаете, как объяснить, почему модель выполнила прогнозы с различными пакетами интерпретируемого пакета SDK для Машинное обучение Azure Python.

С помощью классов и методов в пакете SDK можно получить:
+ Значения важности функций для необработанных и инженерных функций
+ Интерпретируемость реальных наборов данных в масштабе, во время обучения и вывода.
+ Интерактивные визуализации, помогающие в обнаружении закономерностей в данных и объяснениях во время обучения

На этапе обучения в цикле разработки конструкторы моделей и оценивают могут использовать результаты интерпретации модели для проверки наличия этих данных и создания отношений доверия с заинтересованными лицами.  Они также используют аналитические данные модели для отладки, проверки поведения модели в соответствии с целями и проверки на наличие смещения.

В машинном обучении **функции** — это поля данных, используемые для прогнозирования целевой точки данных. Например, для прогнозирования кредитного риска можно использовать поля данных для возраста, размера учетной записи и возраста учетной записи. В этом случае срок хранения, размер учетной записи и возраст учетной записи являются **функциями**. Важность функции сообщает, как каждое поле данных затронуло прогнозы модели. Например, возраст может сильно использоваться в прогнозе, тогда как размер и возраст учетной записи не влияют на точность точности прогнозов. Этот процесс позволяет специалистам по обработке и анализу данных объяснить результирующие прогнозы, чтобы заинтересованные лица могли видеть, какие точки данных наиболее важны в модели.

С помощью этих средств можно глобально описать модели машинного обучения **для всех данных**или **локально на определенной точке данных** , используя современные технологии в удобном для использования и масштабируемом режиме.

Доступ к классам интерпретации предоставляется через несколько пакетов SDK. Узнайте, как [установить пакеты SDK для машинное обучение Azure](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py).

* [`azureml.explain.model`](https://docs.microsoft.com/python/api/azureml-explain-model/?view=azure-ml-py)— основной пакет, содержащий функциональные возможности, поддерживаемые корпорацией Майкрософт.

* `azureml.contrib.explain.model`, предварительная версия и экспериментальные возможности, которые можно попробовать.

* `azureml.train.automl.automlexplainer`пакет для интерпретации моделей автоматического машинного обучения.

> [!IMPORTANT]
> Содержимое в `contrib` пространстве имен поддерживается не полностью. По мере того, как экспериментальные функциональные возможности становятся более зрелыми, они постепенно перемещаются в основное пространство имен.

## <a name="how-to-interpret-your-model"></a>Как интерпретировать модель

Вы можете применить классы и методы интерпретации для понимания глобального поведения модели или конкретных прогнозов. Первое из них называется глобальным объяснением, а второе — локальным объяснением.

Методы также можно классифицировать в зависимости от того, является ли метод независимым от модели или зависящим от модели. Некоторые методы предназначены для определенных типов моделей. Например, пояснение к дереву ШАП применимо только к моделям на основе дерева. Некоторые методы обрабатывают модель в виде черного прямоугольника, такого как пояснение к процедуре "имитировать" или ШАП. `explain` Пакет использует различные подходы на основе наборов данных, типов моделей и вариантов использования.

Выходные данные представляют собой набор сведений о том, как данная модель делает прогноз, например:
* Глобальное/локальное относительное важность функции

* Отношение "глобальный/локальный" и "прогноз"

### <a name="explainers"></a>Пояснения

Существует два набора пояснений: Прямые объяснения и meta объяснить в пакете SDK.

__Прямые объяснения__ поступают из интегрированных библиотек. Пакет SDK упаковывает все пояснения таким образом, чтобы они предоставляли общий интерфейс API и формат вывода. Если вы более хорошо знакомы с этими объяснениями, вы можете напрямую вызывать их вместо использования общего интерфейса API и формата вывода. Ниже приведен список прямых объяснений, доступных в пакете SDK.

* **Пояснение к дереву ШАП**: Пояснение к дереву ШАП, в котором основное внимание уделяется алгоритму оценки скорости быстрого ШАП значений времени, характерному для деревьев и это совокупности деревьев.
* **ШАП глубокое пояснение**: Основываясь на пояснениях от ШАП, глубокая пояснения — алгоритм аппроксимации с высокой скоростью для ШАП значений в моделях глубокого обучения, который строится на связи с Диплифт, описанным в документе ШАП НИПС. Модели TensorFlow и модели keras с использованием серверной части TensorFlow поддерживаются (также доступна предварительная поддержка PyTorch).
* **Пояснение ядра ШАП**: В объяснении ядра ШАП используется специально взвешенная локальная линейная регрессия для оценки значений ШАП для любой модели.
* **Описание**действия "имитировать": Концепция имитируется на основе идеи глобальных суррогатных моделей. Глобальная суррогатная модель — это внутренняя интерпретируемая модель, которая обучена для приблизительных прогнозов модели с черным прямоугольником как можно точнее. Анализу данных может интерпретировать суррогатную модель, чтобы рисовать выводы о модели черного ящика. В качестве суррогатной модели можно использовать одну из следующих интерпретируемых моделей: LightGBM (Линеарексплаинаблемодел), линейная регрессия (Линеарексплаинаблемодел), модель с метод стохастического градиента (Сгдексплаинаблемодел) и дерево принятия решений (ДеЦисионтриексплаинаблемодел).


* **Пояснение по важности функции перестановки**: Важность функции перестановки — это методика, используемая для объяснения моделей классификации и регрессии, которые представляют собой [статью о случайных лесах бреиман](https://www.stat.berkeley.edu/%7Ebreiman/randomforest2001.pdf) (см. раздел 10). На высоком уровне, как это работает, случайным образом перетасовывание данные по одному компоненту для всего набора данных и подсчета того, насколько показатель производительности уменьшается. Чем больше это изменение, тем важна эта функция.

* **Травяной пояснением** (`contrib`): В соответствии с ТРАВЯным, ТРАВЯным объяснением для создания локальных суррогатных моделей используется современный интерпретируемый алгоритм, не зависящий от модели (ТРАВЯной). В отличие от глобальных суррогатных моделей, он ориентирован на обучение локальных суррогатных моделей для объяснения отдельных прогнозов.
* **Пояснение к тексту** (`contrib`): В пояснении к тексту используется иерархическая сеть внимания для получения объяснений модели из текстовых данных для данной текстовой модели "черная рамка". Мы обучить заменяющую модель в заданных прогнозируемых выходах модели преподавателей. После обучения по всему тексту совокупности мы добавили шаг точной настройки для конкретного документа, чтобы улучшить точность объяснений. При использовании двунаправленного РНН с двумя слоями для внимания предложений и слов. После того как DNN обучена в модели преподавателей и настроена для конкретного документа, мы можем извлечь важность слова из уровней внимания. Мы обнаружили, что у нас больше, чем «ТРАВЯной» или ШАП для текстовых данных, но и более дорогостоящий в плане обучения. Однако мы внесли улучшения в время обучения, предоставляя пользователю возможность инициализировать сеть с помощью Специализированныйных внедрений Word, хотя она по-прежнему работает слишком долго. Время обучения можно значительно улучшить, выполнив команду "No +" на удаленной виртуальной машине GPU Azure. Реализация класса «со» описывается в разделе [«иерархические сети для классификации документов (Иванов et al., 2016)»](https://www.researchgate.net/publication/305334401_Hierarchical_Attention_Networks_for_Document_Classification).


__Meta-пояснения__ автоматически выбирают подходящее пользовательское объяснение и создают наиболее подходящие сведения на основе заданной модели и наборов данных. В meta объясняются все библиотеки (ШАП, ТРАВЯной, имитировать и т. д.), которые были интегрированы или разработаны. Ниже приведены метаданные meta, доступные в пакете SDK:

* **Табличное пояснение**: Используется с табличными наборами данных.
* **Пояснение к тексту**: Используется с текстовыми наборами данных.
* **Пояснение к изображению**: Используется с наборами данных Image.

Помимо мета-выбора для прямых пояснений, Meta объясняют дополнительные функции поверх базовых библиотек и улучшают скорость и масштабируемость по прямым объяснениям.

В `TabularExplainer` настоящее время использует следующую логику для вызова прямых шапных пояснений:

1. Если это модель на основе дерева, примените ШАП `TreeExplainer`, else
2. Если это модель DNN, примените ШАП `DeepExplainer`, иначе
3. Рассматривайте его как модель с черным ящиком и примените ШАП`KernelExplainer`

Логика, встроенная в `TabularExplainer` , станет более сложной, чем в пакете SDK, и мы будем изучать преимущества и недостатки каждого объяснения.

`TabularExplainer`также обладает значительными улучшениями функций и производительности по прямым объяснениям:

* **Формирование сводных данных инициализации**. В случаях, когда скорость объяснения наиболее важна, мы суммируем набор данных инициализации и создадим небольшой набор репрезентативных образцов, который ускоряет как глобальное, так и локальное объяснение.
* **Выборка набора оценочных данных**. Если пользователь передает большой набор образцов оценки, но на самом деле они не требуют оценки, для параметра выборки можно задать значение true, чтобы ускорить глобальное объяснение.

На следующей диаграмме показана текущая структура прямого и мета объяснения.

[![Архитектура интерпретации Машинное обучение](./media/machine-learning-interpretability-explainability/interpretability-architecture.png)](./media/machine-learning-interpretability-explainability/interpretability-architecture.png#lightbox)


### <a name="models-supported"></a>Поддерживаемые модели

Все модели, обученные в наборах данных `numpy.array`в `pandas.DataFrame`Python `iml.datatypes.DenseData`,, `scipy.sparse.csr_matrix` `explain` или формате, поддерживаются пакетом интерпретации пакета SDK.

Функции пояснения принимают в качестве входных данных модели и конвейеры. Если модель предоставлена, то модель должна реализовать функцию `predict` прогнозирования или `predict_proba` соответствует соглашению Scikit. Если указан конвейер (имя скрипта конвейера), функция пояснения предполагает, что выполняющийся скрипт конвейера возвращает прогноз. Мы поддерживаем модели, обученные с помощью платформ глубокого обучения PyTorch, TensorFlow и keras.

### <a name="local-and-remote-compute-target"></a>Локальный и удаленный целевые объекты вычислений

`explain` Пакет предназначен для работы с локальными и удаленными целевыми объектами вычислений. При локальном запуске функции пакета SDK не будут обращаться к службам Azure. Вы можете запустить объяснение удаленно на Машинное обучение Azure вычислить и записать пояснения в Машинное обучение Azure выполнение служб журнала. После записи этих сведений отчеты и визуализации из описания можно легко найти на рабочая область машинного обучения Azure портале для анализа пользователей.

## <a name="interpretability-in-training"></a>Интерпретируемость при обучении

### <a name="train-and-explain-locally"></a>Обучение и объяснение локально

1. Обучить модель в локальной записной книжке Jupyter.

    ```python
    # load breast cancer dataset, a well-known small dataset that comes with scikit-learn
    from sklearn.datasets import load_breast_cancer
    from sklearn import svm
    from sklearn.model_selection import train_test_split
    breast_cancer_data = load_breast_cancer()
    classes = breast_cancer_data.target_names.tolist()
    
    # split data into train and test
    from sklearn.model_selection import train_test_split
    x_train, x_test, y_train, y_test = train_test_split(breast_cancer_data.data,            
                                                        breast_cancer_data.target,  
                                                        test_size=0.2,
                                                        random_state=0)
    clf = svm.SVC(gamma=0.001, C=100., probability=True)
    model = clf.fit(x_train, y_train)
    ```

2. Вызов пояснения: Чтобы инициализировать объект объяснения, необходимо передать модель и некоторые обучающие данные в конструктор пояснения. Кроме того, можно передать имена функций и имена классов вывода (если вы выполняете классификацию), которые будут использоваться для повышения информативных объяснений и визуализаций. Вот как создать экземпляр объекта пояснения с помощью [табуларексплаинер](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.tabularexplainer?view=azure-ml-py), [мимицексплаинер](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.mimic.mimicexplainer?view=azure-ml-py)и [пфиексплаинер](https://docs.microsoft.com/python/api/azureml-explain-model/azureml.explain.model.permutation.permutation_importance.pfiexplainer?view=azure-ml-py) локально. `TabularExplainer`вызывает один из трех объяснений ШАП под (`TreeExplainer`, `DeepExplainer`или `KernelExplainer`) и автоматически выбирает наиболее подходящий из них для вашего варианта использования. Однако можно вызвать каждый из трех базовых методов объяснения напрямую.

    ```python
    from azureml.explain.model.tabular_explainer import TabularExplainer
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                 x_train, 
                                 features=breast_cancer_data.feature_names, 
                                 classes=classes)
    ```

    или диспетчер конфигурации служб

    ```python
    from azureml.explain.model.mimic.mimic_explainer import MimicExplainer
    
    # you can use one of the following four interpretable models as a global surrogate to the black box model
    from azureml.explain.model.mimic.models.lightgbm_model import LGBMExplainableModel
    from azureml.explain.model.mimic.models.linear_model import LinearExplainableModel
    from azureml.explain.model.mimic.models.linear_model import SGDExplainableModel
    from azureml.explain.model.mimic.models.tree_model import DecisionTreeExplainableModel

    # "features" and "classes" fields are optional
    # augment_data is optional and if true, oversamples the initialization examples to improve surrogate model accuracy to fit original model.  Useful for high-dimensional data where the number of rows is less than the number of columns. 
    # max_num_of_augmentations is optional and defines max number of times we can increase the input data size.
    # LGBMExplainableModel can be replaced with LinearExplainableModel, SGDExplainableModel, or DecisionTreeExplainableModel
    explainer = MimicExplainer(model, 
                               x_train, 
                               LGBMExplainableModel, 
                               augment_data=True, 
                               max_num_of_augmentations=10, 
                               features=breast_cancer_data.feature_names, 
                               classes=classes)
    ```
   или диспетчер конфигурации служб

    ```python
    from azureml.explain.model.permutation.permutation_importance import PFIExplainer 
    
    # "features" and "classes" fields are optional
    explainer = PFIExplainer(model, 
                             features=breast_cancer_data.feature_names, 
                             classes=classes)
    ```

3. Получите глобальные значения важности функций.

    ```python
    # you can use the training data or the test data here
    global_explanation = explainer.explain_global(x_train)
    
    # if you used the PFIExplainer in the previous step, use the next line of code instead
    # global_explanation = explainer.explain_global(x_train, true_labels=y_test)

    # sorted feature importance values and feature names
    sorted_global_importance_values = global_explanation.get_ranked_global_values()
    sorted_global_importance_names = global_explanation.get_ranked_global_names()
    dict(zip(sorted_global_importance_names, sorted_global_importance_values))

    # alternatively, you can print out a dictionary that holds the top K feature names and values
    global_explanation.get_feature_importance_dict()
    ```

4. Получить значения важности локального компонента: используйте следующие вызовы функций для объяснения отдельного экземпляра или группы экземпляров. Обратите внимание, что Пфиексплаинер не поддерживает местные объяснения.

    ```python
    # explain the first data point in the test set
    local_explanation = explainer.explain_local(x_test[0])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

    или диспетчер конфигурации служб

    ```python
    # explain the first five data points in the test set
    local_explanation = explainer.explain_local(x_test[0:4])

    # sorted feature importance values and feature names
    sorted_local_importance_names = local_explanation.get_ranked_local_names()
    sorted_local_importance_values = local_explanation.get_ranked_local_values()
    ```

### <a name="train-and-explain-remotely"></a>Обучение и объяснение удаленно

Хотя вы можете обучить различные целевые объекты вычислений, поддерживаемые Машинное обучение Azure, пример в этом разделе показывает, как это сделать с помощью Машинное обучение Azure целевого объекта вычислений.

1. Создайте сценарий обучения в локальной записной книжке Jupyter (например, run_explainer. корректировка).

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    from azureml.core.run import Run

    run = Run.get_context()
    client = ExplanationClient.from_run(run)

    # write code to get and split your data into train and test sets here
    # write code to train your model here 

    # explain predictions on your local machine
    # "features" and "classes" fields are optional
    explainer = TabularExplainer(model, 
                                x_train, 
                                features=feature_names, 
                                classes=classes)

    # explain overall model predictions (global explanation)
    global_explanation = explainer.explain_global(x_test)
    
    # uploading global model explanation data for storage or visualization in webUX
    # the explanation can then be downloaded on any compute
    # multiple explanations can be uploaded
    client.upload_model_explanation(global_explanation, comment='global explanation: all features')
    # or you can only upload the explanation object with the top k feature info
    #client.upload_model_explanation(global_explanation, top_k=2, comment='global explanation: Only top 2 features')
    ```

2. Следуйте инструкциям по [настройке целевых объектов вычислений для обучения модели](how-to-set-up-training-targets.md#amlcompute) , чтобы узнать, как настроить машинное обучение Azure вычислений в качестве целевого объекта вычислений и отправить обучающий запуск.

3. Скачайте пояснение в локальной записной книжке Jupyter.

    ```python
    from azureml.contrib.explain.model.explanation.explanation_client import ExplanationClient
    
    client = ExplanationClient.from_run(run)
    
    # get model explanation data
    explanation = client.download_model_explanation()
    # or only get the top k (e.g., 4) most important features with their importance values
    explanation = client.download_model_explanation(top_k=4)
    
    global_importance_values = explanation.get_ranked_global_values()
    global_importance_names = explanation.get_ranked_global_names()
    print('global importance values: {}'.format(global_importance_values))
    print('global importance names: {}'.format(global_importance_names))
    ```

## <a name="visualizations"></a>Визуализации

Используйте панель мониторинга визуализации, чтобы понять и интерпретировать модель:

### <a name="global-visualizations"></a>Глобальные визуализации

На следующих диаграммах представлено глобальное представление обученной модели, а также ее прогнозов и объяснений.

|График|Описание|
|----|-----------|
|Исследование данных| Общие сведения о наборе данных и прогнозируемых значениях.|
|Глобальная важность|Отображает первые K (с возможностью настройки K) важные функции в глобальном масштабе. Эта диаграмма полезна для понимания глобального поведения базовой модели.|
|Исследование объяснений|Демонстрируется, как функция отвечает за внесение изменений в прогнозируемые значения модели (или вероятность прогнозируемых значений). |
|Сводка| Использует значения важности подписанных локальных компонентов для всех точек данных, чтобы продемонстрировать распределение влияния каждого компонента на прогнозируемое значение.|

[![Глобальная панель мониторинга визуализации](./media/machine-learning-interpretability-explainability/global-charts.png)](./media/machine-learning-interpretability-explainability/global-charts.png#lightbox)

### <a name="local-visualizations"></a>Локальные визуализации

Можно щелкнуть любую отдельную точку данных в любое время предыдущих построений, чтобы загрузить график важности локальных компонентов для заданной точки данных.

|График|Описание|
|----|-----------|
|Локальная важность|Отображает первые K (с возможностью настройки K) важные функции в глобальном масштабе. Эта диаграмма полезна для понимания локального поведения базовой модели на определенной точке данных.|
|Исследование пертурбатион|Позволяет изменять значения компонентов выбранной точки данных и следить за тем, как эти изменения повлияют на прогнозируемое значение.|
|Отдельное условное ожидание (ICE)| Позволяет изменить значение компонента с минимального на максимальное значение, чтобы увидеть, как изменяется прогноз точки данных при изменении компонента.|

[![Важность локального компонента панели мониторинга визуализации](./media/machine-learning-interpretability-explainability/local-charts.png)](./media/machine-learning-interpretability-explainability/local-charts.png#lightbox)


[![Компонент панели мониторинга визуализации Пертурбатион](./media/machine-learning-interpretability-explainability/perturbation.gif)](./media/machine-learning-interpretability-explainability/perturbation.gif#lightbox)


[![Графики панели мониторинга визуализации](./media/machine-learning-interpretability-explainability/ice-plot.png)](./media/machine-learning-interpretability-explainability/ice-plot.png#lightbox)

Обратите внимание, что перед запуском ядра Jupyter необходимо включить расширения мини-приложений панели мониторинга визуализации.

* Записные книжки Jupyter

    ```shell
    jupyter nbextension install --py --sys-prefix azureml.contrib.explain.model.visualize
    jupyter nbextension enable --py --sys-prefix azureml.contrib.explain.model.visualize
    ```



* Jupyter Labs

    ```shell
    jupyter labextension install @jupyter-widgets/jupyterlab-manager
    jupyter labextension install microsoft-mli-widget
    ```
Чтобы загрузить панель мониторинга визуализации, используйте следующий код:

```python
from azureml.contrib.explain.model.visualize import ExplanationDashboard

ExplanationDashboard(global_explanation, model, x_test)
```

## <a name="raw-feature-transformations"></a>Преобразования необработанных функций

При необходимости можно передать конвейер преобразования компонентов в пояснение, чтобы получить объяснения в терминах необработанных функций перед преобразованием (а не сконструированными функциями). При пропуске этого объяснения пояснения приводятся в терминах инженерных функций.

Формат поддерживаемых преобразований совпадает со стандартом, описанным в [sklearn-Pandas](https://github.com/scikit-learn-contrib/sklearn-pandas). Как правило, все преобразования поддерживаются при условии, что они работают с одним столбцом и, таким образом, явно являются одними и многими. 

Можно объяснить необработанные функции с помощью или `sklearn.compose.ColumnTransformer` списка допустимых кортежей трансформатора. В ячейке, `sklearn.compose.ColumnTransformer`расположенной ниже, используется. 

```python
from sklearn.compose import ColumnTransformer

numeric_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='median')),
    ('scaler', StandardScaler())])

categorical_transformer = Pipeline(steps=[
    ('imputer', SimpleImputer(strategy='constant', fill_value='missing')),
    ('onehot', OneHotEncoder(handle_unknown='ignore'))])

preprocessor = ColumnTransformer(
    transformers=[
        ('num', numeric_transformer, numeric_features),
        ('cat', categorical_transformer, categorical_features)])

# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# append classifier to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', preprocessor),
                      ('classifier', LogisticRegression(solver='lbfgs'))])


# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=preprocessor)
```

Если вы хотите выполнить пример со списком заданных кортежей трансформатора, используйте следующий код: 
```python
from sklearn.pipeline import Pipeline
from sklearn.impute import SimpleImputer
from sklearn.preprocessing import StandardScaler, OneHotEncoder
from sklearn.linear_model import LogisticRegression
from sklearn_pandas import DataFrameMapper

# assume that we have created two arrays, numerical and categorical, which holds the numerical and categorical feature names

numeric_transformations = [([f], Pipeline(steps=[('imputer', SimpleImputer(
    strategy='median')), ('scaler', StandardScaler())])) for f in numerical]

categorical_transformations = [([f], OneHotEncoder(
    handle_unknown='ignore', sparse=False)) for f in categorical]

transformations = numeric_transformations + categorical_transformations

# append model to preprocessing pipeline.
# now we have a full prediction pipeline.
clf = Pipeline(steps=[('preprocessor', DataFrameMapper(transformations)),
                      ('classifier', LogisticRegression(solver='lbfgs'))])

# clf.steps[-1][1] returns the trained classification model
# pass transformation as an input to create the explanation object
# "features" and "classes" fields are optional
tabular_explainer = TabularExplainer(clf.steps[-1][1],
                                     initialization_examples=x_train,
                                     features=dataset_feature_names,
                                     classes=dataset_classes,
                                     transformations=transformations)
```

## <a name="interpretability-at-inferencing-time"></a>Интерпретируемость при изучении времени

Объяснение может быть развернуто вместе с исходной моделью и может использоваться во время оценки для предоставления сведений о локальных объяснениях. Мы также предлагаем более понятные пояснения к оценкам, которые позволят повысить эффективность интерпретации. Процесс развертывания пояснения к более светлой оценке аналогичен развертыванию модели и включает следующие шаги:




1. Создайте объект пояснения (например, с помощью Табуларексплаинер):

   ```python
   from azureml.contrib.explain.model.tabular_explainer import TabularExplainer

   explainer = TabularExplainer(model, 
                                initialization_examples=x_train, 
                                features=dataset_feature_names, 
                                classes=dataset_classes, 
                                transformations=transformations)
   ```

1. Создайте пояснение к оценке с помощью объекта пояснения:

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import KernelScoringExplainer, save

   # create a lightweight explainer at scoring time
   scoring_explainer = KernelScoringExplainer(explainer)

   # pickle scoring explainer
   # pickle scoring explainer locally
   OUTPUT_DIR = 'my_directory'
   save(scoring_explainer, directory=OUTPUT_DIR, exist_ok=True)
   ```

1. Настройка и регистрация образа, использующего модель пояснения к оценкам.

   ```python
   # register explainer model using the path from ScoringExplainer.save - could be done on remote compute
   # scoring_explainer.pkl is the filename on disk, while my_scoring_explainer.pkl will be the filename in cloud storage
   run.upload_file('my_scoring_explainer.pkl', os.path.join(OUTPUT_DIR, 'scoring_explainer.pkl'))
   
   scoring_explainer_model = run.register_model(model_name='my_scoring_explainer', 
                                                model_path='my_scoring_explainer.pkl')
   print(scoring_explainer_model.name, scoring_explainer_model.id, scoring_explainer_model.version, sep = '\t')
   ```

1. Используемых Получение объяснения оценки из облака и тестирование объяснений

   ```python
   from azureml.contrib.explain.model.scoring.scoring_explainer import load

   # retrieve the scoring explainer model from cloud"
   scoring_explainer_model = Model(ws, 'my_scoring_explainer')
   scoring_explainer_model_path = scoring_explainer_model.download(target_dir=os.getcwd(), exist_ok=True)

   # load scoring explainer from disk
   scoring_explainer = load(scoring_explainer_model_path)

   # test scoring explainer locally
   preds = scoring_explainer.explain(x_test)
   print(preds)
   ```

1. Разверните образ в целевом объекте вычислений:

   1. Создайте файл оценки (перед выполнением этого шага выполните действия, описанные в разделе [Развертывание моделей с машинное обучение Azure](https://docs.microsoft.com/azure/machine-learning/service/how-to-deploy-and-where) для регистрации исходной модели прогнозирования).

        ```python
        %%writefile score.py
        import json
        import numpy as np
        import pandas as pd
        import os
        import pickle
        from sklearn.externals import joblib
        from sklearn.linear_model import LogisticRegression
        from azureml.core.model import Model

        def init():

            global original_model
            global scoring_model

            # retrieve the path to the model file using the model name
            # assume original model is named original_prediction_model
            original_model_path = Model.get_model_path('original_prediction_model')
            scoring_explainer_path = Model.get_model_path('my_scoring_explainer')

            original_model = joblib.load(original_model_path)
            scoring_explainer = joblib.load(scoring_explainer_path)

        def run(raw_data):
            # get predictions and explanations for each data point
            data = pd.read_json(raw_data)
            # make prediction
            predictions = original_model.predict(data)
            # retrieve model explanations
            local_importance_values = scoring_explainer.explain(data)
            # you can return any data type as long as it is JSON-serializable
            return {'predictions': predictions.tolist(), 'local_importance_values': local_importance_values}
        ```

   1. Определите конфигурацию развертывания (Эта конфигурация зависит от требований модели. В следующем примере определяется конфигурация, использующая одно ядро ЦП и 1 ГБ памяти.

        ```python
        from azureml.core.webservice import AciWebservice

        aciconfig = AciWebservice.deploy_configuration(cpu_cores=1,
                                                       memory_gb=1,
                                                       tags={"data": "NAME_OF_THE_DATASET",
                                                             "method" : "local_explanation"},
                                                       description='Get local explanations for NAME_OF_THE_PROBLEM')
        ```

   1. Создание файла с зависимостями окружения

        ```python
        from azureml.core.conda_dependencies import CondaDependencies

        # WARNING: to install this, g++ needs to be available on the Docker image and is not by default (look at the next cell)

        azureml_pip_packages = ['azureml-defaults', 'azureml-contrib-explain-model', 'azureml-core', 'azureml-telemetry', 'azureml-explain-model']
 

        # specify CondaDependencies obj
        myenv = CondaDependencies.create(conda_packages=['scikit-learn', 'pandas'],
                                         pip_packages=['sklearn-pandas'] + azureml_pip_packages,
                                         pin_sdk_version=False)


        with open("myenv.yml","w") as f:
            f.write(myenv.serialize_to_string())

        with open("myenv.yml","r") as f:
            print(f.read())
        ```

   1. Создание настраиваемого dockerfile с установленным g + +

        ```python
        %%writefile dockerfile
        RUN apt-get update && apt-get install -y g++
        ```

   1. Развертывание созданного образа (оценка времени: 5 минут)

        ```python
        from azureml.core.webservice import Webservice
        from azureml.core.image import ContainerImage

        # use the custom scoring, docker, and conda files we created above
        image_config = ContainerImage.image_configuration(execution_script="score.py",
                                                        docker_file="dockerfile",
                                                        runtime="python",
                                                        conda_file="myenv.yml")

        # use configs and models generated above
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

    # create data to test service with
    examples = x_list[:4]
    input_data = examples.to_json()

    headers = {'Content-Type':'application/json'}

    # send request to service
    resp = requests.post(service.scoring_uri, input_data, headers=headers)

    print("POST to url", service.scoring_uri)
    # can covert back to Python objects from json string if desired
    print("prediction:", resp.text)
    ```

1. Очистка: Для удаления развернутой веб-службы используйте `service.delete()`.




## <a name="next-steps"></a>Следующие шаги

Чтобы просмотреть набор записных книжек Jupyter, демонстрирующих приведенные выше инструкции, см. [Пример записных книжек машинное обучение Azure интерпретируемость](https://github.com/Azure/MachineLearningNotebooks/tree/master/how-to-use-azureml/explain-model).
