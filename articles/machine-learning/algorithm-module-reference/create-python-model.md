---
title: 'Создание модели Python: Справочник по модулям'
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модуль создания модели Python в Машинное обучение Azure для создания пользовательского модуля моделирования или обработки данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371823"
---
# <a name="create-python-model-module"></a>Создать модуль модели Python

В этой статье описывается модуль в Машинное обучение Azure Designer (Предварительная версия).

Узнайте, как использовать модуль создания модели Python для создания обученной модели из скрипта Python. Модель можно создать на основе любого из сведений, включенного в пакет Python в среде Машинное обучение Azure конструктора. 

После создания модели можно использовать [обучение модели](train-model.md) для обучения модели на наборе данных, как и любые другие знания в машинное обучение Azure. Обученную модель можно передать в [модель оценки](score-model.md) для создания прогнозов. Затем можно сохранить обученную модель и опубликовать рабочий процесс оценки как веб-службу.

> [!WARNING]
> В настоящее время невозможно передать оцененные результаты модели Python для [оценки модели](evaluate-model.md). Если необходимо оценить модель, можно написать пользовательский скрипт Python и запустить его с помощью модуля [выполнение скрипта Python](execute-python-script.md) .  


## <a name="configure-the-module"></a>Настройка модуля

Для использования этого модуля необходимы промежуточные или квалифицированные знания о Python. Модуль поддерживает использование любых сведений, включенных в пакеты Python, уже установленные в Машинное обучение Azure. См. список предварительно установленного пакета Python в разделе [выполнение скрипта Python](execute-python-script.md).
  

В этой статье показано, как использовать **модель создания Python** с простым конвейером. Вот схема конвейера:

![Схема создания модели Python](./media/module/create-python-model.png)

1. Выберите **создать модель Python**и измените скрипт, чтобы реализовать процесс моделирования или управления данными. Модель можно создать на основе любого из сведений, включенного в пакет Python в среде Машинное обучение Azure.

   В следующем примере кода классификатора упрощенного алгоритма Байеса с двумя классами используется популярный пакет *sklearn* :

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

1. Подключите только что созданный модуль **создания модели Python** для обучения модели **модели** и **оценки**.

1. Если необходимо оценить модель, добавьте модуль [выполнение скрипта Python](execute-python-script.md) и измените скрипт Python.

   Следующий скрипт является образцом кода вычисления:

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

## <a name="next-steps"></a>Дальнейшие действия

См. [набор модулей, доступных](module-reference.md) для машинное обучение Azure. 