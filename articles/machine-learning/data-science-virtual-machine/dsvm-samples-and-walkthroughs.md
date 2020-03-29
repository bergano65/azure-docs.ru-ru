---
title: Примеры программ & пошаговых пошаговых отвагов ML
titleSuffix: Azure Data Science Virtual Machine
description: С помощью этих образцов и пошагов, узнать, как обрабатывать общие задачи и сценарии с Data Science Виртуальная машина.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: d064e940997d13e51be4fd38e9f678b778ce31f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74900046"
---
# <a name="samples-on-azure-data-science-virtual-machines"></a>Образцы виртуальных машин Azure Data Science

Виртуальные машины Azure Data Science (DSVMs) содержат полный набор образцовых кодов. Эти образцы включают ноутбуки Jupyter и скрипты на таких языках, как Python и R.
> [!NOTE]
> Для получения дополнительной информации о том, как запустить ноутбуки Jupyter на виртуальных машинах науки о данных, смотрите раздел [Access Jupyter.](#access-jupyter)

## <a name="prerequisites"></a>Предварительные требования

Для того, чтобы запустить эти образцы, вы должны иметь подготовлены данных наука виртуальная машина. Смотрите quickstarts для [Windows](./provision-vm.md) и [Ubuntu](./dsvm-ubuntu-intro.md).

## <a name="available-samples"></a>Доступные примеры
| Категории примеров | Описание | Расположения |
| ------------- | ------------- | ------------- |
| Язык R  | Образцы иллюстрируют сценарии, такие как как подключение к облачным хранилищам данных на основе Azure и как сравнить R и Microsoft Machine Learning Server с открытым исходным кодом. Они также объясняют, как можно операционние моделей на сервере Microsoft Machine Learning Server и сервере S'L. <br/> [R язык](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Язык Python  | В примерах объясняются такие сценарии, как подключение к облачным хранилищам данных на базе Azure и работа с Azure Machine Learning.  <br/> [Язык python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Язык Julia  | Предоставляет подробное описание заговора и глубокого обучения в Джулии. Также объясняет, как позвонить C и Python от Джулии. <br/> [Джулия язык](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Машинное обучение Azure  | Иллюстрирует, как создавать модели машинного обучения и глубокого обучения с помощью машинного обучения. Модели можно развертывать в любом расположении. Используйте автоматическое машинное обучение и интеллектуальную настройку гиперпараметров. Вы также можете использовать управление моделями и распределенное обучение. <br/> [Машинное обучение](#azure-machine-learning) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Записные книжки PyTorch  | Глубокое обучение образцов, которые используют PyTorch основе нейронных сетей. Записные книжки есть как для базовых, так и для расширенных сценариев.  <br/> [Ноутбуки PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Разнообразие образцов и методов нейронных сетей, реализованных с помощью платформы TensorFlow. <br/> [TensorFlow](#tensorflow) | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Образцы глубокого обучения, опубликованные группой Cognitive Toolkit в корпорации Майкрософт.  <br/> [Когнитивный инструмент](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Глубокое обучение образцов, которые используют Caffe2 основе нейронных сетей. Несколько ноутбуков знакомит пользователей с Caffe2 и как его эффективно использовать. Примеры включают предварительную обработку изображений и создание набора данных. Они также включают регрессию и как использовать предподготовленные модели. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Образцы на основе Python, которые используют H2O для реальных сценариев проблем. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Язык SparkML  | Образцы, которые используют функции apache Spark MLLib инструментарий через pySpark и MMLSpark: Microsoft машинного обучения для Apache Spark на Apache Spark 2.x.  <br/> [SparkML язык](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost; | Стандартные образцы машинного обучения в XGBoost для таких сценариев, как классификация и регрессия. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Доступ к Jupyter 

Чтобы получить доступ к Jupyter, выберите значок **Jupyter** в меню рабочего стола или приложения. Вы также можете получить доступ к Jupyter на Linux издание DSVM. Чтобы получить удаленный доступ из `https://<Full Domain Name or IP Address of the DSVM>:8000` веб-браузера, перейдите на Ubuntu.

Чтобы добавить исключения и сделать доступ Jupyter доступным в браузере, используйте следующие рекомендации:


![Добавление исключения Jupyter](./media/ubuntu-jupyter-exception.png)


Войдите в систему с тем же паролем, который вы используете для входа в виртуальную машину Data Science.
<br/>

**Джупитера дома**
<br/>![Домашняя страница Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Язык R 
<br/>![Примеры для R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Язык Python
<br/>![Примеры для Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Язык Julia 
<br/>![Примеры для Julia](./media/julia-samples.png)<br/>

## <a name="azure-machine-learning"></a>Машинное обучение Azure 
<br/>![Образцы машинного обучения Azure](./media/azureml-samples.png)<br/>

## <a name="pytorch"></a>PyTorch
<br/>![Примеры PyTorch](./media/pytorch-samples.png)<br/>

## <a name="tensorflow"></a>TensorFlow 
<br/>![Примеры TensorFlow](./media/tensorflow-samples.png)<br/>


## <a name="cntk"></a>CNTK 
<br/>![Примеры CNTK](./media/cntk-samples.png)<br/>


## <a name="caffe2"></a>Caffe2 
<br/>![Примеры caffe2](./media/caffe2-samples.png)<br/>

## <a name="h2o"></a>H2O 
<br/>![Примеры H2O](./media/h2o-samples.png)<br/>

## <a name="sparkml"></a>SparkML 
<br/>![Примеры SparkML](./media/sparkml-samples.png)<br/>

## <a name="xgboost"></a>XGBoost; 
<br/>![Примеры XGBoost](./media/xgboost-samples.png)<br/>

