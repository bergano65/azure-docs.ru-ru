---
title: Проверенные среды
titleSuffix: Azure Machine Learning
description: Коллекция проверенных сред, доступных в Машинное обучение Azure
services: machine-learning
author: luisquintanilla
ms.author: luquinta
ms.reviewer: luquinta
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.date: 09/03/2020
ms.openlocfilehash: 4ae96976f81aab9a0949594551c82d3a3fec4f0a
ms.sourcegitcommit: f8d2ae6f91be1ab0bc91ee45c379811905185d07
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89662077"
---
# <a name="azure-machine-learning-curated-environments"></a>Машинное обучение Azure проверенные среды

В этой статье перечислены проверенные среды в Машинное обучение Azure, а также пакеты и каналы, предварительно установленные в них. Проверенные среды предоставляются Машинное обучение Azure и доступны в рабочей области по умолчанию. Они поддерживаются кэшированными образами DOCKER, что сокращает затраты на подготовку к запуску и позволяет ускорить развертывание. Используйте эти среды для быстрого начала работы с различными платформами машинного обучения.

> [!NOTE]
> Этот список обновляется по состоянию на сентябрь 2020. Используйте пакет SDK для Python, чтобы получить самый обновленный список. Дополнительные сведения см. в [статье о средах](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azureml-automl"></a>AzureML-Аутомл

**Каналы пакетов:**

* Anaconda
* conda — подделка
* pytorch

**Пакеты Conda:**

* python
* numpy
* scikit-learn
* pandas
* Копировать — xgboost
* фбпрофет
* государственные
* setuptools-Git
* псутил

**Пакеты PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml — значения по умолчанию
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* вывод схемы
* Копировать — кпуинфо

## <a name="azureml-automl-dnn"></a>AzureML-Аутомл-DNN

**Каналы пакетов:**

* Anaconda
* conda — подделка
* pytorch

**Пакеты Conda:**

* python
* numpy
* scikit-learn
* pandas
* Копировать — xgboost
* фбпрофет
* государственные
* setuptools-Git
* pytorch
* кудатулкит
* псутил

**Пакеты PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml — значения по умолчанию
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* вывод схемы
* pytorch-преобразователи
* спаци
* en_core_web_sm
* Копировать — кпуинфо

## <a name="azureml-automl-dnn-gpu"></a>AzureML-Аутомл-DNN-GPU

**Каналы пакетов:**

* Anaconda
* conda — подделка
* pytorch

**Пакеты Conda:**

* python
* numpy
* scikit-learn
* pandas
* фбпрофет
* государственные
* setuptools-Git
* pytorch
* кудатулкит
* псутил

**Пакеты PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml — значения по умолчанию
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* вывод схемы
* хоровод
* pytorch-преобразователи
* спаци
* en_core_web_sm
* Копировать — кпуинфо

## <a name="azureml-automl-dnn-vision-gpu"></a>AzureML-Аутомл-DNN-концепция-GPU

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml-DataSet-Runtime
* azureml-contrib-dataset
* azureml-telemetry
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml — значения по умолчанию
* azureml-interpret
* azureml-explain-model
* azureml-train-automl-runtime
* azureml-train-automl
* azureml-от участников сообщества-аутомл-DNN-концепция

## <a name="azureml-automl-gpu"></a>AzureML-Аутомл-GPU

**Каналы пакетов:**

* Anaconda
* conda — подделка
* pytorch

**Пакеты Conda:**

* python
* numpy
* scikit-learn
* pandas
* фбпрофет
* государственные
* setuptools-Git
* псутил

**Пакеты PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml — значения по умолчанию
* azureml-interpret
* azureml-explain-model
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* вывод схемы
* Копировать — кпуинфо

## <a name="azureml-chainer-510-cpu"></a>AzureML-chaining-5.1.0-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* chainer
* mpi4py

## <a name="azureml-chainer-510-gpu"></a>AzureML-Chain-5.1.0-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* chainer
* cupy — cuda90
* mpi4py

## <a name="azureml-dask-cpu"></a>AzureML-Даск-CPU

**Каналы пакетов:**

* conda — подделка
* pytorch
* Значения по умолчанию

**Пакеты Conda:**

* python

**Пакеты PIP:**

* адлфс
* azureml-core
* azureml-DataSet-Runtime
* Даск [полный]
* Даск-ml [полный]
* распределенные
* фастпаркует
* фсспек
* жоблиб
* jupyterlab
* lz4
* mpi4py
* записная книжка
* pyarrow

## <a name="azureml-dask-gpu"></a>AzureML-Даск-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python
* matplotlib

**Пакеты PIP:**

* azureml — значения по умолчанию
* адлфс
* azureml-core
* Даск [полный]
* Даск-ml [полный]
* распределенные
* фастпаркует
* фсспек
* жоблиб
* jupyterlab
* lz4
* mpi4py
* записная книжка
* pyarrow

## <a name="azureml-hyperdrive-forecastdnn"></a>AzureML-Форекастднн

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml-pipeline-core
* azureml-telemetry
* azureml — значения по умолчанию
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-от участников сообщества-аутомл-DNN — прогнозирование

## <a name="azureml-minimal"></a>AzureML — минимальный

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию

## <a name="azureml-pyspark-mmlspark-015"></a>AzureML-PySpark-MmlSpark-0,15

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core

## <a name="azureml-pytorch-10-cpu"></a>AzureML-PyTorch-1,0-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод

## <a name="azureml-pytorch-10-gpu"></a>AzureML-PyTorch-1,0-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод

## <a name="azureml-pytorch-11-cpu"></a>AzureML-PyTorch-1,1-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-11-gpu"></a>AzureML-PyTorch-1,1-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-12-cpu"></a>AzureML-PyTorch-1,2-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-12-gpu"></a>AzureML-PyTorch-1,2-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-13-cpu"></a>AzureML-PyTorch-1,3-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-13-gpu"></a>AzureML-PyTorch-1,3-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-14-cpu"></a>AzureML-PyTorch-1,4-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-14-gpu"></a>AzureML-PyTorch-1,4-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-15-cpu"></a>AzureML-PyTorch-1,5-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-15-gpu"></a>AzureML-PyTorch-1,5-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-16-cpu"></a>AzureML-PyTorch-1,6-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-pytorch-16-gpu"></a>AzureML-PyTorch-1,6-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* Torch
* торчвисион
* MKL
* хоровод
* tensorboard
* будущем

## <a name="azureml-scikit-learn-0203"></a>AzureML-Scikit-учиться-0.20.3

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* scikit-learn
* scipy
* жоблиб

## <a name="azureml-tensorflow-110-cpu"></a>AzureML-TensorFlow-1,10-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow
* хоровод

## <a name="azureml-tensorflow-110-gpu"></a>AzureML-TensorFlow-1,10-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow — GPU
* хоровод

## <a name="azureml-tensorflow-112-cpu"></a>AzureML-TensorFlow-1,12-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow
* хоровод

## <a name="azureml-tensorflow-112-gpu"></a>AzureML-TensorFlow-1,12-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow — GPU
* хоровод

## <a name="azureml-tensorflow-113-cpu"></a>AzureML-TensorFlow-1,13-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow
* хоровод

## <a name="azureml-tensorflow-113-gpu"></a>AzureML-TensorFlow-1,13-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow — GPU
* хоровод

## <a name="azureml-tensorflow-20-cpu"></a>AzureML-TensorFlow-2,0-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow
* хоровод

## <a name="azureml-tensorflow-20-gpu"></a>AzureML-TensorFlow-2,0-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow — GPU
* хоровод

## <a name="azureml-tensorflow-21-cpu"></a>AzureML-TensorFlow-2,1-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow
* хоровод

## <a name="azureml-tensorflow-21-gpu"></a>AzureML-TensorFlow-2,1-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow — GPU
* хоровод

## <a name="azureml-tensorflow-22-cpu"></a>AzureML-TensorFlow-2,2-CPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow
* хоровод

## <a name="azureml-tensorflow-22-gpu"></a>AzureML-TensorFlow-2,2-GPU

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* tensorflow — GPU
* хоровод

## <a name="azureml-tutorial"></a>AzureML — учебник

**Каналы пакетов:**

* Anaconda
* conda — подделка

**Пакеты Conda:**

* python
* pandas
* numpy
* ткдм
* scikit-learn
* matplotlib

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-telemetry
* azureml-Training-рестклиентс-"устройство"
* azureml-train-core
* azureml-widgets
* azureml-pipeline-core
* azureml-pipeline-steps
* azureml-opendatasets
* azureml-automl-core
* azureml-automl-runtime
* azureml-train-automl-client
* azureml-train-automl-runtime
* azureml-train-automl
* azureml — обучение
* azureml-sdk
* azureml-interpret
* azureml-tensorboard
* azureml-mlflow
* млфлов
* sklearn — Pandas

## <a name="azureml-vowpalwabbit-880"></a>AzureML-Вовпалваббит-8.8.0

**Каналы пакетов:**

* conda — подделка

**Пакеты Conda:**

* python

**Пакеты PIP:**

* azureml-core
* azureml — значения по умолчанию
* azureml-DataSet-Runtime [предохранитель, Pandas]
