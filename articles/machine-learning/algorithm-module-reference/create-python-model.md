---
title: 'Создание модели Python: Ссылка на модуль'
titleSuffix: Azure Machine Learning service
description: Узнайте, как использовать модель Создание модели Python в службе машинного обучения Azure для создания пользовательского модуля моделирования или обработки данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: xiaoharper
ms.author: zhanxia
ms.date: 05/06/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ea9b50cede3e2d264ca0476b6a987ca6896c3c79
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65029750"
---
# <a name="create-python-model"></a>Создание модели Python

В этой статье описывается использование **Создание модели Python** модуль создания необученную модель с помощью скрипта Python. 

Модель может быть основан на любой ученик, включенный в пакет Python в среде машинного обучения Azure. 

После создания модели, можно использовать [Обучение модели](train-model.md) для обучения модели на наборе данных, как и любой другой ученик в машинном обучении Azure. Обученную модель можно передать [Score Model](score-model.md) для использования модели для создания прогнозов. Затем можно сохранить обученную модель и рабочего процесса оценки могут быть опубликованы как веб-службы.

> [!WARNING]
> В настоящее время не можно передать результаты оценки модели Python, чтобы [Evaluate Model](evaluate-model.md). Если вам нужно оценить модель, можно написать пользовательский Python скрипт и запустить его, используя [Execute Python Script](execute-python-script.md) модуля.  


## <a name="how-to-configure-create-python-model"></a>Как настроить создание модели Python

Использование этого модуля требуется промежуточная или экспертов знание Python. Модуль поддерживает использование любого ученика, включенного в пакеты Python, уже установлен в машинном обучении Azure. См. в разделе списка предустановленных пакетов Python в [Execute Python Script](execute-python-script.md).
  

В этой статье будет показано, как использовать **Создание модели Python** с простого эксперимента. Ниже приведен граф эксперимента.

![CREATE-python-model](./media/module/aml-create-python-model.png)

1.  Нажмите кнопку **Создание модели Python**, измените сценарий для реализации вашей моделирования или обработки данных управления. Модель может быть основан на любой ученик, включенный в пакет Python в среде машинного обучения Azure.


    Ниже приведен пример кода два двухклассовых классификатора Байеса с помощью популярного *sklearn* пакета.

```Python

# The script MUST define a class named AzureMLModel.
# This class MUST at least define the following three methods:
    # __init__: in which self.model must be assigned,
    # train: which trains self.model, the two input arguments must be pandas DataFrame,
    # predict: which generates prediction result, the input argument and the prediction result MUST be pandas DataFrame.
# The signatures (method names and argument names) of all these methods MUST be exactly the same as the following example.


import pandas as pd
from sklearn.naive_bayes import GaussianNB


class AzureMLModel:
    def __init__(self):
        self.model = GaussianNB()
        self.feature_column_names = list()

    def train(self, df_train, df_label):
        self.feature_column_names = df_train.columns.tolist()
        self.model.fit(df_train, df_label)

    def predict(self, df):
        return pd.DataFrame(
            {'Scored Labels': self.model.predict(df[self.feature_column_names]), 
             'probabilities': self.model.predict_proba(df[self.feature_column_names])[:, 1]}
        )


```


2. Подключение **Создание модели Python** модуля, вы только что создали для **Обучение модели** и **оценка модели**

3. Если необходимо оценить модель, добавьте [Execute Python Script](execute-python-script.md) и измените скрипт Python для реализации вычисления.

Ниже приведен пример оценки кода.

```Python


# The script MUST contain a function named azureml_main
# which is the entry point for this module.

# imports up here can be used to 
import pandas as pd

# The entry point function can contain up to two input arguments:
#   Param<dataframe1>: a pandas.DataFrame
#   Param<dataframe2>: a pandas.DataFrame
def azureml_main(dataframe1 = None, dataframe2 = None):
    
    from sklearn.metrics import accuracy_score, precision_score, recall_score, roc_auc_score, roc_curve
    import pandas as pd
    import numpy as np
    
    scores = dataframe1.ix[:, ("income", "Scored Labels", "probabilities")]
    ytrue = np.array([0 if val == '<=50K' else 1 for val in scores["income"]])
    ypred = np.array([0 if val == '<=50K' else 1 for val in scores["Scored Labels"]])    
    probabilities = scores["probabilities"]
    
    accuracy, precision, recall, auc = \
    accuracy_score(ytrue, ypred),\
    precision_score(ytrue, ypred),\
    recall_score(ytrue, ypred),\
    roc_auc_score(ytrue, probabilities)
    
    metrics = pd.DataFrame();
    metrics["Metric"] = ["Accuracy", "Precision", "Recall", "AUC"];
    metrics["Value"] = [accuracy, precision, recall, auc]
    
    return metrics,

```
