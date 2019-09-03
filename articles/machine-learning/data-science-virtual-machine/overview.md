---
title: Общие сведения о виртуальных машинах для обработки и анализа данных Azure на Linux и Windows | Документация Майкрософт
description: Основные аналитические сценарии и компоненты виртуальных машин Linux и Windows для обработки и анализа данных.
keywords: средства анализа и обработки данных, виртуальная машина для анализа и обработки данных, средства для анализа и обработки данных, анализ и обработка данных Linux
services: machine-learning
documentationcenter: ''
author: vijetajo
manager: cgronlun
ms.assetid: d4f91270-dbd2-4290-ab2b-b7bfad0b2703
ms.service: machine-learning
ms.subservice: data-science-vm
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 02/22/2019
ms.author: vijetaj
ms.openlocfilehash: 22192d67a946f9f466848b65b9cd763b16e6c5f0
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/28/2019
ms.locfileid: "70099373"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Что такое виртуальная машина для обработки и анализа данных Azure на Linux и Windows?

Виртуальная машина для обработки и анализа данных (DSVM) — это настроенный образ виртуальной машины на облачной платформе Azure, созданный специально для обработки и анализа данных. В ней предварительно установлено и настроено множество популярных средств для анализа и обработки данных, а также других средств, позволяющих быстро приступить к созданию интеллектуальных приложений для расширенной аналитики. 

Конфигурации средств тщательно протестированы специалистами по обработке и анализу данных и разработчиками корпорации Майкрософт, а также широким сообществом по обработке и анализу данных. Это тестирование помогает обеспечить стабильность и общую устойчивость.

DSVM можно использовать в следующих системах:
+ Windows Server 2016, Windows Server 2012
+ Ubuntu 16.04 LTS и CentOS 7.4

> [!NOTE]
> В виртуальную машину для обработки и анализа данных были добавлены все инструменты виртуальной машины для глубокого обучения. 


## <a name="what-can-i-do-with-the-dsvm"></a>Что можно сделать с помощью DSVM?
Назначение Виртуальной машины для обработки и анализа данных — обеспечить для специалистов, обладающих любыми навыками и в любых отраслях, беспроблемную предварительно настроенную и полностью интегрированную среду для обработки и анализа данных. Вместо развертывания сравнимой рабочей области можно подготавливать DSVM. Этот вариант позволит сэкономить дни или даже _недели_ при процессах установки, настройки и управления пакетами. Когда виртуальная машина DSVM будет выделена, можно немедленно приступать к проекту по обработке и анализу данных.

DSVM разработана и настроена для работы в широком диапазоне сценариев использования. Среду можно масштабировать по мере измерения требований проекта. Кроме того, вы можете использовать любой предпочитаемый язык для программирования задач обработки и анализа данных и устанавливать другие инструменты, чтобы настроить систему в соответствии со своими требованиями.

### <a name="preconfigured-analytics-desktop-in-the-cloud"></a>Предварительно настроенный облачный компьютер для анализа
DSVM предоставляет базовую конфигурацию для команд обработки и анализа данных, которые хотят заменить свои локальные компьютеры на управляемые облачные компьютеры. За счет базы у всех специалистов по обработке и анализу данных в рабочей группе будут согласованные настройки, позволяющие проверять эксперименты и повышать уровень взаимодействия. Это также снижает расходы за счет сокращения нагрузки на системных администраторов. Это сокращение нагрузки позволяет сократить время, затрачиваемое на вычисление, установку и обслуживание программных пакетов для расширенной аналитики.

### <a name="data-science-training-and-education"></a>Обучение обработке и анализу данных
Корпоративные инструкторы и преподаватели, обучающие классы по обработке и анализу данных, обычно предоставляют образ виртуальной машины. Этот образ гарантирует, что учащиеся имеют одинаковые настройки, а примеры программ работают предсказуемо. 

DSVM создает требуемую среду с согласованными настройками, что упрощает задачи поддержки и устраняет проблемы несовместимости. Для случаев, когда необходимо часто создавать такие среды, особенно в рамках коротких учебных курсов, характерно больше преимуществ.

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Эластичность по требованию для проектов большого масштаба
Интенсивная работа и соревнования по обработке и анализу данных, или моделирование и изучение данных большого масштаба обычно требуют использования мощного оборудования в течение короткого периода. DSVM помогает быстро и по требованию реплицировать среду обработки данных на развернутые серверы, что позволяет проводить эксперименты, требующие мощных вычислительных ресурсов для репликации.

### <a name="custom-compute-power-for-azure-notebooks"></a>Настройка вычислительной мощности для Записных книжек Azure
[Записные книжки Azure ](../../notebooks/azure-notebooks-overview.md)— это бесплатная размещенная служба для разработки и запуска записных книжек Jupyter, а также предоставления к ним общего доступа в облаке без их установки. Бесплатный уровень служб ограничен 4 ГБ памяти и 1 ГБ данных. 

Чтобы снять все ограничения, вы можете подключить проект "Записные книжки" к DSVM или к любой другой виртуальной машине, работающей на сервере Jupyter. Если вы вошли в "Записные книжки Azure" с помощью учетной записи, использующей Azure Active Directory (например, корпоративной учетной записи), в Записных книжках будут автоматически отображатся сведения о виртуальных машинах DSVM в любых подписках, связанных с этой учетной записью. Вы можете [подключить DSVM к Записным книжкам Azure](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier), чтобы расширить доступные вычислительные возможности.

### <a name="short-term-experimentation-and-evaluation"></a>Краткосрочные эксперименты и оценка
Вы можете использовать DSVM, чтобы вычислять или изучать инструменты, которые устанавливаются с минимальными усилиями:

- Microsoft Machine Learning Server
- SQL Server
- Инструменты Visual Studio
- Jupyter
- Наборы инструментов для глубокого и машинного обучения
- Новые инструменты, которые популярны в сообществе 

Поскольку DSVM быстронастраиваемая, ее можно применить в других краткосрочных сценариях использования. К ним относится репликация опубликованных экспериментов, исполнение демонстрационных версий и выполнение пошаговых руководств в интерактивных сеансах и руководствах на конференциях.

### <a name="deep-learning"></a>Глубокое обучение
В DSVM модели обучения могут использовать алгоритмы глубокого обучения на оборудовании, основанном на графических процессорах (GPU). Используя преимущества масштабирования виртуальных машин платформы Azure, DSVM помогает использовать оборудование на основе GPU в облаке в соответствии с вашими потребностями. Вы можете переключиться на виртуальную машину на основе GPU при обучении больших моделей или выполняете высокоскоростные вычисления, не меняя диск операционной системы.  

Версия DSVM для Windows Server 2016 содержит предварительно установленные драйверы GPU, платформы и версии GPU платформ глубокого обучения. В версии для Linux глубокое обучение на GPU включено во все версии DSVM, как для CentOS, так и для Ubuntu. 

Вы также можете развернуть DSVM в версии для Ubuntu, CentOS или Windows 2016 на виртуальной машине Azure, которая не основана на GPU. В этом случае все платформы глубокого обучения вернутся в режим ЦП.
 
[Дополнительные сведения о доступных платформах для глубокого обучения и искусственного интеллекта](dsvm-deep-learning-ai-frameworks.md).

<a name="included"></a>

## <a name="whats-included-on-the-dsvm"></a>Что входит в DSVM?
В этой виртуальной машине уже установлено и настроено множество популярных средств для обработки и анализа данных, а также глубокого обучения. Среди них также есть средства, упрощающие работу с различными данными и продуктами для анализа Azure. К этим продуктам относятся Microsoft Machine Learning Server (R, Python) для построения прогностических моделей и SQL Server 2017 для крупномасштабного исследования наборов данных. DSVM включает другие инструменты, предоставляемые сообществом с открытым исходным кодом и корпорацией Майкрософт, а также [пример кода и записных книжек](dsvm-samples-and-walkthroughs.md). 

Ниже приведен список средств и платформ:
+ [Поддерживаемые языки программирования](dsvm-languages.md)

+ [Поддерживаемые платформы данных](dsvm-data-platforms.md)

+ [Средства разработки и среды IDE](dsvm-tools-development.md)

+ [Платформы глубокого обучения и искусственного интеллекта](dsvm-deep-learning-ai-frameworks.md)

+ [Средства машинного обучения и обработки и анализа данных](dsvm-ml-data-science-tools.md)

+ [Средства приема данных](dsvm-tools-ingestion.md)

+ [Средства анализа и визуализации данных](dsvm-tools-explore-and-visualize.md)

В следующей таблице перечислены основные компоненты выпусков Windows и Linux виртуальной машины для обработки и анализа данных.

| **Средство**                                                           | **Версия для Windows** | **Версия для Linux** |
| :------------------------------------------------------------------ |:-------------------:|:------------------:|
| [Microsoft R Open](https://mran.microsoft.com/open/) с предустановленными популярными пакетами   |Да                      | Да             |
| [Microsoft Machine Learning Server (R, Python)](https://docs.microsoft.com/machine-learning-server/) Developer Edition (Версия для разработчиков) включает: <br />  &nbsp;&nbsp;&nbsp;&nbsp; [RevoScaleR/revoscalepy](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-revoscaler) — параллельная и распределенная высокопроизводительная платформа (R и Python);<br />  &nbsp;&nbsp;&nbsp;&nbsp; [MicrosoftML](https://docs.microsoft.com/machine-learning-server/r/concept-what-is-the-microsoftml-package) — новые современные алгоритмы машинного обучения от корпорации Майкрософт; <br />  &nbsp;&nbsp;&nbsp;&nbsp; [Ввод в эксплуатацию R и Python](https://docs.microsoft.com/machine-learning-server/what-is-operationalization).                                            |Да                      | Да |
| [Microsoft Office](https://products.office.com/business/office-365-proplus-business-software) ProPlus с общей активацией: Excel, Word и PowerPoint   |Да                      |Нет              |
| [Anaconda Python](https://www.continuum.io/) 2.7 и 3.5 с предварительно установленными популярными пакетами    |Да                      |Да              |
| [JuliaPro](https://juliacomputing.com/products/juliapro.html) с предварительно установленными популярными пакетами для языка Julia                         |Да                      |Да              |
| Реляционные базы данных                                                            | [SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition| [PostgreSQL](https://www.postgresql.org/) (CentOS)<br/>[SQL Server 2017](https://www.microsoft.com/sql-server/sql-server-2017) <br/> Developer Edition (Ubuntu) |
| Инструменты базы данных                                                       |  SQL Server Management Studio <br/> SQL Server Integration Services<br/> [bcp, sqlcmd](https://docs.microsoft.com/sql/tools/command-prompt-utility-reference-database-engine)<br />  Драйверы ODBC и JDBC|  [SQuirreL SQL](http://squirrel-sql.sourceforge.net/) (инструмент запросов), <br />  bcp, sqlcmd <br />  Драйверы ODBC и JDBC|
| Масштабируемая аналитика в базе данных с помощью служб машинного обучения SQL Server (R, Python) | Да     |Нет              |
| [Jupyter Notebook Server](https://jupyter.org/) со следующими ядрами:                                  | Да     | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp; R | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp; Python | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp; Julia | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp; PySpark | Да | Да |
|     &nbsp;&nbsp;&nbsp;&nbsp; [Sparkmagic](https://github.com/jupyter-incubator/sparkmagic) | Нет | Да (только Ubuntu) |
|     &nbsp;&nbsp;&nbsp;&nbsp; SparkR     | Нет | Да |
| JupyterHub (многопользовательский сервер записной книжки)| Нет | Да |
| JupyterLab (многопользовательский сервер записной книжки) | Нет | Да (только Ubuntu) |
| Инструменты разработки, интегрированные среды разработки и редакторы кода:| | |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio 2019 (Community Edition)](https://www.visualstudio.com/community/) с подключаемым модулем Git, Azure HDInsight (Hadoop), Azure Data Lake, SQL Server Data Tools, [Node.js](https://github.com/Microsoft/nodejstools), [Python](https://aka.ms/ptvs) и [Инструменты R для Visual Studio (RTVS)](https://microsoft.github.io/RTVS-docs/) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp; [Visual Studio Code](https://code.visualstudio.com/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Desktop](https://www.rstudio.com/products/rstudio/#Desktop) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [RStudio Server](https://www.rstudio.com/products/rstudio/#Server) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyCharm Community Edition](https://www.jetbrains.com/pycharm/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Atom](https://atom.io/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Juno (интегрированная среда разработки Julia)](https://junolab.org/)| Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; Vim and Emacs | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; Git and Git Bash | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; OpenJDK | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; .NET Framework | Да | Нет |
| Power BI Desktop | Да | Нет |
| Пакеты SDK для доступа к Azure и набор служб Cortana Intelligence | Да | Да |
| Инструменты перемещения данных и управления ими: | | |
| &nbsp;&nbsp;&nbsp;&nbsp;Обозреватель службы хранилища Azure | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Интерфейс командной строки Azure](https://docs.microsoft.com/cli/azure) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; Azure PowerShell | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp; [Azcopy](https://docs.microsoft.com/azure/storage/storage-use-azcopy) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp; [Драйвер больших двоичных объектов FUSE](https://github.com/Azure/azure-storage-fuse) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Adlcopy (Azure Data Lake Storage)](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-copy-data-azure-storage-blob) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp; [Средство миграции данных Azure Cosmos DB](https://docs.microsoft.com/azure/documentdb/documentdb-import-data) | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp; [Шлюз управления данными Майкрософт](https://msdn.microsoft.com/library/dn879362.aspx): перемещение данных между локальными источниками и облаком | Да | Нет |
| &nbsp;&nbsp;&nbsp;&nbsp; Программы командной строки для Unix и Linux | Да | Да |
| [Apache Drill](https://drill.apache.org) для исследования данных | Да | Да |
| Средства машинного обучения: |||
| &nbsp;&nbsp;&nbsp;&nbsp; Средства, интегрирующиеся с [машинным обучением Azure](https://azure.microsoft.com/services/machine-learning/) (R, Python) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [XGBoost](https://github.com/dmlc/xgboost) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Vowpal Wabbit](https://github.com/JohnLangford/vowpal_wabbit) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Weka](https://www.cs.waikato.ac.nz/ml/weka/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Rattle](https://togaware.com/rattle/) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [LightGBM](https://github.com/Microsoft/LightGBM) | Нет | Да (только Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [CatBoost](https://tech.yandex.com/catboost/) | Нет | Да (только Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [H2O](https://www.h2o.ai/h2o/), [Sparkling Water](https://www.h2o.ai/sparkling-water/) | Нет | Да (только Ubuntu) |
| Средства глубокого обучения, работающие на GPU или ЦП: |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp; [Microsoft Cognitive Toolkit (CNTK)](https://docs.microsoft.com/cognitive-toolkit/) (Windows 2016) | Да | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow](https://www.tensorflow.org/) | Y (Windows 2016) | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Horovod](https://github.com/uber/horovod) | Нет | Y (Ubuntu) |
| &nbsp;&nbsp;&nbsp;&nbsp; [MXNet](https://mxnet.io/) | Y (Windows 2016) | Да|
| &nbsp;&nbsp;&nbsp;&nbsp; [Caffe и Caffe2](https://github.com/caffe2/caffe2) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Chainer](https://chainer.org/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Torch](http://torch.ch/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Theano](https://github.com/Theano/Theano) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Keras](https://keras.io/)| Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [PyTorch](https://pytorch.org/)| Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [NVidia Digits](https://github.com/NVIDIA/DIGITS) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [Сервер модели MXNet](https://github.com/awslabs/mxnet-model-server) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorFlow Serving](https://www.tensorflow.org/serving/) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [TensorRT](https://developer.nvidia.com/tensorrt) | Нет | Да |
| &nbsp;&nbsp;&nbsp;&nbsp; [CUDA, cuDNN, драйвер NVIDIA](https://developer.nvidia.com/cuda-toolkit) | Да | Да |

## <a name="next-steps"></a>Дополнительная информация

См. сведения в следующих статьях:

+ Windows:
  + [Настройка Виртуальной машины для обработки и анализа данных (Windows)](provision-vm.md)
  + [Десять вещей, которые можно сделать с помощью Windows DSVM](vm-do-ten-things.md)

+ Linux:
  + [Настройка Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Настройка Linux DSVM (CentOS)](linux-dsvm-intro.md)
  + [Обработка и анализ данных в Linux DSVM](linux-dsvm-walkthrough.md)
