---
title: 'Создание модели Python: Ссылка на модуль'
titleSuffix: Azure Machine Learning
description: Узнайте, как использовать модуль Модели создания Python в Azure Machine Learning для создания пользовательского модуля моделирования или обработки данных.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
author: likebupt
ms.author: keli19
ms.date: 11/19/2019
ms.openlocfilehash: 929938bba9c9512ecfd663a540cf4a7ebbf68e2b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371823"
---
# <a name="create-python-model-module"></a>Создание модуля модели Python

В этой статье описывается модуль в дизайнере машинного обучения Azure (предварительный просмотр).

Узнайте, как использовать модуль Модели создания Python для создания неподготовленной модели из сценария Python. Вы можете основывать модель на любом ученике, включенном в пакет Python в среде конструктора Посвящающихся машинным обучением Azure Machine Learning. 

После создания модели можно использовать [модель Train Model](train-model.md) для обучения модели на наборе данных, как и любой другой учащийся в Azure Machine Learning. Обученная модель может быть передана в [модель оценки](score-model.md) для прогнозирования. Затем можно сохранить обученную модель и опубликовать рабочий процесс скоринга в виде веб-сервиса.

> [!WARNING]
> В настоящее время невозможно передать забитые результаты модели Python для [оценки модели.](evaluate-model.md) Если вам нужно оценить модель, вы можете написать пользовательский скрипт Python и запустить его с помощью модуля [Execute Python Script.](execute-python-script.md)  


## <a name="configure-the-module"></a>Настройка модуля

Использование этого модуля требует промежуточных или экспертных знаний Python. Модуль поддерживает использование любого учащегося, включенного в пакеты Python, уже установленные в Azure Machine Learning. Смотрите список предустановленного пакета Python в [сценарии «Выполнить Python».](execute-python-script.md)
  

В этой статье показано, как использовать **модель Создания Python** с помощью простого конвейера. Вот схема конвейера:

![Диаграмма создания модели Python](./media/module/create-python-model.png)

1. Выберите **«Создайте модель Python»** и отспособьте сценарий для реализации процесса моделирования или управления данными. Модель можно основывать на любом ученике, включенном в пакет Python в среде машинного обучения Azure.

   В следующем примере классификатора двухклассных Байв Байес использует популярный пакет *sklearn:*

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

1. Подключите модуль **Модели создания Python,** который вы только что создали для **обучения модели** и модели **оценки.**

1. Если вам нужно оценить модель, добавьте модуль [Execute Python Script](execute-python-script.md) и отспособите сценарий Python.

   Следующим скриптом является пример оценки кода:

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

Ознакомьтесь с [набором модулей, доступных](module-reference.md) для машинного обучения Azure. 