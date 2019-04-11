---
title: Настройка среды разработки Python
titleSuffix: Azure Machine Learning service
description: Узнайте, как настроить среду разработки при работе со Службой машинного обучения Azure. Изучив эту статью, вы научитесь использовать среды Conda, создавать файлы конфигурации и настраивать Jupyter Notebook, Записные книжки Azure, интегрированные среды разработки, Azure Databricks, редакторы кода и Виртуальную машину для обработки и анализа данных.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 02/24/2019
ms.custom: seodec18
ms.openlocfilehash: d4866a6863143d2228c556a64c8e75c9f273076e
ms.sourcegitcommit: 6e32f493eb32f93f71d425497752e84763070fad
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/10/2019
ms.locfileid: "59469902"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Настройка среды разработки для Машинного обучения Azure

В этой статье описано, как настроить среду разработки для работы со Службой машинного обучения Azure. Служба машинного обучения не зависит от платформы.

Единственное требование для среды разработки — Python 3, Anaconda (для изолированных средах) и файл конфигурации, который содержит сведения о рабочей области машинного обучения Azure.

В этой статье описаны следующие среды и средства.

* Записные книжки Azure. Служба Jupyter Notebook, размещенная в облаке Azure. Это самый простой способ начать работу, так как пакет SDK для Машинного обучения Azure уже установлен.

* [Виртуальная машина для обработки и анализа данных (DSVM)](#dsvm). Предварительно настроенная среда для разработки и экспериментов в облаке Azure, которая предназначена для обработки и анализа данных и может быть развернута как на экземплярах виртуальных машин на основе ЦП, так и на экземплярах виртуальных машин на основе GPU. Установленные Python 3, Conda, приложения Jupyter Notebook и пакет SDK для Машинного обучения Azure. Виртуальная машина предоставляется с популярными платформами машинного или глубокого обучения, а также с инструментами и редакторами для разработки решений машинного обучения. Это, вероятно, самая многофункциональная среда разработки для машинного обучения на платформе Azure.

* [Jupyter Notebook](#jupyter). Если вы уже используете Jupyter Notebook, вам нужно установить некоторые вспомогательные компоненты, которые содержит пакет SDK.

* [Visual Studio Code](#vscode). Если вы используете Visual Studio Code, то вы можете установить некоторые полезные расширения.

* [Azure Databricks](#aml-databricks). Популярная платформа для аналитики данных на основе Apache Spark. Узнайте, как установить пакет SDK для Машинного обучения Azure в кластере, чтобы получить возможность развертывать модели.

* [Записные книжки Azure](#aznotebooks). Служба Jupyter Notebook, размещенная в облаке Azure. Также легко приступить к работе, так как уже установлен пакет SDK Azure Machine Learning.  

Если у вас уже есть среда Python 3, или вам нужно выполнить основные шаги для установки пакета SDK, см. инструкции, предназначенные для [локального компьютера](#local).

## <a name="prerequisites"></a>Технические условия

- Рабочая область службы машинного обучения Azure. Чтобы создать рабочую область, см. в разделе [создать рабочую область службы машинного обучения Azure](setup-create-workspace.md).

Рабочая область — все, что нужно для начала работы с вашей [записных книжек Azure](#aznotebooks), [DSVM](#dsvm), или [Azure Databricks](#aml-databricks).

Для установки пакета SDK для среды для вашего [локального компьютера](#local), [сервер записных книжек Jupyter](#jupyter) или [Visual Studio Code](#vscode) необходимо также:

- Либо [Anaconda](https://www.anaconda.com/download/) или [Miniconda](https://conda.io/miniconda.html) диспетчера пакетов.

- В Linux или macOS вам понадобится оболочка bash.

    > [!TIP]
    > Если в Linux или macOS вы используете оболочку, отличающуюся от bash (например, zsh), при выполнении некоторых команд могут возникнуть ошибки. Чтобы решить эту проблему, используйте команду `bash`, чтобы запустить bash и выполните там нужные команды.

- В Windows вам понадобится командная строка по умолчанию или командная строка Anaconda (устанавливается вместе с Anaconda и Miniconda).

## <a id="aznotebooks"></a>Записные книжки Azure

[Записные книжки Azure](https://notebooks.azure.com) (предварительная версия) — это интерактивная среда разработки в облаке Azure. Это простой способ приступить к работе с разработки машинного обучения Azure.

* Пакет SDK для Машинного обучения Azure уже установлен.
* Создав рабочую область Службы машинного обучения Azure на портале Azure, можно нажать кнопку, чтобы автоматически настроить среду Записных книжек Azure для работы с рабочей областью.

Используйте [портала Azure](https://portal.azure.com) приступить к работе с Azure Notebooks.  Откройте рабочую область и из **Обзор** выберите **приступить к работе в записных книжках Azure**.

По умолчанию Записные книжки Azure используют бесплатный уровень обслуживания, который ограничен 4 ГБ памяти и 1 ГБ данных. Однако вы можете снять эти ограничения, подключив экземпляр Виртуальной машины для обработки и анализа данных к проекту Записных книжек Azure. Дополнительные сведения см. в статье [Manage and configure Azure Notebooks projects — Compute tier](/azure/notebooks/configure-manage-azure-notebooks-projects#compute-tier) (Управление и настройка проектов Записных книжек Azure — уровень вычислений).    


## <a id="dsvm"></a>Виртуальная машина для обработки и анализа данных

DSVM — это настраиваемый образ виртуальной машины. Он предназначен для обработки и анализа данных, и на нем предварительно настроены:

  - пакеты TensorFlow, PyTorch, Scikit-learn, XGBoost и пакет SDK для Машинного обучения Azure;
  - популярные инструменты для обработки и анализа данных, в том числе изолированная среда Spark и Drill;
  - инструменты Azure, такие как интерфейс командной строки Azure, AzCopy и Обозреватель службы хранилища;
  - интегрированные среды разработки, например Visual Studio Code и PyCharm;
  - Сервер Jupyter Notebook.

Пакет SDK для Машинного обучения Azure работает с версией DSVM для Ubuntu или Windows. Но если вы также планируете использовать Виртуальную машину для обработки и анализа данных в качестве цели для вычислений, это возможно только на Ubuntu.

Чтобы использовать DSVM в качестве среды разработки, выполните следующие действия.

1. Создайте DSVM в любой из следующих сред:

    * На портале Azure:

        * [Создайте виртуальную машину обработки и анализа данных Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Создание виртуальной машины Windows для обработки и анализа данных](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Интерфейс командной строки Azure:

        > [!IMPORTANT]
        > * Чтобы использовать Azure CLI, нужно сначала войти в подписку Azure с помощью команды `az login`.
        >
        > * При использовании команд на этом шаге необходимо указать имя группы ресурсов, имя виртуальной машины, имя пользователя и пароль.

        * Чтобы создать DSVM на платформе Ubuntu, воспользуйтесь приведенной ниже командой.

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Чтобы создать DSVM на платформе Windows, воспользуйтесь приведенной ниже командой.

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Пакет SDK для Машинного обучения Azure уже установлен в DSVM. Чтобы использовать среду Conda, которая содержит пакет SDK, используйте одну из следующих команд:

    * Виртуальная машина для обработки и анализа данных под управлением Ubuntu:

        ```shell
        conda activate py36
        ```

    * Виртуальная машина для обработки и анализа данных под управлением Windows:

        ```shell
        conda activate AzureML
        ```

1. Чтобы убедиться, у вас есть доступ к пакету SDK и проверить версию, используйте следующий код Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Дополнительные сведения о настройке Виртуальной машины для обработки и анализа данных для роботы с рабочей областью Службы машинного обучения Azure см. в разделе [Создание файла конфигурации рабочей области](#workspace).

Дополнительные сведения о Виртуальных машинах для обработки и анализа данных см. [здесь](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Локальный компьютер

При использовании локального компьютера (который также может быть удаленной виртуальной машине), создать среду Anaconda и установить пакет SDK, сделав следующее:

1. Скачайте и установите [Anaconda](https://www.anaconda.com/distribution/#download-section) (версии Python 3.7) Если у вас еще нет.

1. Откройте командную строку с Anaconda и создать среду, выполнив следующие команды:

    Выполните следующую команду для создания среды.

    ```shell
    conda create -n myenv python=3.6.5
    ```

    Затем активируйте среду.

    ```shell
    conda activate myenv
    ```

    В этом примере создается окружение с помощью python 3.6.5, но можно выбрать любой определенной subversions. Совместимости пакета SDK может не гарантируется, с помощью некоторых основных версий (3.5 + рекомендуется), и мы рекомендуем попробовать разные версии/subversion в вашей среде Anaconda, при возникновении ошибки. Скачивание компонентов и пакетов и последующее создание среды займет несколько минут.

1. Выполните следующие команды в новой среде для конкретной среды ipython ядра. Это обеспечит ожидаемый ядра и пакет импорта поведение, при работе с записными книжками Jupyter в средах Anaconda:

    ```shell
    conda install notebook ipykernel
    ```

    Затем выполните следующую команду, чтобы создать ядра:

    ```shell
    ipython kernel install --user
    ```

1. Чтобы установить пакеты, используйте следующие команды:

    Эта команда устанавливает базовый пакет SDK Azure машины обучения с помощью записной книжки и automl дополнения. `automl` Очень больших установку и может быть удален из квадратные скобки, если вы не собираетесь запустить автоматические эксперименты. `automl` Дополнительных также включает пакет SDK Azure машины обучения данные подготовки по умолчанию как зависимость.

     ```shell
    pip install azureml-sdk[notebooks,automl]
    ```

    Используйте следующую команду для установки пакета SDK Azure Machine Learning данных подготовки сам по себе:

    ```shell
    pip install azureml-dataprep
    ```

   > [!NOTE]
   > Если появится сообщение о том, что не удается удалить PyYAML, попробуйте использовать другую команду:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Он займет несколько минут, чтобы установить пакет SDK. См. в разделе [руководство по установке](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py) Дополнительные сведения о вариантах установки.

1. Установите другие пакеты для машинного обучения "Экспериментирование".

    Используйте одну из приведенных ниже команд и замените  *\<новый пакет >* с пакет, который требуется установить. Установка пакетов с помощью `conda install` требует, что пакет является частью текущего каналов (новые каналы могут добавляться в облаке Anaconda).

    ```shell
    conda install <new package>
    ```

    Кроме того, вы можете установить пакеты через `pip`.

    ```shell
    pip install <new package>
    ```

### <a id="jupyter"></a>Приложения Jupyter Notebook

Приложения Jupyter Notebook предоставляются компанией [Project Jupyter](https://jupyter.org/). Это решение предназначено для реализации интерактивного процесса кодирования, в котором создаются документы с динамическим кодом, описательным текстом и графикой. Приложения Jupyter Notebook также позволяют использовать результаты вашей работы совместно с другими пользователями, так как вы можете сохранять выходные данные разделов кода в документе. Jupyter Notebook можно устанавливать на различных платформах.

Процедуру, описанную в [локального компьютера](#local) раздел устанавливает необходимые компоненты для с записными книжками Jupyter в среде Anaconda. Чтобы включить эти компоненты в вашей среде Jupyter Notebook, следуйте инструкциям, приведенным ниже.

1. Откройте командную строку с Anaconda и активации вашей среды.

    ```shell
    conda activate myenv
    ```

1. Запустите сервер Jupyter Notebook с помощью следующей команды:

    ```shell
    jupyter notebook
    ```

1. Чтобы убедиться, что записная книжка Jupyter можно использовать пакет SDK, создайте **New** записной книжки выберите **Python 3** качестве ядра и затем выполните следующую команду в ячейку записной книжки:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. При возникновении проблем с импортом модулей и получать `ModuleNotFoundError`, ядро Jupyter должна быть подключена на правильный путь для вашей среды, выполнив следующий код в ячейку записной книжки.

    ```python
    import sys
    sys.path
    ```

1. Дополнительные сведения о настройке Jupyter Notebook для роботы с рабочей областью Службы машинного обучения Azure см. в разделе [Создание файла конфигурации рабочей области](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code — это кроссплатформенный редактор кода. Для его использования требуется локальная установка Python 3 и Conda для поддержки Python, но он также предоставляет дополнительные средства для работы с искусственным интеллектом. Кроме того, этот редактор предоставляет поддержку для выбора среды Conda в редакторе кода.

Чтобы использовать Visual Studio Code для разработки, сделайте следующее:

1. Дополнительные сведения об использовании Visual Studio Code для разработки с помощью Python см. в руководстве по [началу работы с Python в VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Чтобы выбрать среду Conda, откройте VS Code и используйте клавиши CTRL+SHIFT+P (Linux и Windows) или COMMAND+SHIFT+P (Mac).
    После этого откроется __палитра команд__.

1. Введите __Python: Select Interpreter__ (Python: выбор интерпретатора) и выберите среду Conda.

1. Чтобы убедиться, что вы можете использовать пакет SDK, создайте и запустите новый файл Python (PY), содержащий следующий код.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Чтобы установить расширение Машинного обучения Azure для Visual Studio Code, перейдите на страницу [Azure Machine Learning for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) (Машинное обучения Azure для Visual Studio Code).

    Дополнительные сведения см. в статье [Приступая к работе с Azure Machine Learning for Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks — это среда на основе Apache Spark, в облаке Azure. Он предоставляет среде записной книжки на базе совместной работы с кластером на основе вычислительных ресурсов ЦП или GPU.

Как Azure Databricks работает со службой машинного обучения Azure:
+ Можно обучить модель с помощью Spark MLlib и развертывании модели в ACI и AKS из, в Azure Databricks. 
+ Можно также использовать [автоматической машинного обучения](concept-automated-ml.md) возможности в специальный пакет SDK Azure ML с Azure Databricks.
+ Azure Databricks можно использовать в качестве целевого объекта вычислений из [конвейера машинного обучения Azure](concept-ml-pipelines.md). 

### <a name="set-up-your-databricks-cluster"></a>Настройка кластера Databricks

Создание [кластера Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal). Некоторые параметры применяются только в том случае, если вы установите пакет SDK для автоматических машинное обучение в Databricks.
**Создание кластера занимает несколько минут.**

Используйте следующие параметры:

| Параметр |Применяется к| Значение |
|----|---|---|
| Имя кластера |всегда| yourclustername |
| Среда выполнения Databricks |всегда| Любая среда выполнения, не относящаяся к Машинному обучению (версии 4.x, 5.x) |
| Версия Python |всегда| 3 |
| Рабочие роли |всегда| 2 или больше |
| Типы виртуальных машин узла рабочей роли <br>(определяет максимальное количество параллельных итераций) |Автоматическое Машинное обучение<br>Только| Предпочитается виртуальная машина, оптимизированная для операций в памяти |
| Включение автомасштабирования |Автоматическое Машинное обучение<br>Только| Снять пометку |

Дождитесь запуска кластера, прежде чем продолжать.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Установка правильного пакета SDK в библиотеке Databricks
После запуска кластера [создать библиотеку](https://docs.databricks.com/user-guide/libraries.html#create-a-library) для присоединения к кластеру соответствующий пакет SDK для Azure Machine Learning пакет. 

1. Выберите **только один** параметр (другие Установка пакета SDK не поддерживаются)

   |Пакет SDK для&nbsp;пакета&nbsp;дополнения|Источник|PyPi&nbsp;имя&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Для Databricks| Отправка Python Egg или PyPI | azureml-sdk[databricks]|
   |Для Databricks - with-<br> возможности автоматического машинного Обучения| Отправка Python Egg или PyPI | azureml-sdk[automl_databricks]|

   > [!Warning]
   > Нет других дополнения пакета SDK можно установить. Выберите только один из перечисленных параметров [databricks] или [automl_databricks].

   * Не устанавливайте **автоматически присоединяются к всех кластеров**.
   * Выберите **Attach** рядом с именем кластера.

1. Отслеживание ошибок, пока состояние не изменится на **Attached**, которая может занять несколько минут.  Если этот шаг завершается ошибкой, проверьте следующее: 

   Попробуйте перезапустить кластера путем:
   1. В левой области щелкните **Кластеры**.
   1. Выберите имя кластера в таблице.
   1. На вкладке **Библиотеки** щелкните **Перезапустить**.
      
   Также рассмотрите возможность:
   + В файле конфигурации Automl, при использовании Azure Databricks, добавьте следующие параметры:
        1. ```max_concurrent_iterations``` основан на количество рабочих узлов в кластере. 
        2. ```spark_context=sc``` основан на контекст spark по умолчанию. 
   + Или, если у вас есть более старые версии SDK, отменить его выбор из установленных библиотек кластера и переместить в корзину. Установите новую версию пакета SDK и перезапустите кластер. Если после этого возникнет проблема, отсоедините и заново присоедините кластер.

Если установка выполнена успешно, импортированную библиотеку должна выглядеть как одна из них:
   
Пакет SDK для Databricks **_без_** автоматической машинного обучения ![машины обучения пакет SDK Azure для Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

Пакет SDK для Databricks **WITH** автоматической машинного обучения ![пакет SDK с автоматической машинного обучения, которые установлены в Databricks](./media/how-to-configure-environment/automlonadb.jpg)

### <a name="start-exploring"></a>Начать просмотр

Попробуйте продукт:
+ Скачайте [файл архива записной книжки](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc) для пакета SDK для Azure Databricks и Azure Machine Learning и [импортировать файл архива](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) подключения к кластеру Databricks.  
  Хотя доступны многие примеры записных книжек, **только [эти примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) работают с Azure Databricks.**
  
+ Узнайте, как [создать конвейер с Databricks как вычислений обучения](how-to-create-your-first-pipeline.md).

## <a id="workspace"></a>Создание файла конфигурации рабочей области

Файл конфигурации рабочей области — это JSON-файл, который используется пакетом SDK для обмена данными с рабочей областью Службы машинного обучения Azure. В этого файла *config.json* следующий формат:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Этот JSON-файл должен находиться в структуре каталогов, которые содержат сценарии Python или приложения Jupyter Notebook. Он может находиться в том же каталоге, подкаталоге с именем *aml_config* или родительском каталоге.

Чтобы использовать этот файл в коде, выполните `ws=Workspace.from_config()`. Этот код загружает данные из файла и подключается к рабочей области.

Существует три способа создать файл конфигурации.

* **Выполните действия, описанные в [создать рабочую область службы машинного обучения Azure](setup-create-workspace.md#sdk)**: Файл *config.json* создается в вашей библиотеке Записных книжек Azure. Файл содержит сведения о конфигурации вашей рабочей области. Вы можете скачать или скопировать файл *config.json* в другие среды разработки.

* **Создайте файл вручную**. В этом случае используйте текстовый редактор. Для этого найдите значения, передаваемые в файл конфигурации, перейдя в рабочую область на [портале Azure](https://portal.azure.com). Скопируйте имя рабочей области, группу ресурсов и идентификатор подписки, чтобы использовать в файле конфигурации.

     ![Портал Azure](./media/how-to-configure-environment/configure.png)

* **Создайте файл программными средствами**. С помощью следующего фрагмента кода вы подключаетесь к рабочей области, указав идентификатор подписки, группу ресурсов и имя рабочей области. Затем конфигурация рабочей области будет сохранена в файле.

    ```python
    from azureml.core import Workspace

    subscription_id = '<subscription-id>'
    resource_group  = '<resource-group>'
    workspace_name  = '<workspace-name>'

    try:
        ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
        ws.write_config()
        print('Library configuration succeeded')
    except:
        print('Workspace not found')
    ```

    Этот код записывает файл конфигурации в файл *aml_config/config.json*.


## <a name="next-steps"></a>Дальнейшие действия

- [Обучение модели](tutorial-train-models-with-aml.md) в Машинном обучении Azure с помощью набора данных MNIST
- Просмотрите статью [What is the Azure Machine Learning SDK for Python?](https://aka.ms/aml-sdk) (Что такое пакет SDK для Машинного обучения Azure для Python?)
- Узнайте о [пакете SDK подготовки данных Машинного обучения Azure для Python ](https://aka.ms/data-prep-sdk)
