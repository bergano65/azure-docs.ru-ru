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
manager: cgronlun
ms.topic: conceptual
ms.date: 01/18/2019
ms.custom: seodec18
ms.openlocfilehash: 136a83c586b2f797269beff3cdd0afb9973cb7c8
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/18/2019
ms.locfileid: "56340524"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Настройка среды разработки для Машинного обучения Azure

В этой статье описано, как настроить среду разработки для работы со Службой машинного обучения Azure. Служба машинного обучения не зависит от платформы. 

Единственное требование для среды разработки — Python 3, Conda (для изолированных сред) и файл конфигурации, который содержит сведения о рабочей области Машинного обучения Azure.

В этой статье описаны следующие среды и средства.

* Записные книжки Azure. Служба Jupyter Notebook, размещенная в облаке Azure. Это самый простой способ начать работу, так как пакет SDK для Машинного обучения Azure уже установлен.

* [Виртуальная машина для обработки и анализа данных (DSVM)](#dsvm). Предварительно настроенная среда для разработки и экспериментов в облаке Azure, которая предназначена для обработки и анализа данных и может быть развернута как на экземплярах виртуальных машин на основе ЦП, так и на экземплярах виртуальных машин на основе GPU. Установленные Python 3, Conda, приложения Jupyter Notebook и пакет SDK для Машинного обучения Azure. Виртуальная машина предоставляется с популярными платформами машинного или глубокого обучения, а также с инструментами и редакторами для разработки решений машинного обучения. Это, вероятно, самая многофункциональная среда разработки для машинного обучения на платформе Azure.

* [Jupyter Notebook](#jupyter). Если вы уже используете Jupyter Notebook, вам нужно установить некоторые вспомогательные компоненты, которые содержит пакет SDK.

* [Visual Studio Code](#vscode). Если вы используете Visual Studio Code, то вы можете установить некоторые полезные расширения.

* [Azure Databricks](#aml-databricks). Популярная платформа для аналитики данных на основе Apache Spark. Узнайте, как установить пакет SDK для Машинного обучения Azure в кластере, чтобы получить возможность развертывать модели.

Если у вас уже есть среда Python 3, или вам нужно выполнить основные шаги для установки пакета SDK, см. инструкции, предназначенные для [локального компьютера](#local).

## <a name="prerequisites"></a>Предварительные требования

- Рабочая область службы машинного обучения Azure. Чтобы создать рабочую область, обратитесь к статье [Краткое руководство. Начало работы со Службой машинного обучения Azure с помощью портала Azure](quickstart-get-started.md).

- Диспетчер пакетов [Continuum Anaconda](https://www.anaconda.com/download/) или [Miniconda](https://conda.io/miniconda.html).

    > [!IMPORTANT]
    > Anaconda и Miniconda не являются обязательными компонентами при работе с Записными книжками Azure.

- В Linux или macOS вам понадобится оболочка bash.

    > [!TIP]
    > Если в Linux или macOS вы используете оболочку, отличающуюся от bash (например, zsh), при выполнении некоторых команд могут возникнуть ошибки. Чтобы решить эту проблему, используйте команду `bash`, чтобы запустить bash и выполните там нужные команды.

- В Windows вам понадобится командная строка по умолчанию или командная строка Anaconda (устанавливается вместе с Anaconda и Miniconda).

## <a id="aznotebooks"></a>Записные книжки Azure

[Записные книжки Azure](https://notebooks.azure.com) (предварительная версия) — это интерактивная среда разработки в облаке Azure. Это самый простой способ начать разработку с использованием Машинного обучения Azure.

* Пакет SDK для Машинного обучения Azure уже установлен.
* Создав рабочую область Службы машинного обучения Azure на портале Azure, можно нажать кнопку, чтобы автоматически настроить среду Записных книжек Azure для работы с рабочей областью.

Чтобы начать разработку с помощью Записных книжек Azure, обратитесь к статье [Краткое руководство. Начало работы со Службой машинного обучения Azure с помощью портала Azure](quickstart-get-started.md).

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

        * [Создание Виртуальной машины для обработки и анализа данных на платформе Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

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

При использовании локального компьютера (который также может быть удаленной виртуальной машиной) следуйте инструкциям ниже, чтобы создать среду Conda и установить пакет SDK.

1. Откройте окно командной строки или оболочку.

1. Создайте среду Conda, выполнив следующие команды:

    ```shell
    # create a new Conda environment with Python 3.6, NumPy, and Cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the Conda environment
    conda activate myenv

    # On macOS run
    source activate myenv
    ```

    Создание среды может занять несколько минут, если потребуется скачать Python 3.6 и (или) другие компоненты.

1. Установите пакет SDK для Машинного обучения Azure и дополнительные компоненты для записных книжек, а также пакет SDK для подготовки данных, выполнив следующую команду:

     ```shell
    pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep
    ```

   > [!NOTE]
   > Если появится сообщение о том, что не удается удалить PyYAML, попробуйте использовать другую команду:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Установка пакета SDK может занять несколько минут.

1. Установите пакеты для экспериментирования в машинном обучении. Используйте следующую команду, заменив в ней *\<new package>* именем пакета, который хотите установить:

    ```shell
    conda install <new package>
    ```

1. Чтобы убедиться, что пакет SDK установлен, выполните следующий код Python:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

### <a id="jupyter"></a>Приложения Jupyter Notebook

Приложения Jupyter Notebook предоставляются компанией [Project Jupyter](https://jupyter.org/). Это решение предназначено для реализации интерактивного процесса кодирования, в котором создаются документы с динамическим кодом, описательным текстом и графикой. Приложения Jupyter Notebook также позволяют использовать результаты вашей работы совместно с другими пользователями, так как вы можете сохранять выходные данные разделов кода в документе. Jupyter Notebook можно устанавливать на различных платформах.

Процедура, описанная в инструкциях по работе с [локальным компьютером](#local), позволяет установить дополнительные компоненты для работы с приложениями Jupyter Notebook. Чтобы включить эти компоненты в вашей среде Jupyter Notebook, следуйте инструкциям, приведенным ниже.

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

1. Чтобы убедиться в том, что в Jupyter Notebook включена поддержка пакета SDK, откройте новую записную книжку и выберите **myenv** в качестве ядра, а затем выполните следующую команду в ячейке записной книжки.

    ```python
    import azureml.core
    azureml.core.VERSION
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

Можно использовать настраиваемую версию пакета SDK Машинного обучения Azure для Azure Databricks, чтобы создать комплексное настраиваемое решение машинного обучения. Или обучить модель в Databricks и использовать [Visual Studio Code](how-to-vscode-train-deploy.md#deploy-your-service-from-vs-code) для ее развертывания.

Чтобы подготовить кластер Databricks и получить примеры записных книжек, сделайте следующее.

1. Создайте [кластер Databricks](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) со следующими параметрами:

    | Параметр | Значение |
    |----|---|
    | Имя кластера | yourclustername |
    | Среда выполнения Databricks | Любая среда выполнения, не относящаяся к Машинному обучению (версии 4.x, 5.x) |
    | Версия Python | 3 |
    | Рабочие роли | 2 или больше |

    Используйте эти параметры, только если вы будете применять автоматизированное машинное обучение в Databricks:
    
    |   Параметр | Значение |
    |----|---|
    | Типы виртуальных машин узла рабочей роли | Предпочитается виртуальная машина, оптимизированная для операций в памяти |
    | Включение автомасштабирования | Снять пометку |
    
    Число рабочих узлов в кластере Databricks определяет максимальное количество параллельных итераций в параметрах автоматического Машинного обучения.  

    Создание кластера занимает несколько минут. Дождитесь запуска кластера, прежде чем продолжать.

1. Установите и подключите к кластеру пакет SDK для Машинного обучения Azure.  

    * [Создайте библиотеку](https://docs.databricks.com/user-guide/libraries.html#create-a-library) с одним из этих параметров (_выберите только один из этих вариантов_):
    
        * Чтобы установить пакет SDK для Машинного обучения Azure _без_ автоматической возможности машинного обучения:
            | Параметр | Значение |
            |----|---|
            |Источник | Отправка Python Egg или PyPI
            |Имя PyPi | azureml-sdk[databricks]
    
        * Чтобы установить пакет SDK для Машинного обучения Azure _с_ автоматическим машинным обучением:
            | Параметр | Значение |
            |----|---|
            |Источник | Отправка Python Egg или PyPI
            |Имя PyPi | azureml-sdk[automl_databricks]
    
    * Не выбирайте параметр **Attach automatically to all clusters** (Присоединиться автоматически ко всем кластерам)

    * Выберите параметр **Присоединиться** рядом с именем кластера

    * Убедитесь в отсутствии ошибок, пока состояние не изменится на **Присоединено**. Это может занять несколько минут.

    Если у вас есть более старая версия пакета SDK, отмените его выбор из установленных библиотек кластера и переместите в корзину. Установите новую версию пакета SDK и перезапустите кластер. Если после этого возникнет проблема, отсоедините и заново присоедините кластер.

    По завершении библиотека присоединится, что будет выглядеть так, как показано на следующем изображении. Обратите внимание на эти [распространенные проблемы с Databricks](resource-known-issues.md#databricks).

    * Если вы установили пакет SDK для Машинного обучения Azure _без_ автоматического машинного обучения ![пакет SDK без автоматического машинного обучения, установленный в Databricks ](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

    * Если вы установили пакет SDK для Машинного обучения Azure _с_ автоматическим машинным обучением ![пакет SDK с автоматическим машинным обучением, установленный в Databricks ](./media/how-to-configure-environment/automlonadb.jpg)

   Если этот шаг завершится ошибкой, перезапустите кластер следующим образом.

   a. В левой области щелкните **Кластеры**. 
   
   б) Выберите имя кластера в таблице. 

   c. На вкладке **Библиотеки** щелкните **Перезапустить**.

1. Скачайте [файл архива записных книжек для Azure Databricks и пакета SDK для Машинного обучения Azure](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks/Databricks_AMLSDK_1-4_6.dbc).

   >[!Warning]
   > Предоставляется много примеров записных книжек для использования со Службой машинного обучения Azure. Только [эти примеры записных книжек](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) подходят для Azure Databricks.

1.  [Импортируйте файл архива](https://docs.azuredatabricks.net/user-guide/notebooks/notebook-manage.html#import-an-archive) в кластер Databricks и приступите к изучению, как описывается [здесь](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks).


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

* **Следуйте указаниям в статье [Краткое руководство. Начало работы со Службой машинного обучения Azure с помощью портала Azure](quickstart-get-started.md)**. Файл *config.json* создается в вашей библиотеке Записных книжек Azure. Файл содержит сведения о конфигурации вашей рабочей области. Вы можете скачать или скопировать файл *config.json* в другие среды разработки.

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

## <a name="next-steps"></a>Дополнительная информация

- [Обучение модели](tutorial-train-models-with-aml.md) в Машинном обучении Azure с помощью набора данных MNIST
- Просмотрите статью [What is the Azure Machine Learning SDK for Python?](https://aka.ms/aml-sdk) (Что такое пакет SDK для Машинного обучения Azure для Python?)
- Узнайте о [пакете SDK подготовки данных Машинного обучения Azure для Python ](https://aka.ms/data-prep-sdk)
