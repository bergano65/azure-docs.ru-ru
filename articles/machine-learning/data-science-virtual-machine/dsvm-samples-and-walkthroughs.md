---
title: Примеры и пошаговые руководства для Виртуальных машин для обработки и анализа данных в Azure | Документация Майкрософт
description: Дополнительные сведения о примерах и пошаговых инструкциях по выполнению распространенных задач и сценариев для этой виртуальной машины.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: gopitk
manager: cgronlun
ms.custom: seodec18
ms.assetid: ''
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: gokuma
ms.openlocfilehash: e61f0f4ba30b29fea1b2fd5f2a2ab253d3a6710c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60502920"
---
# <a name="samples-on-data-science-virtual-machines"></a>Примеры для Виртуальных машин для обработки и анализа данных

Виртуальные машины для обработки и анализа данных Azure включают комплексный набор примеров кода. Примеры кода представлены в виде записных книжек Jupyter и скриптов на таких языках, как Python и R. 
> [!NOTE]
> Дополнительные сведения о том, как запустить записные книжки Jupyter на виртуальных машинах для обработки и анализа данных, см. в разделе [Доступ к Jupyter](#access-jupyter).

## <a name="quick-reference-of-samples"></a>Краткий справочник примеров
| Категории примеров | ОПИСАНИЕ | Расположения |
| ------------- | ------------- | ------------- |
| Язык R  | В примерах на языке R объясняются сценарии, такие как подключение к облачным хранилищам данных Azure. В них также объясняется сравнение между языками R с открытым кодом и Microsoft R и поясняется, как использовать модели в Microsoft R Server или SQL Server. <br/> [Язык R](#r-language) | <br/>`~notebooks` <br/> <br/> `~samples/MicrosoftR` <br/> <br/> `~samples/RSqlDemo` <br/> <br/> `~samples/SQLRServices`<br/> <br/>|
| Язык Python  | В примерах на Python рассматриваются такие сценарии, как подключение к облачным хранилищам данных Azure и работа с Машинным обучением Azure.  <br/> [Язык Python](#python-language) | <br/>`~notebooks` <br/><br/>|
| Язык Julia  | В примерах на Julia подробно рассматривается построение графиков и глубокое обучение, а также вызовы C и Python из Julia. <br/> [Язык Julia](#julia-language) |<br/> Windows:<br/> `~notebooks/Julia_notebooks`<br/><br/> Linux:<br/> `~notebooks/julia`<br/><br/> |
| Машинное обучение Azure  | Создавайте модели машинного обучения и глубокого обучения с помощью службы Машинного обучения Azure. Модели можно развертывать в любом расположении. Используйте автоматическое машинное обучение и интеллектуальную настройку гиперпараметров. Вы также можете использовать управление моделями и распределенное обучение. <br/> [Машинное обучение](#azureml) | <br/>`~notebooks/AzureML`<br/> <br/>|
| Записные книжки PyTorch  | Примеры глубокого обучения, использующие нейронные сети на основе PyTorch. Записные книжки есть как для базовых, так и для расширенных сценариев.  <br/> [Записные книжки PyTorch](#pytorch) | <br/>`~notebooks/Deep_learning_frameworks/pytorch`<br/> <br/>|
| TensorFlow  |  Различные примеры и методы нейронной сети, реализованные с помощью платформы TensorFlow. <br/> [TensorFlow](#tensorflow); | <br/>`~notebooks/Deep_learning_frameworks/tensorflow`<br/><br/> |
| Microsoft Cognitive Toolkit <br/>   | Примеры глубокого обучения, опубликованные командой Cognitive Toolkit в Майкрософт.  <br/> [Cognitive Toolkit](#cntk) | <br/> `~notebooks/DeepLearningTools/CNTK/Tutorials`<br/><br/> Linux:<br/> `~notebooks/CNTK`<br/> <br/>|
| Caffe2 | Примеры глубокого обучения, использующие нейронные сети на основе caffe2. С помощью нескольких записных книжек пользователи могут ознакомиться с caffe2 и его эффективным использованием. Примеры включают в себя создание набора данных и предварительную обработку изображений. Они также включают модель регрессии и сведения об использовании предварительно обученных моделей. <br/> [Caffe2](#caffe2) | <br/>`~notebooks/Deep_learning_frameworks/caffe2`<br/><br/> |
| H2O   | Примеры на основе Python, использующие H2O для решения проблем во множестве реальных сценариев. <br/> [H2O](#h2o) | <br/>`~notebooks/h2o`<br/><br/> |
| Язык SparkML  | Примеры использования функций набора средств MLlib Spark через pySpark и приложение "Библиотека машинного обучения Microsoft для Apache Spark (MMLSpark)" в Apache Spark 2.x.  <br/> [Язык SparkML](#sparkml) | <br/>`~notebooks/SparkML/pySpark`<br/>`~notebooks/MMLSpark`<br/><br/>  |
| XGBoost; | Примеры стандартного машинного обучения в XGBoost для таких сценариев, как классификация и регрессия. <br/> [XGBoost](#xgboost) | <br/>Windows:<br/>`\dsvm\samples\xgboost\demo`<br/><br/> |

<br/>

## <a name="access-jupyter"></a>Доступ к Jupyter 

Доступ к Jupyter можно получить, щелкнув значок `Jupyter` на рабочем столе или в меню приложения. Jupyter также можно открыть в выпусках Linux на Виртуальных машинах для обработки и анализа данных. Вы можете получить доступ удаленно из веб-браузера, посетив `https://<Full Domain Name or IP Address of the DSVM>:8000` в Ubuntu.

Чтобы добавить исключения и получить доступ к Jupyter через браузер, ознакомьтесь со следующим снимком экрана.


![Добавление исключения Jupyter](./media/ubuntu-jupyter-exception.png)


Войдите, используя один и тот же пароль, что и для входа на Виртуальные машины для обработки и анализа данных.
<br/>

**Домашняя страница Jupyter**
<br/>![Домашняя страница Jupyter](./media/jupyter-home.png)<br/>

## <a name="r-language"></a>Язык R 
<br/>![Примеры для R](./media/r-language-samples.png)<br/>

## <a name="python-language"></a>Язык Python
<br/>![Примеры для Python](./media/python-language-samples.png)<br/>

## <a name="julia-language"></a>Язык Julia 
<br/>![Примеры для Julia](./media/julia-samples.png)<br/>

## <a name="azureml"></a>AzureML 
<br/>![Примеры AzurekML](./media/azureml-samples.png)<br/>

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

