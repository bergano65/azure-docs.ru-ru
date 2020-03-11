---
title: Преобразование кода эксперимента машинного обучения в рабочий код
titleSuffix: Azure Machine Learning
description: Узнайте, как преобразовать экспериментальный код машинного обучения в рабочий код с помощью шаблона кода MLOpsPython.
author: bjcmit
ms.author: brysmith
ms.service: machine-learning
ms.topic: tutorial
ms.date: 02/10/2020
ms.openlocfilehash: 5a7c4ce6d5868efef4cfb4fbe2183ec8337ff5b6
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78301851"
---
# <a name="tutorial-convert-ml-experimental-code-to-production-code"></a>Руководство по Преобразование экспериментального кода машинного обучения в рабочий код

Проект машинного обучения требует экспериментирования, в котором гипотезы проверяются на реальных наборах данных с помощью таких гибких инструментов, как Jupyter Notebook. Когда модель будет готова к рабочей среде, ее код следует поместить в репозиторий рабочего кода. В некоторых случаях для размещения в репозитории рабочего кода код модели необходимо преобразовать в сценарии Python. В этом руководстве рассматривается рекомендуемый подход к экспорту кода экспериментов в сценарии Python.

В этом руководстве описано следующее:

> [!div class="checklist"]
>
> * Очистка ненужного кода.
> * Рефакторинг кода Jupyter Notebook в функции.
> * Создание сценариев Python для связанных задач.
> * Создание модульных тестов.

## <a name="prerequisites"></a>Предварительные требования

- Создайте [шаблон MLOpsPython](https://github.com/microsoft/MLOpsPython/generate) и используйте записные книжки `experimentation/Diabetes Ridge Regression Training.ipynb` и `experimentation/Diabetes Ridge Regression Scoring.ipynb`. Эти записные книжки используются в качестве примера преобразования кода эксперимента в рабочий код.
- Установите nbconvert. Выполните только инструкции по установке в разделе __Установка nbconvert__ на странице [Установка](https://nbconvert.readthedocs.io/en/latest/install.html).

## <a name="remove-all-nonessential-code"></a>Удаление всего ненужного кода

Часть кода, написанного во время эксперимента, предназначена только для исследовательских целей. Поэтому первый шаг преобразования экспериментального кода в рабочий заключается в том, чтобы удалить этот уже ненужный код. Удаление ненужного кода также улучшит обслуживаемость кода. В этом разделе вы удалите код из записной книжки `experimentation/Diabetes Ridge Regression Training.ipynb`. Операторы, которые выводят на печать форму `X` и `y`, а также вызов ячейки `features.describe` предназначены только для исследования данных и могут быть удалены. После удаления ненужного кода `experimentation/Diabetes Ridge Regression Training.ipynb` должен выглядеть, как в показанном ниже коде без Markdown.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib

X, y = load_diabetes(return_X_y=True)

X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
data = {"train": {"X": X_train, "y": y_train},
        "test": {"X": X_test, "y": y_test}}

alpha = 0.5

reg = Ridge(alpha=alpha)
reg.fit(data["train"]["X"], data["train"]["y"])

preds = reg.predict(data["test"]["X"])
print("mse", mean_squared_error(preds, data["test"]["y"]))

model_name = "sklearn_regression_model.pkl"
joblib.dump(value=reg, filename=model_name)
```

## <a name="refactor-code-into-functions"></a>Рефакторинг кода в функции

Далее необходимо выполнить рефакторинг кода Jupyter в функции. Рефакторинг кода в функции упрощает модульное тестирование и делает код более удобным для обслуживания. В этом разделе вы выполните рефакторинг:

- записной книжки Diabetes Ridge Regression Training (`experimentation/Diabetes Ridge Regression Training.ipynb`);
- записной книжки Diabetes Ridge Regression Scoring (`experimentation/Diabetes Ridge Regression Scoring.ipynb`).

### <a name="refactor-diabetes-ridge-regression-training-notebook-into-functions"></a>Рефакторинг записной книжки Diabetes Ridge Regression Training в функции

В `experimentation/Diabetes Ridge Regression Training.ipynb` необходимо выполнить следующие шаги.

1. Создайте функцию `train_model`, которая принимает параметры `data` и `alpha` и возвращает модель.
1. Скопируйте код под заголовками "Train Model on Training Set" и "Validate Model on Validation Set" в функцию `train_model`.

Функция `train_model` должна выглядеть примерно как следующий код.

```python
def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg
```

Создав функцию `train_model`, замените код под заголовками "Train Model on Training Set" и "Validate Model on Validation Set" приведенным ниже оператором.

```python
reg = train_model(data, alpha)
```

Предыдущий оператор вызывает функцию `train_model`, которая передает параметры `data` и `alpha` и возвращает модель.

В `experimentation/Diabetes Ridge Regression Training.ipynb` необходимо выполнить следующие шаги.

1. Создайте функцию `main`, которая не принимает параметры и ничего не возвращает.
1. Скопируйте код под заголовками "Load Data", "Split Data into Training and Validation Sets" и "Save Mode" в функцию `main`.
1. Скопируйте созданный вызов `train_model` в функцию `main`.

Функция `main` должна выглядеть примерно как следующий код.

```python
def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)
```

Создав функцию `main`, замените весь код под заголовками "Load Data", "Split Data into Training and Validation Sets" и "Save Mode" в `train_model` приведенным ниже оператором.

```python
main()
```

После рефакторинга `experimentation/Diabetes Ridge Regression Training.ipynb` должен содержать код без Markdown, как показано ниже.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():

    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

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

Преобразуйте записную книжку в исполняемый сценарий, выполнив в командной строке приведенный оператор, который использует пакет nbconvert и путь к `experimentation/Diabetes Ridge Regression Training.ipynb`.

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Training.ipynb" –output train
```

После преобразования записной книжки в `train.py` удалите все комментарии. Файл `train.py` должен выглядеть примерно как приведенный ниже код.

```python
from sklearn.datasets import load_diabetes
from sklearn.linear_model import Ridge
from sklearn.metrics import mean_squared_error
from sklearn.model_selection import train_test_split
import joblib


def train_model(data, alpha):
    reg = Ridge(alpha=alpha)
    reg.fit(data["train"]["X"], data["train"]["y"])
    preds = reg.predict(data["test"]["X"])
    print("mse", mean_squared_error(
        preds, data["test"]["y"]))
    return reg

def main():
    model_name = "sklearn_regression_model.pkl"
    alpha = 0.5

    X, y = load_diabetes(return_X_y=True)

    X_train, X_test, y_train, y_test = train_test_split(
        X, y, test_size=0.2, random_state=0)
    data = {"train": {"X": X_train, "y": y_train},
            "test": {"X": X_test, "y": y_test}}

    reg = train_model(data, alpha)

    joblib.dump(value=reg, filename=model_name)

main()
```

Файл `train.py`, находящийся в каталоге `diabetes_regression/training` репозитория MLOpsPython, поддерживает аргументы командной строки `build_id`, `model_name` и `alpha`. Можно добавить поддержку аргументов командной строки в файл `train.py`, чтобы использовать динамические имена моделей и значения `alpha`, но это необязательно для успешного выполнения кода.

### <a name="create-python-file-for-the-diabetes-ridge-regression-scoring-notebook"></a>Создание файла Python для записной книжки Diabetes Ridge Regression Scoring

Преобразуйте записную книжку в исполняемый сценарий, выполнив в командной строке приведенный оператор, который использует пакет nbconvert и путь к `experimentation/Diabetes Ridge Regression Scoring.ipynb`.

```
jupyter nbconvert -- to script "Diabetes Ridge Regression Scoring.ipynb" –output score
```

После преобразования записной книжки в `score.py` удалите все комментарии. Файл `score.py` должен выглядеть примерно как приведенный ниже код.

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

Функцию `train_model` требуется изменить, чтобы создать экземпляр модели глобальных переменных. Тогда она будет видна во всем сценарии. Добавьте следующий оператор в начало функции `init`.

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

Теперь необходимо создать модульные тесты для каждого файла Python, что сделает код более надежным и удобным в обслуживании. В этом разделе вы создадите модульный тест для одной из функций в `train.py`.

Файл `train.py` содержит две функции, `train_model` и `main`. Для каждой функции требуется модульный тест, но в этом руководстве мы создадим только один модульный тест для функции `train_model`, используя платформу Pytest. Pytest — это единственная платформа модульного тестирования Python, но она является одной из наиболее часто используемых. Дополнительные сведения доступны на веб-сайте [Pytest](https://pytest.org).

Модульный тест обычно содержит три основных действия.

- Упорядочение объекта — создание и настройка необходимых объектов.
- Выполнение действия с объектом.
- Утверждение ожидаемого результата.

Распространенным условием для `train_model` является передача значений `data` и `alpha`. Ожидаемым результатом является вызов функций `Ridge.train` и `Ridge.predict`. Так как методы обучения в машинном обучении зачастую выполняются медленно, вызов `Ridge.train` будет имитироваться. Так как возвращаемое значение `Ridge.train` является имитируемым объектом, мы также сымитируем `Ridge.predict`. Модульный тест для тестирования `train_model` и получения ожидаемых значений `data` и `alpha` функций `Ridge.train` и `Ridge.predict`, вызываемых с помощью имитации и платформы Pytest, должен выглядеть, как приведенный ниже код.

```python
import pytest
from code.training.train import train_model

class TestTrain:

    @staticmethod
    def test_train_model(mocker):
        # Arrange
        test_data = {"train": {"X": [[1, 2, 3]], "y": [0]},
                     "test": {"X": [[4, 5, 6]], "y": [0]}}
        test_alpha = 0.5
        mock_ridge_fit = mocker.patch('Ridge.fit')
        mock_ridge_predict = mocker.patch('Ridge.predict')

        # Act
        train_model(test_data, test_alpha)

        # Assert
        mock_ridge_fit.assert_called()
        mock_ridge_predict.assert_called()
```

## <a name="use-your-own-model-with-mlopspython-code-template"></a>Использование собственной модели с шаблоном кода MLOpsPython

Если вы выполнили действия, описанные в этом руководстве, то у вас имеется набор сценариев, соответствующих сценариям обучения, оценки и тестирования, доступным в репозитории MLOpsPython.  В соответствии со структурой, описанной выше, выполните следующие действия, чтобы использовать эти файлы в собственном проекте машинного обучения.

1. Выполните указания из руководства [по началу работы](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) с MLOpsPython.
2. Чтобы создать начальную точку проекта, следуйте инструкциям [по начальной загрузке](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) MLOpsPython.
3. Замена кода обучения.
4. Замена кода оценки.
5. Изменение кода проверки.

### <a name="follow-the-getting-started-guide"></a>Выполнение указаний из руководства по началу работы
Выполнение указаний из руководства [по началу работы](https://github.com/microsoft/MLOpsPython/blob/master/docs/getting_started.md) необходимо, чтобы обеспечить инфраструктуру и конвейеры для выполнения MLOpsPython.

### <a name="follow-the-bootstrap-instructions"></a>Инструкции по начальной загрузке

С руководством по [начальной загрузке из репозитория MLOpsPython](https://github.com/microsoft/MLOpsPython/blob/master/bootstrap/README.md) вы сможете быстро подготовить репозиторий для проекта.

**Примечание.** Так как сценарий начальной загрузки переименует папку diabetes_regression в выбранное вами имя проекта, мы будем называть проект `[project name]` при использовании путей.

### <a name="replace-training-code"></a>Замена кода обучения

Замена кода, используемого для обучения модели, и удаление или замена соответствующих модульных тестов необходимы для того, чтобы решение функционировало с вашим кодом. Особенно важно выполнить следующие действия.

1. Замените `[project name]/training/train.py`. Этот сценарий обучает модель локально или в вычислительной среде Машинного обучения Azure.
1. Удалите или замените модульные тесты обучения в `[project name]/training/test_train.py`.

### <a name="replace-score-code"></a>Замена кода оценки

Чтобы модель поддерживала вывод в реальном времени, необходимо заменить код оценки. Шаблон MLOpsPython использует код оценки, чтобы развернуть модель для оценки в реальном времени для ACI, AKS или веб-приложений. Если вы хотите оставить оценку, замените `[project name]/scoring/score.py`.

### <a name="update-evaluation-code"></a>Изменение кода проверки

Шаблон MLOpsPython использует сценарий evaluate_model для сравнения эффективности новой обученной модели и текущей рабочей модели на основе среднеквадратической погрешности. Если эффективность новой обученной модели выше, чем текущей рабочей модели, то конвейеры продолжают работу. В противном случае конвейеры отменяются. Чтобы оставить оценку, замените все экземпляры `mse` в `[project name]/evaluate/evaluate_model.py` требуемой метрикой.

Чтобы избавиться от оценки, задайте для переменной конвейера DevOps `RUN_EVALUATION` в `.pipelines/[project name]-variables-template.yml` значение `false`.

## <a name="next-steps"></a>Дальнейшие действия

Теперь, когда вы понимаете, как преобразовать эксперимент в рабочий код, воспользуйтесь приведенными ниже ссылками, чтобы узнать, как отслеживать выполнение экспериментов и модели, развернутые в виде веб-служб.

> [!div class="nextstepaction"]
> [Мониторинг запусков и метрик экспериментов в Машинном обучении Azure](https://docs.microsoft.com/azure/machine-learning/how-to-track-experiments)
> [Мониторинг и сбор данных из конечных точек веб-службы Машинного обучения](https://docs.microsoft.com/azure/machine-learning/how-to-enable-app-insights)
