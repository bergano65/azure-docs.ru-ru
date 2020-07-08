---
title: Как сохранить конфиденциальность данных с помощью пакетов WhiteNoise
titleSuffix: Azure Machine Learning
description: Сведения о том, как применить рекомендации по дифференциальной конфиденциальности для моделей Машинного обучения Azure с помощью пакетов WhiteNoise.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: how-to
ms.author: slbird
author: slbird
ms.reviewer: luquinta
ms.date: 05/17/2020
ms.openlocfilehash: b43fd25e77e07b7b64416d8784d47c14fed48ea7
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84981852"
---
# <a name="use-differential-privacy-in-azure-machine-learning"></a>Использование дифференциальной конфиденциальности в Машинном обучении Azure

[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

Сведения о том, как применить рекомендации по дифференциальной конфиденциальности для моделей Машинного обучения Azure с помощью пакетов Python WhiteNoise.

Дифференциальная конфиденциальность является образцовым определением конфиденциальности. Системы, которые соответствуют этому определению конфиденциальности, предоставляют надежную защиту от широкого спектра атак с целью реконструкции и переидентификации данных, в том числе атак злоумышленников, располагающих дополнительными сведениями. См. дополнительные сведения о [работе дифференциальной конфиденциальности](./concept-differential-privacy.md).

> [!NOTE]
> Обратите внимание, что мы переименованы набор средств, и в ближайшие недели будет введено новое имя. 

## <a name="prerequisites"></a>Предварительные требования

- Если у вас еще нет подписки Azure, создайте бесплатную учетную запись, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Машинного обучения Azure](https://aka.ms/AMLFree) уже сегодня.
- [Python 3](https://www.python.org/downloads/);

## <a name="install-whitenoise-packages"></a>Установка пакетов WhiteNoise

### <a name="standalone-installation"></a>Автономная установка

Эти библиотеки предназначены для работы из распределенных кластеров Spark и могут устанавливаться так же, как и любые другие пакеты.

В приведенных ниже инструкциях предполагается, что команды `python` и `pip` сопоставлены с `python3` и `pip3`.

Используйте pip, чтобы установить [пакеты Python WhiteNoise](https://pypi.org/project/opendp-whitenoise/).

`pip install opendp-whitenoise`

Чтобы убедиться, что пакеты установлены, откройте командную строку Python и введите следующее.

```python
import opendp.whitenoise.core
import opendp.whitenoise.sql
```

Если импорт выполнен успешно, то библиотеки установлены и готовы к использованию.

### <a name="docker-image"></a>Образ Docker

Вы также можете использовать пакеты WhiteNoise с Docker.

Извлеките образ `opendp/whitenoise` для использования библиотек в контейнере Docker, который включает Spark, Jupyter и пример кода.

```sh
docker pull opendp/whitenoise:privacy
```

После извлечения образа запустите сервер Jupyter.

```sh
docker run --rm -p 8989:8989 --name whitenoise-run opendp/whitenoise:privacy
```

Это запустит сервер Jupyter на порте `8989` вашего `localhost` с паролем `pass@word99`. Если вы использовали приведенную выше командную строку для запуска контейнера с именем `whitenoise-privacy`, можете открыть терминал bash на сервере Jupyter, выполнив следующую команду.

```sh
docker exec -it whitenoise-run bash
```

Этот экземпляр Docker очищает все состояния при завершении работы, поэтому вы потеряете все записные книжки, созданные в запущенном экземпляре. Чтобы устранить эту проблему, можно привязать локальную папку к контейнеру при запуске.

```sh
docker run --rm -p 8989:8989 --name whitenoise-run --mount type=bind,source=/Users/your_name/my-notebooks,target=/home/privacy/my-notebooks opendp/whitenoise:privacy
```

Все записные книжки, созданные в папке *my-notebooks*, будут храниться в локальной файловой системе.

## <a name="perform-data-analysis"></a>Выполнение анализа данных

Чтобы подготовить выпуск с дифференциальной конфиденциальностью, необходимо выбрать источник данных, статистический показатель и некоторые параметры конфиденциальности, указывающие уровень защиты конфиденциальности.  

Этот образец ссылается на микроданные для общественного использования (PUMS) в Калифорнии, представляющие анонимные записи о демографических характеристиках граждан.

```python
import os
import sys
import numpy as np
import opendp.whitenoise.core as wn

data_path = os.path.join('.', 'data', 'PUMS_california_demographics_1000', 'data.csv')
var_names = ["age", "sex", "educ", "race", "income", "married", "pid"]
```

В этом примере мы вычисляем среднее значение и дисперсию возраста.  Мы используем общий `epsilon`, равный 1,0 (epsilon — это параметр конфиденциальности, распределяющий наш бюджет конфиденциальности по двум величинам, которые мы хотим вычислить). Дополнительные сведения о [метриках конфиденциальности](concept-differential-privacy.md#differential-privacy-metrics).

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
    # get variance of age
    age_var = wn.dp_variance(data = wn.cast(data['age'], type="FLOAT"),
                             privacy_usage = {'epsilon': .35},
                             data_lower = 0.,
                             data_upper = 100.,
                             data_n = 1000
                            )
analysis.release()

print("DP mean of age: {0}".format(age_mean.value))
print("DP variance of age: {0}".format(age_var.value))
print("Privacy usage: {0}".format(analysis.privacy_usage))
```

Результаты выглядят примерно так.

```text
DP mean of age: 44.55598845931517
DP variance of age: 231.79044646429134
Privacy usage: approximate {
  epsilon: 1.0
}
```

В этом примере следует обратить внимание на некоторые важные моменты.  Во-первых, объект `Analysis` представляет граф обработки данных.  В этом примере среднее значение и дисперсия вычисляются из одного и того же исходного узла.  Однако можно включить более сложные выражения, объединяющие входы с выходами произвольным образом.

Граф анализа включает метаданные `data_upper` и `data_lower`, указывающие нижнюю и верхнюю границы для возраста.  Эти значения используются для точной калибровки шума, чтобы обеспечить дифференциальную конфиденциальность.  Эти значения также используются в некоторых способах обработки выбросов или отсутствующих значений.

Наконец, граф анализа отслеживает общий потраченный бюджет конфиденциальности.

Библиотеку можно использовать для создания более сложных графов анализа, используя несколько механизмов, видов статистики и служебных функций.

| Статистика    | Механизмы | Техническое оборудование  |
| ------------- |------------|------------|
| Count         | Гауссовский   | Приведение       |
| Гистограмма     | Геометрический  | Прикрепление   |
| Среднее значение          | Лапласовский    | Оцифровывание   |
| Квантили     |            | Filter     |
| SUM           |            | Добавления отсутствующих данных |
| Вариативность/ковариация |      | Преобразование  |

Дополнительные сведения см. в [записной книжке базового анализа данных](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/basic_data_analysis.ipynb).

## <a name="approximate-utility-of-differentially-private-releases"></a>Приблизительная полезность выпусков с дифференциальной конфиденциальностью

Так как дифференциальная конфиденциальность работает путем калибровки шума, полезность выпусков может зависеть от риска конфиденциальности.  Как правило, шум, необходимый для защиты каждого отдельного пользователя, становится незначительным по мере увеличения размера образцов, однако кардинальным образом меняет результат для выпусков, ориентированных на отдельного пользователя.  Аналитики могут изучить сведения о точности для выпуска, чтобы определить, насколько он полезен.

```python
with wn.Analysis() as analysis:
    # load data
    data = wn.Dataset(path = data_path, column_names = var_names)

    # get mean of age
    age_mean = wn.dp_mean(data = wn.cast(data['age'], type="FLOAT"),
                          privacy_usage = {'epsilon': .65},
                          data_lower = 0.,
                          data_upper = 100.,
                          data_n = 1000
                         )
analysis.release()

print("Age accuracy is: {0}".format(age_mean.get_accuracy(0.05)))
```

Результат этой операции должен выглядеть примерно так.

```text
Age accuracy is: 0.2995732273553991
```

Этот пример вычисляет среднее значение, приведенное выше, и использует функцию `get_accuracy` для запроса точности `alpha` в размере 0,05. Значение `alpha` в 0,05 представляет интервал 95 %, в котором выпускаемое значение будет находиться в пределах указанной точности примерно 95 % всего времени.  В этом примере указанная точность равна 0,3, что означает, что выпускаемое значение будет находиться в пределах интервала, равного 0,6, примерно 95 % всего времени.  Это значение не следует считать пределом погрешностей, так как выпускаемое значение выходит за пределы указанного диапазона точности, задаваемого `alpha`, а значения за пределами диапазона могут выходить за него в любом направлении.

Аналитики могут запрашивать `get_accuracy` для различных значений `alpha`, чтобы получить более узкие или широкие доверительные интервалы, без дополнительных затрат на конфиденциальность.

## <a name="generate-a-histogram"></a>Создание гистограммы

Встроенная функция `dp_histogram` создает гистограммы дифференциальной конфиденциальности для любого из следующих типов данных.

- Непрерывная переменная, в которой набор чисел должен быть разделен на ячейки.
- Логическая или дихотомическая переменная, которая может принимать только два значения.
- Категориальная переменная, в которой есть отдельные категории, перечисленные в виде строк.

Ниже приведен пример `Analysis` с указанием ячеек для гистограммы непрерывной переменной.

```python
income_edges = list(range(0, 100000, 10000))

with wn.Analysis() as analysis:
    data = wn.Dataset(path = data_path, column_names = var_names)

    income_histogram = wn.dp_histogram(
            wn.cast(data['income'], type='int', lower=0, upper=100),
            edges = income_edges,
            upper = 1000,
            null_value = 150,
            privacy_usage = {'epsilon': 0.5}
        )
```

Так как отдельные пользователи дизъюнктивно разнесены по ячейкам гистограммы, расходы на конфиденциальность начисляются только один раз для каждой гистограммы, даже если гистограмма содержит много ячеек.

Дополнительные сведения о гистограммах см. в [записной книжке о гистограммах](https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/histograms.ipynb).

## <a name="generate-a-covariance-matrix"></a>Создание матрицы ковариаций

WhiteNoise предлагает три различных варианта использования для функции `dp_covariance`.

- Ковариация между двумя векторами
- Матрица ковариаций
- Матрица кросс-ковариации пары матриц

Ниже приведен пример вычисления скалярной ковариации.

```python
with wn.Analysis() as analysis:
    wn_data = wn.Dataset(path = data_path, column_names = var_names)

    age_income_cov_scalar = wn.dp_covariance(
      left = wn.cast(wn_data['age'], 
      type = "FLOAT"), 
      right = wn.cast(wn_data['income'], 
      type = "FLOAT"), 
      privacy_usage = {'epsilon': 1.0},
      left_lower = 0., 
      left_upper = 100., 
      left_n = 1000, 
      right_lower = 0., 
      right_upper = 500_000.,
      right_n = 1000)
```

Дополнительные сведения см. в [записной книжке ковариации](
https://github.com/opendifferentialprivacy/whitenoise-samples/blob/master/analysis/covariance.ipynb).

## <a name="next-steps"></a>Next Steps

- Изучите [примеры записных книжек WhiteNoise](https://github.com/opendifferentialprivacy/whitenoise-samples/tree/master/analysis).