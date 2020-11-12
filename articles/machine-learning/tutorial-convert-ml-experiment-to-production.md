---
title: Преобразование кода записной книжки в скрипты Python
titleSuffix: Azure Machine Learning
description: Преобразуйте экспериментальные записные книжки машинного обучения в готовый к применению в реальных условиях код с помощью шаблона кода MLOpsPython. Этот код можно затем протестировать, развернуть и автоматизировать.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 04/30/2020
ms.custom: devx-track-python
ms.openlocfilehash: 2f7746f079e740493348731376d0a5a7b1a9e954
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/04/2020
ms.locfileid: "93317862"
---
# <a name="tutorial-convert-ml-experiments-to-production-python-code"></a>Руководство по Преобразование экспериментов машинного обучения в рабочий код Python

Из этого руководства вы узнаете, как преобразовать записные книжки Juptyer в скрипты Python, чтобы адаптировать их к тестированию и автоматизации с помощью шаблона кода MLOpsPython и Машинного обучения Azure. Как правило, этот процесс используется для получения кода эксперимента и обучения из записной книжки Juptyer и преобразования его в скрипты Python. Затем эти скрипты можно использовать для тестирования и автоматизации CI/CD в рабочей среде. 

Проект машинного обучения требует экспериментирования, в котором гипотезы проверяются на реальных наборах данных с помощью таких гибких инструментов, как Jupyter Notebook. Когда модель будет готова к рабочей среде, ее код следует поместить в репозиторий рабочего кода. В некоторых случаях для размещения в репозитории рабочего кода код модели необходимо преобразовать в сценарии Python. В этом руководстве рассматривается рекомендуемый подход к экспорту кода экспериментов в сценарии Python.

В этом руководстве описано следующее:

> [!div class="checklist"]
> * Очистка ненужного кода.
> * Рефакторинг кода Jupyter Notebook в функции.
> * Создание сценариев Python для связанных задач.
> * Создание модульных тестов.

## <a name="prerequisites"></a>Предварительные требования

- Создайте [шаблон MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) и используйте записные книжки `experimentation/Diabetes Ridge Regression Training.ipynb` и `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Эти записные книжки используются в качестве примера преобразования кода эксперимента в рабочий код. Эти записные книжки можно найти по адресу [https://github.com/microsoft/MLOpsPython/tree/master/experimentation](https://github.com/microsoft/MLOpsPython/tree/master/experimentation).
- Установить службы `nbconvert`. Выполните только инструкции по установке в разделе __Установка nbconvert__ на странице [Установка](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Удаление всего ненужного кода

Часть кода, написанного во время эксперимента, предназначена только для исследовательских целей. Поэтому первый шаг преобразования экспериментального кода в рабочий заключается в том, чтобы удалить этот уже ненужный код. Удаление ненужного кода также улучшит обслуживаемость кода. В этом разделе вы удалите код из записной книжки `experimentation/Diabetes Ridge Regression Training.ipynb`. Операторы, которые выводят на печать форму `X` и `y`, а также вызов ячейки `features.describe` предназначены только для исследования данных и могут быть удалены. После удаления ненужного кода `experimentation/Diabetes Ridge Regression Training.ipynb` должен выглядеть, как в показанном ниже коде без Markdown.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib
import pandas as pd

sample_data = load_diabetes()

df = pd.DataFrame(
    data=sample_data.data,
    columns=sample_data.feature_names)
df['Y'] = sample_data.target

X = df.drop('Y', axis=1).values
y = df['Y'].values

X_train, X_test, y_train, y_test = train_test_split(
    X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

args = {
    "alpha": 0.5
}

reg_model = Ridge(**args)
reg_model.fit(data["train"]["X"], data["train"]["y"])

preds = reg_model.predict(data["test"]["X"])
mse = mean_squared_error(preds, y_test)
metrics = {"mse": mse}
print(metrics)

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Рефакторинг кода в функции

Далее необходимо выполнить рефакторинг кода Jupyter в функции. Рефакторинг кода в функции упрощает модульное тестирование и делает код более удобным для обслуживания. В этом разделе вы выполните рефакторинг:

- записной книжки Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`);
- записной книжки Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`).

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Рефакторинг записной книжки Diabetes Ridge Regression Training в функции

В `experimentation/Diabetes Ridge Regression Training.ipynb` необходимо выполнить следующие шаги.

1. Создайте функцию `split_data`, чтобы разделить кадр данных на тестовые данные и данные для обучения. Эта функция должна принимать кадр данных `df` в качестве параметра и возвращать словарь с ключами `train` и `test`.

    Переместите код под заголовком *Split Data into Training and Validation Sets* в функцию `split_data` и измените ее, чтобы она возвращала объект `data`.

1. Создайте функцию `train_model`, которая принимает параметры `data` и `args`, а затем возвращает обученную модель.

    Переместите код под заголовком *Training Model on Training Set* в функцию `train_model` и измените ее, чтобы она возвращала объект `reg_model`. Удалите словарь `args`, значения будут формироваться из параметра `args`.

1. Создайте функцию `get_model_metrics`, которая принимает параметры `reg_model` и `data`, а затем оценивает модель и возвращает словарь метрик для обученной модели.

    Переместите код под заголовком *Validate Model on Validation Set* в функцию `get_model_metrics` и измените ее, чтобы она возвращала объект `metrics`.

Эти три функции должны выглядеть следующим образом:

```python
# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics
```

В `experimentation/Diabetes Ridge Regression Training.ipynb` все еще необходимо выполнить следующие шаги:

1. Создайте функцию `main`, которая не принимает параметры и ничего не возвращает.
1. Переместите код под заголовком Load Data в функцию `main`.
1. Добавьте вызов только что написанной функции в функцию `main`:
    ```python
    # Split Data into Training and Validation Sets
    data = split_data(df)
    ```

    ```python
    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)
    ```

    ```python
    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)
    ```
1. Переместите код под заголовком Save Model в функцию `main`.

Функция `main` должна выглядеть примерно как следующий код.

```python
def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)
```

На этом этапе в записной книжке не должно остаться кода, которого нет в функции (кроме операторов импорта в первой ячейке).

Добавьте оператор, который вызывает функцию `main`.

```python
main()
```

После рефакторинга `experimentation/Diabetes Ridge Regression Training.ipynb` должен содержать код без Markdown, как показано ниже.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

main()
```

### <a name="refactor-diabetes-ridge-regression-scoring-notebook-into-functions"></a>Рефакторинг записной книжки Diabetes Ridge Regression Scoring в функции

В `experimentation/Diabetes Ridge Regression Scoring.ipynb` необходимо выполнить следующие шаги.

1. Создайте функцию `init`, которая не принимает параметры и ничего не возвращает.
1. Скопируйте код под заголовком "Load Model" в функцию `init`.

Функция `init` должна выглядеть примерно как следующий код.

```python
def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

После создания функции `init` замените весь код под заголовком "Load Model" одним вызовом `init`, как показано ниже.

```python
init()
```

В `experimentation/Diabetes Ridge Regression Scoring.ipynb` необходимо выполнить следующие шаги.

1. Создайте функцию `run`, которая принимает `raw_data` и `request_headers` в качестве параметров и возвращает словарь результатов, как показано ниже.

    ```python
    {"result": result.tolist()}
    ```

1. Скопируйте код под заголовками "Prepare Data" и "Score Data" в функцию `run`.

    Функция `run` должна выглядеть, как приведенный ниже код. Не забудьте удалить операторы, задающие переменные `raw_data` и `request_headers`, которые будут использоваться позже при вызове функции `run`.

    ```python
    def run(raw_data, request_headers):
        data = json.loads(raw_data)["data"]
        data = numpy.array(data)
        result = model.predict(data)

        return {"result": result.tolist()}
    ```

Создав функцию `run`, замените весь код под заголовками "Prepare Data" и "Score Data" приведенным ниже кодом.

```python
raw_data = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(raw_data, request_header)
print("Test result: ", prediction)
```

Предыдущий код задает переменные `raw_data` и `request_header`, вызывает функцию `run` с параметрами `raw_data` и `request_header`, а затем выводит прогнозы.

После рефакторинга `experimentation/Diabetes Ridge Regression Scoring.ipynb` должен содержать код без Markdown, как показано ниже.

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, {})
print("Test result: ", prediction)
```

## <a name="combine-related-functions-in-python-files"></a>Объединение связанных функций в файлах Python

Связанные функции должны быть объединены в файлы Python, чтобы код было удобно многократно использовать. В этом разделе вы создадите файлы Python для следующих записных книжек:

- записной книжки Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`);
- записной книжки Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`).

### <a name="create-python-file-for-the-diabetes-ridge-regression-training-notebook"></a>Создание файла Python для записной книжки Diabetes Ridge Regression Training

Преобразуйте записную книжку в исполняемый сценарий, выполнив в командной строке приведенный оператор, который использует пакет `nbconvert` и путь к `experimentation/Diabetes Ridge Regression Training.ipynb`.

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

После преобразования записной книжки в `train.py` удалите все ненужные комментарии. Замените вызов `main()` в конце файла на условный вызов, например, как в следующем коде:

```python
if __name__ == '__main__':
    main()
```

Файл `train.py` должен выглядеть примерно как приведенный ниже код.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import pandas as pd
import joblib


# Split the dataframe into test and train data
def split_data(df):
    X = df.drop('Y', axis=1).values
    y = df['Y'].values

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}
    return data


# Train the model, return the model
def train_model(data, args):
    reg_model = Ridge(**args)
    reg_model.fit(data["train"]["X"], data["train"]["y"])
    return reg_model


# Evaluate the metrics for the model
def get_model_metrics(reg_model, data):
    preds = reg_model.predict(data["test"]["X"])
    mse = mean_squared_error(preds, data["test"]["y"])
    metrics = {"mse": mse}
    return metrics


def main():
    # Load Data
    sample_data = load_diabetes()

    df = pd.DataFrame(
        data=sample_data.data,
        columns=sample_data.feature_names)
    df['Y'] = sample_data.target

    # Split Data into Training and Validation Sets
    data = split_data(df)

    # Train Model on Training Set
    args = {
        "alpha": 0.5
    }
    reg = train_model(data, args)

    # Validate Model on Validation Set
    metrics = get_model_metrics(reg, data)

    # Save Model
    model_name = "sklearn_regression_model.pkl"

    joblib.dump(value=reg, filename=model_name)

if __name__ == '__main__':
    main()
```

`train.py` теперь можно вызвать из терминала, запустив `python train.py`.
Функцию из `train.py` можно также вызвать с других файлов.

Файл `train_aml.py` в каталоге `diabetes_regression/training` репозитория MLOpsPython вызывает функцию, определенную в `train.py` в контексте экспериментального запуска службы "Машинное обучение Azure". Функции также можно вызвать в модульных тестах, которые описаны далее в этом руководстве.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Создание файла Python для записной книжки Diabetes Ridge Regression Scoring

Преобразуйте записную книжку в исполняемый сценарий, выполнив в командной строке приведенный оператор, который использует пакет `nbconvert` и путь к `experimentation/Diabetes Ridge Regression Scoring.ipynb`.

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

После преобразования записной книжки в `score.py` удалите все ненужные комментарии. Файл `score.py` должен выглядеть примерно как приведенный ниже код.

```python
import json
import numpy
from azureml.core.model import Model
import joblib

def init():
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)

def run(raw_data, request_headers):
    data = json.loads(raw_data)["data"]
    data = numpy.array(data)
    result = model.predict(data)

    return {"result": result.tolist()}

init()
test_row = '{"data":[[1,2,3,4,5,6,7,8,9,10],[10,9,8,7,6,5,4,3,2,1]]}'
request_header = {}
prediction = run(test_row, request_header)
print("Test result: ", prediction)
```

Чтобы переменная `model` отображалась в скрипте, она должна быть глобальной. Добавьте следующий оператор в начало функции `init`.

```python
global model
```

После добавления предыдущего оператора функция `init` должна выглядеть, как приведенный ниже код.

```python
def init():
    global model

    # load the model from file into a global object
    model_path = Model.get_model_path(
        model_name="sklearn_regression_model.pkl")
    model = joblib.load(model_path)
```

## <a name="create-unit-tests-for-each-python-file"></a>Создание модульных тестов для каждого файла Python

Создайте модульные тесты для функций Python. Модульные тесты позволяют избежать снижения функциональности кода и упрощают его обслуживание. В этом разделе вы создадите модульные тесты для функций в `train.py`.

`train.py` содержит несколько функций, но в этом руководстве мы создадим только один модульный тест для функции `train_model`, используя платформу Pytest. Pytest — это единственная платформа модульного тестирования Python, но она является одной из наиболее часто используемых. Дополнительные сведения доступны на веб-сайте [Pytest](https://pytest.org).

Модульный тест обычно содержит три основных действия.

- Упорядочение объекта — создание и настройка необходимых объектов.
- Выполнение действия с объектом.
- Утверждение ожидаемого результата.

Модульный тест будет вызывать `train_model` с некоторыми жестко прописанными в коде данными и аргументами, а также проверять, что `train_model` работает должным образом, с помощью окончательной обученной модели. Это позволит сделать прогноз и сравнить его с ожидаемым значением.

```python
import numpy as np
from code.training.train import train_model


def test_train_model():
    # Arrange
    X_train = np.array([1, 2, 3, 4, 5, 6]).reshape(-1, 1)
    y_train = np.array([10, 9, 8, 8, 6, 5])
    data = {"train": {"X": X_train, "y": y_train}}

    # Act
    reg_model = train_model(data, {"alpha": 1.2})

    # Assert
    preds = reg_model.predict([[1], [2]])
    np.testing.assert_almost_equal(preds, [9.93939393939394, 9.03030303030303])
```

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы понимаете, как преобразовать эксперимент в рабочий код, воспользуйтесь приведенными ниже ссылками, чтобы получить дополнительные сведения и дальнейшие инструкции.

+ [MLOpsPython.](https://github.com/microsoft/MLOpsPython/blob/master/docs/custom_model.md) Создайте конвейер CI/CD, чтобы обучать, вычислять и развертывать собственную модель с помощью Azure Pipelines и службы "Машинное обучение Azure".
+ [Мониторинг запусков и метрик экспериментов в Машинном обучении Azure](./how-to-track-experiments.md)
+ [Мониторинг и сбор данных из конечных точек веб-службы Машинного обучения](./how-to-enable-app-insights.md)