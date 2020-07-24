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
ms.topic: conceptual
ms.date: 07/08/2020
ms.openlocfilehash: 72ccf2a765f50358635e4a803ed0b92e60bd7d19
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87012218"
---
# <a name="azure-machine-learning-curated-environments"></a>Машинное обучение Azure проверенные среды

В этой статье перечислены проверенные среды в Машинное обучение Azure, а также пакеты и каналы, предварительно установленные в них. Проверенные среды предоставляются Машинное обучение Azure и доступны в рабочей области по умолчанию. Они поддерживаются кэшированными образами DOCKER, что сокращает затраты на подготовку к запуску и позволяет ускорить развертывание. Используйте эти среды для быстрого начала работы с различными платформами машинного обучения.

> [!NOTE]
> Этот список обновляется с 2020 июня. Используйте пакет SDK для Python, чтобы получить самый обновленный список. Дополнительные сведения см. в [статье о средах](./how-to-use-environments.md#use-a-curated-environment).

## <a name="azure-automl"></a>Azure Аутомл

- [AzureML Аутомл](#azureml-automl)
- [Графический процессор AzureML Аутомл](#azureml-automl-gpu)
- [AzureML Аутомл DNN](#azureml-automl-dnn)
- [Графический процессор AzureML Аутомл DNN](#azureml-automl-dnn-gpu)
- [Графический процессор концепции DNN для Azure Аутомл](#azureml-automl-dnn-vision-gpu)

### <a name="azureml-automl"></a>AzureML Аутомл

каналы пакетов:
- Anaconda
- conda — подделка
- pytorch

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-интерпретировать = = 1.8.0
  - azureml-объяснить-Model = = 1.8.0
  - azureml-аутомл-Core = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1
  - вывод схемы
  - pyarrow = = 0.17.0
  - корректировка — кпуинфо = = 5.0.0
- NumPy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- Копировать — xgboost<= 0,90
- фбпрофет = = 0,5
- setuptools-Git
- псутил>5.0.0, <6.0.0

### <a name="azureml-automl-gpu"></a>Графический процессор AzureML Аутомл

каналы пакетов:
- Anaconda
- conda — подделка
- pytorch

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-интерпретировать = = 1.8.0
  - azureml-объяснить-Model = = 1.8.0
  - azureml-аутомл-Core = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1
  - вывод схемы
  - pyarrow = = 0.17.0
  - корректировка — кпуинфо = = 5.0.0
- NumPy>= 1.16.0, <= 1.16.2
- Pandas>= 0.21.0, <= 0.23.4
- фбпрофет = = 0,5
- setuptools-Git
- псутил>5.0.0, <6.0.0

### <a name="azureml-automl-dnn"></a>AzureML Аутомл DNN

каналы пакетов:
- Anaconda
- conda — подделка
- pytorch

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-интерпретировать = = 1.8.0
  - azureml-объяснить-Model = = 1.8.0
  - azureml-аутомл-Core = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1
  - вывод схемы
  - pytorch-Transformers = = 1.0.0
  - спаци = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - корректировка — кпуинфо = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- NumPy>= 1.16.0, <= 1.16.2
- Копировать — xgboost<= 0,90
- фбпрофет = = 0,5
- setuptools-Git
- pytorch = 1.4.0
- кудатулкит = 10.0.130
- псутил>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-gpu"></a>Графический процессор AzureML Аутомл DNN

каналы пакетов:
- Anaconda
- conda — подделка
- pytorch

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-интерпретировать = = 1.8.0
  - azureml-объяснить-Model = = 1.8.0
  - azureml-аутомл-Core = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1
  - вывод схемы
  - Хоровод = = 0.19.4
  - pytorch-Transformers = = 1.0.0
  - спаци = = 2.1.8
  - https://aka.ms/automl-resources/packages/en_core_web_sm-2.1.0.tar.gz
  - pyarrow = = 0.17.0
  - корректировка — кпуинфо = = 5.0.0
- Pandas>= 0.21.0, <= 0.23.4
- NumPy>= 1.16.0, <= 1.16.2
- фбпрофет = = 0,5
- setuptools-Git
- pytorch = 1.4.0
- кудатулкит = 10.0.130
- псутил>5.0.0, <6.0.0

### <a name="azureml-automl-dnn-vision-gpu"></a>Графический процессор Аутомл DNN для концепции AzureML

сборки
- Python = 3.7
- показывает
  - azureml-аутомл-Core = = 1.8.0
  - azureml-Core = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-интерпретировать = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-объяснить-Model = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1
  - azureml-Training-аутомл = = 1.8.0
  - azureml-от участников сообщества-DataSet = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - azureml-конвейер — шаги = = 1.8.0
  - azureml-Pipeline = = 1.8.0
  - azureml-поезд = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-от участников сообщества-аутомл-DNN-видение = = 1.8.0

## <a name="azure-ml-designer"></a>Конструктор машинного обучения Azure

- [Конструктор AzureML](#azureml-designer)
- [ОПС в конструкторе AzureML](#azureml-designer-cv)
- [Преобразование ОПС в конструкторе AzureML](#azureml-designer-cv-transform)
- [Ввод-вывод конструктора AzureML](#azureml-designer-io)
- [NLP конструктора AzureML](#azureml-designer-nlp)
- [PyTorch конструктора AzureML](#azureml-designer-pytorch)
- [PyTorch для обучения AzureML Designer](#azureml-designer-pytorch-train)
- [Конструктор AzureML R](#azureml-designer-r)
- [Рекомендуемый конструктор AzureML](#azureml-designer-recommender)
- [Оценка конструктора AzureML](#azureml-designer-score)
- [Преобразование конструктора AzureML](#azureml-designer-transform)

### <a name="azureml-designer"></a>Конструктор AzureML

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.8
- scikit-удивление = 1.0.6
- показывает
  - azureml-Designer-Classic-modules = = 0.0.124
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - спаци = = 2.1.7

### <a name="azureml-designer-cv"></a>ОПС в конструкторе AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-ОПС-modules = = 0.0.6

### <a name="azureml-designer-cv-transform"></a>Преобразование ОПС в конструкторе AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-ОПС-modules [pytorch] = = 0.0.6

### <a name="azureml-designer-io"></a>Ввод-вывод конструктора AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml->, — 1,6
  - azureml-Designer-датаио-modules = = 0.0.30

### <a name="azureml-designer-nlp"></a>NLP конструктора AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-Classic-modules = = 0.0.121
  - https://github.com/explosion/spacy-models/releases/download/en_core_web_sm-2.1.0/en_core_web_sm-2.1.0.tar.gz#egg=en_core_web_sm
  - спаци = = 2.1.7

### <a name="azureml-designer-pytorch"></a>PyTorch конструктора AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-pytorch-train"></a>PyTorch для обучения AzureML Designer

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-pytorch-modules = = 0.0.8

### <a name="azureml-designer-r"></a>Конструктор AzureML R

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.8
- r-курсор = 6.0
- r-катулс = 1.17.1
- r-Cluster = 2.1.0
- r-dplyr = 0.8.5
- r-e1071 = 1.7
- r-форкатс = 0.5.0
- r-Прогноз = 8.12
- r-glmnet = 2.0
- r-ИГРАФ = 1.2.4
- r-Matrix = 1,2
- r-мклуст = 5.4.6
- r-мгкв = 1.8
- r-нлме = 3.1
- r-ннет = 7.3
- r-ПЛИР = 1.8.6
- r-randomforest = 4.6
- r-ретикулате = 1.12
- r-рокр = 1.0
- r-rodbc = 1.3
- r-rpart = 4.1
- r-String = 1.4.0
- r-тидиверсе = 1.2.1
- r-timedate = 3043.102
- r-тсериес = 0,10
- r = 3.5.1
- показывает
  - azureml-Designer-Classic-modules = = 0.0.124

### <a name="azureml-designer-recommender"></a>Рекомендуемый конструктор AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-рекомендовать-modules = = 0.0.5

### <a name="azureml-designer-score"></a>Оценка конструктора AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- conda
- Python = 3.6.8
- показывает
  - azureml-Designer-Score-modules = = 0.0.5

### <a name="azureml-designer-transform"></a>Преобразование конструктора AzureML

каналы пакетов:
- Значения по умолчанию

сборки
- Python = 3.6.8
- показывает
  - azureml-Designer-Transform-modules = = 0.0.49

## <a name="azureml-hyperdrive-forecastdnn"></a>Форекастднн устройство AzureML

сборки
- Python = 3.7
- показывает
  - azureml-Core = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-аутомл-Core = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1
  - azureml-от участников сообщества-аутомл-DNN-прогнозирование = = 1.8.0

## <a name="azureml-minimal"></a>Минимум AzureML

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0

## <a name="azureml-sidecar"></a>AzureML расширения

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2

## <a name="azureml-tutorial"></a>Руководство по AzureML

каналы пакетов:
- Anaconda
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - azureml-Widget = = 1.8.0
  - azureml-конвейер-Core = = 1.8.0
  - azureml-конвейер — шаги = = 1.8.0
  - azureml-опендатасетс = = 1.8.0
  - azureml-аутомл-Core = = 1.8.0
  - azureml-аутомл-Runtime = = 1.8.0
  - azureml-Training-аутомл-Client = = 1.8.0
  - azureml-Training-аутомл-Runtime = = 1.8.0. post1  
  - azureml-Training-аутомл = = 1.8.0
  - azureml-поезд = = 1.8.0
  - azureml-SDK = = 1.8.0
  - azureml-интерпретировать = = 1.8.0
  - azureml-tensorboard = = 1.8.0
  - azureml-млфлов = = 1.8.0
  - млфлов
  - sklearn — Pandas
- pandas
- numpy
- ткдм
- scikit-learn
- matplotlib

## <a name="azureml-vowpalwabbit-880"></a>AzureML Вовпалваббит 8.8.0

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-подготовка [предохранитель, Pandas]

## <a name="dask"></a>даск

- [ЦП Даск AzureML](#azureml-dask-cpu)
- [Графический процессор AzureML Даск](#azureml-dask-gpu)

### <a name="azureml-dask-cpu"></a>ЦП Даск AzureML

каналы пакетов:
- conda — подделка
- pytorch
- Значения по умолчанию

сборки
- Python = 3.6.9
- показывает
  - адлфс
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - Даск [полный]
  - Даск-ml [полный]
  - распределенные
  - фастпаркует
  - фсспек
  - жоблиб
  - jupyterlab
  - lz4
  - mpi4py
  - записная книжка
  - pyarrow

### <a name="azureml-dask-gpu"></a>Графический процессор AzureML Даск

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.9
- показывает
  - azureml — значения по умолчанию = = 1.8.0
  - адлфс
  - azureml-Core = = 1.8.0
  - azureml-dataprep
  - Даск [полный]
  - Даск-ml [полный]
  - распределенные
  - фастпаркует
  - фсспек
  - жоблиб
  - jupyterlab
  - lz4
  - mpi4py
  - записная книжка
  - pyarrow
- matplotlib

## <a name="chainer"></a>Chainer

- [ЦП 5.1.0 формирователя цепочки AzureML](#azureml-chainer-510-cpu)
- [GPU 5.1.0 "формирователь цепочки AzureML"](#azureml-chainer-510-gpu)

### <a name="azureml-chainer-510-cpu"></a>ЦП 5.1.0 формирователя цепочки AzureML

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - формирователь цепочки = = 5.1.0
  - mpi4py = = 3.0.0

### <a name="azureml-chainer-510-gpu"></a>GPU 5.1.0 "формирователь цепочки AzureML"

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - формирователь цепочки = = 5.1.0
  - cupy-cuda90 = = 5.1.0
  - mpi4py = = 3.0.0

## <a name="pyspark"></a>PySpark

### <a name="azureml-pyspark-mmlspark-015"></a>AzureML PySpark MmlSpark 0,15

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0

## <a name="pytorch"></a>PyTorch

- [ЦП AzureML PyTorch 1,0](#azureml-pytorch-10-cpu)
- [Графический процессор AzureML PyTorch 1,0](#azureml-pytorch-10-gpu)
- [ЦП AzureML PyTorch 1,1](#azureml-pytorch-11-cpu)
- [Графический процессор AzureML PyTorch 1,1](#azureml-pytorch-11-gpu)
- [ЦП AzureML PyTorch 1,2](#azureml-pytorch-12-cpu)
- [Графический процессор AzureML PyTorch 1,2](#azureml-pytorch-12-gpu)
- [ЦП AzureML PyTorch 1,3](#azureml-pytorch-13-cpu)
- [Графический процессор AzureML PyTorch 1,3](#azureml-pytorch-13-gpu)
- [ЦП AzureML PyTorch 1,4](#azureml-pytorch-14-cpu)
- [Графический процессор AzureML PyTorch 1,4](#azureml-pytorch-14-gpu)
- [ЦП AzureML PyTorch 1,5](#azureml-pytorch-15-cpu)
- [Графический процессор AzureML PyTorch 1,5](#azureml-pytorch-15-gpu)

### <a name="azureml-pytorch-10-cpu"></a>ЦП AzureML PyTorch 1,0

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1,0
  - торчвисион = = 0.2.1
  - MKL = = 2018.0.3
  - Хоровод = = 0.16.1

### <a name="azureml-pytorch-10-gpu"></a>Графический процессор AzureML PyTorch 1,0

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1,0
  - торчвисион = = 0.2.1
  - MKL = = 2018.0.3
  - Хоровод = = 0.16.1

### <a name="azureml-pytorch-11-cpu"></a>ЦП AzureML-PyTorch 1,1

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.1
  - торчвисион = = 0.2.1
  - MKL = = 2018.0.3
  - Хоровод = = 0.16.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-11-gpu"></a>Графический процессор AzureML PyTorch 1,1

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.1
  - торчвисион = = 0.2.1
  - MKL = = 2018.0.3
  - Хоровод = = 0.16.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-12-cpu"></a>ЦП AzureML PyTorch 1,2

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1,2
  - торчвисион = = 0.4.0
  - MKL = = 2018.0.3
  - Хоровод = = 0.16.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-12-gpu"></a>Графический процессор AzureML PyTorch 1,2

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1,2
  - торчвисион = = 0.4.0
  - MKL = = 2018.0.3
  - Хоровод = = 0.16.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-13-cpu"></a>ЦП AzureML PyTorch 1,3

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.3
  - торчвисион = = 0.4.1
  - MKL = = 2018.0.3
  - Хоровод = = 0.18.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-13-gpu"></a>Графический процессор AzureML PyTorch 1,3

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.3
  - торчвисион = = 0.4.1
  - MKL = = 2018.0.3
  - Хоровод = = 0.18.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-14-cpu"></a>ЦП AzureML PyTorch 1,4

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.4.0
  - торчвисион = = 0.5.0
  - MKL = = 2018.0.3
  - Хоровод = = 0.18.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-14-gpu"></a>Графический процессор AzureML PyTorch 1,4

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.4.0
  - торчвисион = = 0.5.0
  - MKL = = 2018.0.3
  - Хоровод = = 0.18.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-15-cpu"></a>ЦП AzureML PyTorch 1,5

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.5.0
  - торчвисион = = 0.5.0
  - MKL = = 2018.0.3
  - Хоровод = = 0.19.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

### <a name="azureml-pytorch-15-gpu"></a>Графический процессор AzureML PyTorch 1,5

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - Torch = = 1.5.0
  - торчвисион = = 0.5.0
  - MKL = = 2018.0.3
  - Хоровод = = 0.19.1
  - tensorboard==1.14.0
  - будущее = = 0.17.1

## <a name="scikit-learn"></a>Scikit

### <a name="azureml-scikit-learn-0203"></a>AzureML Scikit — изучение 0.20.3

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - scikit-learn==0.20.3
  - SciPy = = 1.2.1
  - жоблиб = = 0.13.2

## <a name="tensorflow"></a>TensorFlow

- [ЦП Azure ML TensorFlow 1,10](#azureml-tensorflow-110-cpu)
- [GPU TensorFlow 1,10 для машинного обучения Azure](#azureml-tensorflow-110-gpu)
- [ЦП Azure ML TensorFlow 1,12](#azureml-tensorflow-112-cpu)
- [GPU TensorFlow 1,12 для машинного обучения Azure](#azureml-tensorflow-112-gpu)
- [ЦП Azure ML TensorFlow 1,13](#azureml-tensorflow-113-cpu)
- [GPU TensorFlow 1,13 для машинного обучения Azure](#azureml-tensorflow-113-gpu)
- [ЦП Azure ML TensorFlow 2,0](#azureml-tensorflow-20-cpu)
- [GPU TensorFlow 2,0 для машинного обучения Azure](#azureml-tensorflow-20-gpu)
- [ЦП Azure ML TensorFlow 2,1](#azureml-tensorflow-21-cpu)
- [GPU TensorFlow 2,1 для машинного обучения Azure](#azureml-tensorflow-21-gpu)

### <a name="azureml-tensorflow-110-cpu"></a>ЦП AzureML TensorFlow 1,10

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow = = 1,10
  - Хоровод = = 0.15.2

### <a name="azureml-tensorflow-110-gpu"></a>Графический процессор AzureML TensorFlow 1,10

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow-GPU = = 1.10.0
  - Хоровод = = 0.15.2

### <a name="azureml-tensorflow-112-cpu"></a>ЦП AzureML TensorFlow 1,12

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow = = 1.12
  - Хоровод = = 0.15.2

### <a name="azureml-tensorflow-112-gpu"></a>Графический процессор AzureML TensorFlow 1,12

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow-GPU = = 1.12.0
  - Хоровод = = 0.15.2

### <a name="azureml-tensorflow-113-cpu"></a>ЦП AzureML-TensorFlow 1,13

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow = = 1.13.1
  - Хоровод = = 0.16.1

### <a name="azureml-tensorflow-113-gpu"></a>Графический процессор AzureML TensorFlow 1,13

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow-GPU = = 1.13.1
  - Хоровод = = 0.16.1

### <a name="azureml-tensorflow-20-cpu"></a>ЦП AzureML TensorFlow 2,0

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow = = 2.0
  - Хоровод = = 0.18.1

### <a name="azureml-tensorflow-20-gpu"></a>Графический процессор AzureML TensorFlow 2,0

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow-GPU = = 2.0.0
  - Хоровод = = 0.18.1

### <a name="azureml-tensorflow-21-cpu"></a>ЦП AzureML TensorFlow 2,1

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow = = 2.1.0
  - Хоровод = = 0.19.1

### <a name="azureml-tensorflow-21-gpu"></a>Графический процессор AzureML TensorFlow 2,1

каналы пакетов:
- conda — подделка

сборки
- Python = 3.6.2
- показывает
  - azureml-Core = = 1.8.0
  - azureml — значения по умолчанию = = 1.8.0
  - azureml-телеметрии = = 1.8.0
  - azureml-Training-рестклиентс-a Drive = = 1.8.0
  - azureml-Training-Core = = 1.8.0
  - tensorflow-GPU = = 2.1.0
  - Хоровод = = 0.19.1

