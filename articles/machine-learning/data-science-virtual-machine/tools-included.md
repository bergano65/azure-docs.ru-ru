---
title: Средства, входящие в состав DSVM
titleSuffix: Azure Data Science Virtual Machine tools
description: Список средств, включенных в образы Windows и Ubuntu DSVM
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: gvashishtha
ms.author: gopalv
ms.topic: overview
ms.date: 10/10/2019
ms.openlocfilehash: 594879df6e1e88c861a2730160d050194cc884ea
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73891080"
---
# <a name="what-tools-are-included-on-the-azure-data-science-virtual-machine"></a>Какие средства включены в виртуальную машину Azure для обработки и анализа данных?

Ниже мы включили обновленный список средств, включенных в виртуальную машину для обработки и анализа данных, а также ссылки для понимания того, как настроено каждое средство.


| **Средство**                                                           | **Виртуальная машина для обработки и анализа данных под управлением Windows** | **Виртуальная машина для обработки и анализа данных под управлением Linux** | **Примечания об использовании** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|:------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) с предустановленными популярными пакетами   |<span class='green-check'>&#9989;</span></br> (3.4.3)                    |<span class='green-check'>&#9989;</span></br> (3.4.3)  | [R на DSVM](./dsvm-languages.md#r)           |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition (Версия для разработчиков) включает: <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) — параллельная и распределенная высокопроизводительная платформа (R и Python);<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) — новые современные алгоритмы машинного обучения от корпорации Майкрософт; <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Ввод в эксплуатацию R и Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization).                                            |<span class='green-check'>&#9989;</span>  </br> (9.3.0)                 |<span class='green-check'>&#9989;</span> | |
| [Microsoft Office](https://www.microsoft.com/p/office-365-proplus/CFQ7TTC0K8R0) ProPlus с общей активацией: Excel, Word и PowerPoint   |<span class='green-check'>&#9989;</span>                       |<span class='red-x'>&#10060;</span>              | |
| [Anaconda Python](https://www.continuum.io/) 2.7 и 3.5 с предварительно установленными популярными пакетами    |<span class='green-check'>&#9989;</span>                       |<span class='green-check'>&#9989;</span></br> (4.2)        | |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) с предварительно установленными популярными пакетами для языка Julia                         |<span class='green-check'>&#9989;</span> </br> (0.6.4)                   |<span class='green-check'>&#9989;</span></br> (0.6.2)              | [Julia на DSVM](./dsvm-languages.md#julia) |
| Реляционные базы данных                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) | [SQL Server на DSVM](./dsvm-data-platforms.md#sql-server-2017-developer-edition) |
| Инструменты базы данных                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Драйверы ODBC и JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (инструмент запросов), <br />  bcp, sqlcmd <br />  Драйверы ODBC и JDBC| |
| Масштабируемая аналитика в базе данных с помощью служб машинного обучения SQL Server (R, Python) |<span class='green-check'>&#9989;</span>    | <span class='red-x'>&#10060;</span>                | |
| [Jupyter Notebook Server](https://jupyter.org/) со следующими ядрами:                                  |<span class='green-check'>&#9989;</span></br> (5.5.0)   |<span class='green-check'>&#9989;</span> | [Примеры записной книжки Jupyter](./dsvm-samples-and-walkthroughs.md) | 
|     &nbsp;&nbsp;&nbsp;&nbsp; R |<span class='green-check'>&#9989;</span></br> (3.4.3) |<span class='green-check'>&#9989;</span></br> (3.4.3) | [Примеры R Jupyter](./dsvm-samples-and-walkthroughs.md#r-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python |<span class='green-check'>&#9989;</span></br> (3) |<span class='green-check'>&#9989;</span> | [Примеры Python Jupyter](./dsvm-samples-and-walkthroughs.md#python-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia |<span class='green-check'>&#9989;</span></br> (0.6.4) |<span class='green-check'>&#9989;</span></br> (0.6.2) | [Примеры Julia Jupyter](./dsvm-samples-and-walkthroughs.md#julia-language) |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Примеры pySpark Jupyter](./dsvm-samples-and-walkthroughs.md#sparkml)
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (только Ubuntu) | |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterHub (многопользовательский сервер записной книжки)|<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | |
| JupyterLab (многопользовательский сервер записной книжки) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> | |
| Инструменты разработки, интегрированные среды разработки и редакторы кода:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2017 (Community Edition)](https://www.visualstudio.com/community/) с подключаемым модулем Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) и [Инструменты R для Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Visual Studio на DSVM](./dsvm-tools-development.md#visual-studio-community-2017) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) |<span class='green-check'>&#9989;</span></br> (1.31.1) |<span class='green-check'>&#9989;</span></br> (1.31) | [Visual Studio Code на DSVM](./dsvm-tools-development.md#visual-studio-code) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) |<span class='green-check'>&#9989;</span></br> (1.2.50xx) |<span class='green-check'>&#9989;</span></br> (1.1.456) | [RStudio Desktop на DSVM](./dsvm-tools-development.md#rstudio--desktop) |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Сервер RStudio на DSVM](./dsvm-tools-development.md#rstudio--server)
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) |<span class='green-check'>&#9989;</span></br> (19.2.3)  |<span class='green-check'>&#9989;</span></br> (2018.2.3) | [PyCharm на DSVM](./dsvm-tools-development.md#pycharm)
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.26.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (интегрированная среда разработки Julia)](https://junolab.org/)|<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span> | [Juno на DSVM](./dsvm-tools-development.md#juno)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vim](https://www.vim.org) |<span class='green-check'>&#9989;</span></br> (8.1.5) |<span class='green-check'>&#9989;</span></br> (7.4.1689) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Emacs](https://www.gnu.org/software/emacs) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (24.5.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Git](https://git-scm.com/) и Git Bash |<span class='green-check'>&#9989;</span></br> (2.20.1) |<span class='green-check'>&#9989;</span></br> (0.6.2) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [OpenJDK](https://openjdk.java.net) |<span class='green-check'>&#9989;</span></br> (1.8.0_201) |<span class='green-check'>&#9989;</span></br> (1.8.0_222) | |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework |<span class='green-check'>&#9989;</span></br> (4.7.2) |<span class='red-x'>&#10060;</span>   | |
| [Power BI Desktop](https://powerbi.microsoft.com/) |<span class='green-check'>&#9989;</span></br> (2.73.55xx) |<span class='red-x'>&#10060;</span>   |
| Пакеты SDK для доступа к Azure и набор служб Cortana Intelligence |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | [Рабочий стол Power BI на DSVM](./dsvm-tools-development.md#power-bi-desktop) |
| Инструменты перемещения данных и управления ими: | | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Обозреватель службы хранилища Azure](https://azure.microsoft.com/features/storage-explorer/) |<span class='green-check'>&#9989;</span></br> (1.10.1) |<span class='green-check'>&#9989;</span></br> (0.7.20160129.1) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure) |<span class='green-check'>&#9989;</span></br> (2.0.56) |<span class='green-check'>&#9989;</span></br> (2.0.58) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) |<span class='green-check'>&#9989;</span></br> (8.1.0) |<span class='red-x'>&#10060;</span>   | [Azcopy на DSVM](./dsvm-tools-ingestion.md#azcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Драйвер больших двоичных объектов FUSE](https://github.com/Azure/azure-storage-fuse) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.0.2) | [blobfuse на DSVM](./dsvm-tools-ingestion.md#blobfuse)
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) |<span class='green-check'>&#9989;</span></br> (1.0.13) |<span class='red-x'>&#10060;</span>   | [Adlcopy на DSVM](./dsvm-tools-ingestion.md#adlcopy)
| &nbsp;&nbsp;&nbsp;&nbsp; [Средство миграции данных Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) |<span class='green-check'>&#9989;</span> |<span class='red-x'>&#10060;</span>   | [Cosmos DB на DSVM](./dsvm-tools-ingestion.md#azure-cosmos-db-data-migration-tool) |
| &nbsp;&nbsp;&nbsp;&nbsp; Программы командной строки для Unix и Linux |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span> | |
| [Apache Drill](https://drill.apache.org) для исследования данных |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>(1.14.0) | [Apache Drill на DSVM](./dsvm-tools-explore-and-visualize.md#apache-drill)
| Средства машинного обучения: ||||
| &nbsp;&nbsp;&nbsp;&nbsp; Средства, интегрирующиеся с [машинным обучением Azure](https://azure.microsoft.com/services/machine-learning/) (R, Python) |<span class='green-check'>&#9989;</span></br> (0.2.7) |<span class='green-check'>&#9989;</span></br> (1.0.45) | [Пакет SDK для службы "Машинное обучение Azure"](./dsvm-ml-data-science-tools.md#azure-machine-learning-sdk-for-python)
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) |<span class='green-check'>&#9989;</span></br> (0.81) |<span class='green-check'>&#9989;</span></br> (0.80) | [XGBoost на DSVM](./dsvm-ml-data-science-tools.md#xgboost)
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (8.1) | [Vowpal Wabbit на DSVM](./dsvm-ml-data-science-tools.md#vowpal-wabbit)
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) |<span class='green-check'>&#9989;</span></br> (3.8) |<span class='green-check'>&#9989;</span></br> (3.8.0) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) |<span class='green-check'>&#9989;</span></br> (5.1.0) |<span class='green-check'>&#9989;</span></br> (5.2.2) | [Weka на DSVM](./dsvm-ml-data-science-tools.md#weka) |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.1.2) | [LightGBM на DSVM](./dsvm-ml-data-science-tools.md#lightgbm)
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.10.3) | |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (3.20.0.7) | [H20 на DSVM](./dsvm-ml-data-science-tools.md#h2o) |
| Средства глубокого обучения, работающие на GPU или ЦП: |  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe](https://github.com/BVLC/caffe) и [Caffe2](https://github.com/facebookarchive/caffe2) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span> | [Caffe](./dsvm-deep-learning-ai-frameworks.md#caffe) и [Caffe2](./dsvm-deep-learning-ai-frameworks.md#caffe2) на DSVM |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (5.2.0) | [Chainer на DSVM](./dsvm-deep-learning-ai-frameworks.md#chainer) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA, cuDNN, драйвер NVIDIA](https://developer.nvidia.com/cuda-toolkit) |<span class='green-check'>&#9989;</span></br> (10.0.130) |<span class='green-check'>&#9989;</span></br> (10.0.130) | [CUDA, cuDNN, драйвер NVIDIA на DSVM](./dsvm-deep-learning-ai-frameworks.md#cuda-cudnn-nvidia-driver) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/horovod/horovod) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (0.16.1) | [Horovod на DSVM](./dsvm-deep-learning-ai-frameworks.md#horovod) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (2.2.4) | [Keras на DSVM](./dsvm-deep-learning-ai-frameworks.md#keras) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (2.5.1) | [CNTK на DSVM](./dsvm-deep-learning-ai-frameworks.md#microsoft-cognitive-toolkit-cntk) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.apache.org/) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.3.0) | [MXNet на DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Сервер модели MXNet](https://github.com/awslabs/mxnet-model-server#quick-start) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br> (1.0.1) | [MXNet Model Server на DSVM](./dsvm-deep-learning-ai-frameworks.md#mxnet-model-server) |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia System Management Interface (nvidia-smi)](https://developer.nvidia.com/nvidia-system-management-interface) |<span class='green-check'>&#9989;</span> |<span class='green-check'>&#9989;</span></br>| [nvidia-smi на DSVM](./dsvm-deep-learning-ai-frameworks.md#nvidia-system-management-interface-nvidia-smi) |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.2.0) | [PyTorch на DSVM](./dsvm-deep-learning-ai-frameworks.md#pytorch) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org) |<span class='green-check'>&#9989;</span></br> (1.13) |<span class='green-check'>&#9989;</span></br> (1.13) | [TensorFlow на DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow) |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/tfx/guide/serving) |<span class='red-x'>&#10060;</span>   |<span class='green-check'>&#9989;</span></br> (1.12.0) | [TensorFlow Serving на DSVM](./dsvm-deep-learning-ai-frameworks.md#tensorflow-serving) |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | <span class='red-x'>&#10060;</span> | <span class='green-check'>&#9989;</span></br> (1.0.3) | [Theano на DSVM](./dsvm-deep-learning-ai-frameworks.md#theano)

