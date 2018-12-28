---
title: Настройка среды разработки Python
titleSuffix: Azure Machine Learning service
description: Узнайте, как настроить среду разработки при работе со службой машинного обучения Azure. Изучив этот документ, вы научитесь использовать среды Conda, создавать файлы конфигурации и настраивать Jupyter Notebook, записные книжки Azure, интегрированные среды разработки, редакторы кода и виртуальную машину для обработки и анализа данных.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: 59f847dc38cddfd9185cfd169cf1ef34c744f8f7
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53192640"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Настройка среды разработки для Машинного обучения Azure

В этом документе описано, как настроить среду разработки для работы со Службой машинного обучения Azure. Служба машинного обучения Azure не зависит от платформы. Единственное требование для среды разработки — __Python 3__, __Conda__ (для изолированных сред) и файл конфигурации, который содержит сведения о рабочей области Машинного обучения Azure.

В этом документе описаны следующие среды и средства:

* [Записные книжки Azure](#aznotebooks). Служба Jupyter Notebook, размещенная в облаке Azure. Это __самый простой__ способ начать работу, так как пакет SDK для Машинного обучения Azure уже установлен.
* [Виртуальная машина для обработки и анализа данных](#dsvm). __Предварительно настроенная среда для разработки и экспериментов__ в облаке Azure, которая __предназначена для обработки и анализа данных__ и может быть развернута как на экземплярах виртуальных машин на основе ЦП, так и на экземплярах виртуальных машин на основе GPU. Установленные Python 3, Conda, приложения Jupyter Notebook и пакет SDK для Машинного обучения Azure. Виртуальная машина предоставляется с популярными платформами машинного или глубокого обучения, а также инструментами и редакторами для разработки решений машинного обучения. Это, вероятно, __самая многофункциональная__ среда разработки для машинного обучения на платформе Azure.
* [Jupyter Notebook](#jupyter). Если вы уже используете Jupyter Notebook, вам нужно установить некоторые вспомогательные компоненты, которые содержит пакет SDK.
* [Visual Studio Code](#vscode). Если вы используете Visual Studio Code, то вы можете установить некоторые полезные расширения.
* [Azure Databricks](#aml-databricks). Популярная платформа для аналитики данных на основе Apache Spark. Узнайте, как установить пакет SDK для Машинного обучения Azure в кластере, чтобы получить возможность развертывать модели.

Если у вас уже есть среда Python 3, или вам нужно выполнить основные шаги для установки пакета SDK, см. инструкции, предназначенные для [локального компьютера](#local).

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область службы машинного обучения Azure. Выполните инструкции из руководства по [началу работы со Службой машинного обучения Azure](quickstart-get-started.md), чтобы создать рабочую область.

- Диспетчер пакетов [Continuum Anaconda](https://www.anaconda.com/download/) или [Miniconda](https://conda.io/miniconda.html).

    > [!IMPORTANT]
    > Anaconda и Miniconda не являются обязательными компонентами при работе с Записными книжками Azure.

- В Linux или Mac OS вам понадобится оболочка bash.

    > [!TIP]
    > Если в Linux или Mac OS вы используете оболочку, отличающуюся от bash (например, zsh), при выполнении некоторых команд могут возникнуть ошибки. Чтобы решить эту проблему, используйте команду `bash`, чтобы запустить bash и выполните там нужные команды.

- В Windows вам понадобится командная строка по умолчанию или командная строка Anaconda (устанавливается вместе с Anaconda и Miniconda).

## <a id="anotebooks"></a>Записные книжки Azure

[Записные книжки Azure](https://notebooks.azure.com) (предварительная версия) — это интерактивная среда разработки в облаке Azure. Это __самый простой__ способ начать разработку с использованием Машинного обучения Azure.

* Пакет SDK для Машинного обучения Azure __уже установлен__.
* Создав рабочую область Службы машинного обучения Azure на портале Azure, можно нажать кнопку, чтобы автоматически настроить среду Записных книжек Azure для работы с рабочей областью.

Чтобы начать разработку с помощью Записных книжек Azure, см. руководство по [началу работы со Службой машинного обучения Azure](quickstart-get-started.md).

## <a id="dsvm"></a>Виртуальная машина для обработки и анализа данных

Виртуальная машина для обработки и анализа данных (DSVM) — это настраиваемый образ виртуальной машины, **предназначенный для задач обработки и анализа данных**. Он содержит следующие предварительно настроенные компоненты.

  - Пакеты Tensorflow, Pytorch, scikit-learn, Xgboost и пакет SDK для Машинного обучения Azure.
  - Популярные инструменты для обработки и анализа данных, в том числе изолированная среда Spark, Drill.
  - Инструменты Azure, такие как интерфейс командной строки, AzCopy и обозреватель хранилища.
  - Интегрированные среды разработки, например Visual Studio Code, PyCharm и RStudio.
  - Сервер Jupyter Notebook. 

Пакет SDK для Машинного обучения Azure работает с версией DSVM для Ubuntu или Windows. Чтобы использовать DSVM в качестве среды разработки, сделайте следующее.

1. Чтобы создать DSVM, воспользуйтесь одним из приведенных ниже способов.

    * С помощью портала Azure.

        * [Создание Виртуальной машины для обработки и анализа данных на платформе __Ubuntu__](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Создание Виртуальной машины для обработки и анализа данных на платформе __Windows__](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * С помощью Azure CLI.

        > [!IMPORTANT]
        > Чтобы использовать Azure CLI, нужно сначала войти в подписку Azure с помощью команды `az login`.
        >
        > При использовании команд на этом шаге необходимо указать имя группы ресурсов, имя виртуальной машины, имя пользователя и пароль.

        * Чтобы создать DSVM на платформе __Ubuntu__, воспользуйтесь приведенной ниже командой.

            ```azurecli
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Чтобы создать DSVM на платформе __Windows__, воспользуйтесь приведенной ниже командой.

            ```azurecli
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```    

2. Пакет SDK для Машинного обучения Azure **уже установлен** в DSVM. Чтобы использовать среду Conda, которая содержит пакет SDK, используйте одну из следующих команд:

    * В DSVM __Ubuntu__ используйте эту команду:

        ```shell
        conda activate py36
        ```

    * В DSVM __Windows__ используйте эту команду:

        ```shell
        conda activate AzureML
        ```

1. Чтобы убедиться, у вас есть доступ к пакету SDK и проверить версию, используйте следующий код Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. См. дополнительные сведения о том, [как настроить](#workspace) DSVM для работы с вашей рабочей областью Службы машинного обучения Azure.

Дополнительные сведения о Виртуальных машинах для обработки и анализа данных см. в [этой статье](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a id="local"></a>Локальный компьютер

При использовании локального компьютера (который также может быть удаленной виртуальной машине) следуйте инструкциям ниже, чтобы создать среду Conda и установить пакет SDK:

1. Откройте окно командной строки или оболочку.

1. Создайте среду conda, выполнив следующие команды:

    ```shell
    # create a new conda environment with Python 3.6, numpy, and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # On Mac OS run
    source activate myenv
    ```

    Создание среды может занять несколько минут, если потребуется скачать Python 3.6 и (или) другие компоненты.

1. Установите пакет SDK для Машинного обучения Azure и дополнительные компоненты для записных книжек, а также пакет SDK для подготовки данных, выполнив следующую команду:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Если появится сообщение о том, что не удается удалить `PyYAML`, попробуйте использовать другую команду:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Установка пакета SDK может занять несколько минут.

1. Установите пакеты для экспериментирования в машинном обучении. Используйте следующую команду, заменив в ней `<new package>` именем пакета, который хотите установить:

    ```shell
    conda install <new package>
    ```

1. Чтобы убедиться, что пакет SDK установлен, выполните следующий код Python:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Приложения Jupyter Notebook

Приложения Jupyter Notebook предоставляются компанией [Project Jupyter](https://jupyter.org/). Это решение предназначено для реализации интерактивного процесса кодирования, в котором создаются документы с динамическим кодом, описательным текстом и графикой. Приложения Jupyter Notebook также позволяют совместно использовать результаты вашей работы с другими пользователями, так как вы можете сохранять выходные данные разделы кода в документе. Jupyter Notebook можно устанавливать на различных платформах.

Действия, описанные в инструкциях по работе с [локальным компьютером](#local), позволяют установить дополнительные компоненты для работы с приложениями Jupyter Notebook. Чтобы включить эти компоненты в вашей среде Jupyter Notebook, следуйте инструкциям ниже:

1. Откройте окно командной строки или оболочку.

1. Чтобы установить Conda с поддержкой сервера Jupyter Notebook, используйте следующую команду.

    ```shell
    # install Jupyter
    conda install nb_conda
    ```

1. Откройте Jupyter Notebook, выполнив следующую команду:

    ```shell
    jupyter notebook
    ```

1. Чтобы убедиться в том, что в Jupyter Notebook включена поддержка пакета SDK, откройте новую записную книжку и выберите myenv в качестве ядра. Затем выполните приведенную ниже команду в ячейке записной книжки:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. См. дополнительные сведения о том, как [настроить Jupyter Notebook для работы с вашей рабочей областью Службы машинного обучения Azure](#workspace).

### <a id="vscode"></a>Visual Studio Code

Visual Studio Code — это кроссплатформенный редактор кода. Для его использования требуется локальная установка Python 3 и Conda для поддержки Python, но он также предоставляет дополнительные средства для работы с искусственным интеллектом. Кроме того, этот редактор предоставляет поддержку для выбора среды Conda в редакторе кода.

Чтобы использовать Visual Studio Code для разработки, сделайте следующее:

1. Чтобы узнать, как использовать Visual Studio Code для разработки с помощью Python, см. руководство по [началу работы с Python в VSCode](https://code.visualstudio.com/docs/python/python-tutorial).

1. Чтобы выбрать среду Conda, откройте VS Code и используйте сочетание клавиш __CTRL+SHIFT+P__ для Windows или __COMMAND+SHIFT+P__ для Mac, чтобы открыть __палитру команд__. Введите __Python: Select Interpreter__ (Python: выбор интерпретатора) и выберите среду Conda.

1. Чтобы убедиться, что вы можете использовать пакет SDK, создайте новый файл Python (.py), содержащий следующий код. Затем запустите этот файл:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Чтобы установить расширение Azure Machine Learning for Visual Studio Code, перейдите на страницу [Azure Machine Learning for Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

    Дополнительные сведения см. в разделе [Приступая к работе с Azure Machine Learning for Visual Studio Code](how-to-vscode-tools.md).

<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks

Можно использовать настраиваемую версию пакета SDK Машинного обучения Azure для Azure Databricks, чтобы создать комплексное настраиваемое решение машинного обучения. Или обучить модель в Databricks и использовать [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) для ее развертывания.

Чтобы подготовить кластер Databricks и получить примеры записных книжек, сделайте следующее.

1. Создайте [кластер Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) с версией среды выполнения Databricks 4.x (предпочтительно с высокой степенью параллелизма) и **Python 3**. 

1. Создайте библиотеку для [установки и подключения](https://docs.databricks.com/user-guide/libraries.html#create-a-library) к кластеру пакета SDK Машинного обучения Azure для пакета Python PyPi `azureml-sdk[databricks]`. Когда все будет готово, вы увидите, что эта библиотека подключена, как показано на рисунке. Обратите внимание на эти [распространенные проблемы с Databricks](resource-known-issues.md#databricks).

   ![Пакет SDK установлен в Databricks ](./media/how-to-azure-machine-learning-on-databricks/sdk-installed-on-databricks.jpg)

   Если этот шаг завершится ошибкой, перезапустите кластер.
   1. В левой области щелкните `Clusters`. Выберите имя кластера в таблице. 
   1. На вкладке `Libraries` выберите `Restart`.

1. Скачайте [файл архива записных книжек для Azure Databricks и пакета SDK для Машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/databricks/Databricks_AMLSDK_github.dbc).

   >[!Warning]
   > Предоставляется много примеров записных книжек для использования со Службой машинного обучения Azure. Только эти примеры записных книжек подходят для Azure Databricks: https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks

1.  [Импортируйте этот файл архива](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) в кластер Databricks и приступите к изучению, как [описывается здесь](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


## <a id="workspace"></a>Создание файла конфигурации рабочей области

Файл конфигурации рабочей области — это документ JSON, который используется пакетом SDK для обмена данными с рабочей областью Службы машинного обучения Azure. Этот файл с именем `config.json` имеет следующий формат:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Этот файл должен находиться в структуре каталогов, которые содержат скрипты Python или приложения Jupyter Notebook. Он может находиться в том же каталоге, подкаталоге с именем `aml_config` или родительском каталоге.

Чтобы использовать этот файл в коде, выполните `ws=Workspace.from_config()`. Этот код загружает данные из файла и подключается к рабочей области.

Есть три способа создать файл конфигурации:

* Если вы следуете инструкциям по [использованию Машинного обучения Azure](quickstart-get-started.md), файл `config.json` создается в библиотеке Записных книжек Azure. Файл содержит сведения о конфигурации вашей рабочей области. Вы можете скачать или скопировать файл `config.json` в другие среды разработки.

* Вы можете **вручную создать файл** в текстовом редакторе. Для этого найдите значения, передаваемые в файл конфигурации, перейдя в рабочую область на [портале Azure](https://portal.azure.com). Скопируйте __имя рабочей области__, __группу ресурсов__ и __ИД подписки__ для использования в файле конфигурации.
        ![портал Azure](./media/how-to-configure-environment/configure.png)

* Вы можете **создать файл программными средствами**. В следующем фрагменте кода показано, как подключиться к рабочей области, указав идентификатор подписки, группу ресурсов и имя рабочей области. Затем конфигурация рабочей области будет сохранена в файле:

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

    Этот код записывает файл конфигурации в файл `aml_config/config.json`.

## <a name="next-steps"></a>Дополнительная информация

- [Обучение модели в машинном обучении Azure с помощью набора данных MNIST](tutorial-train-models-with-aml.md)
- [Пакет SDK службы "Машинное обучение Azure" для Python](https://aka.ms/aml-sdk)
- [Пакет SDK azureml-dataprep службы "Машинное обучение Azure"](https://aka.ms/data-prep-sdk)
