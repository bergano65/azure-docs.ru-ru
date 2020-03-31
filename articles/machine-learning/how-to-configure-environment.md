---
title: Настройка среды разработки Python
titleSuffix: Azure Machine Learning
description: Научитесь настраивать среду разработки для машинного обучения Azure. Используйте среды Conda, создавайте файлы конфигурации и настраивайте свой собственный облачный ноутбук, ноутбуки Jupyter, Azure Databricks, IDEs, редакторы кодов и виртуальную машину Data Science.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.topic: conceptual
ms.date: 12/27/2019
ms.custom: seodec18
ms.openlocfilehash: a5f46f5af723e1245afbc6bca90d25ae9036d646
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79472432"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Настройка среды разработки для Машинного обучения Azure
[!INCLUDE [applies-to-skus](../../includes/aml-applies-to-basic-enterprise-sku.md)]

В этой статье вы узнаете, как настроить среду разработки для работы с Azure Machine Learning. Azure Machine Learning — это агностик платформы. Единственным неотъемлимым требованием для среды разработки является Python 3. Также рекомендуется изолированная среда, такая как Anaconda или Virtualenv.

В следующей таблице показана каждая среда разработки, описанная в этой статье, а также плюсы и минусы.

| Среда | Плюсы | Минусы |
| --- | --- | --- |
| [Облачный экземпляр вычислений машинного обучения Azure (предварительный просмотр)](#compute-instance) | Самый простой способ начать работу. Весь SDK уже установлен в вашем рабочем пространстве VM, а учебники для ноутбуков предварительно клонированы и готовы к запуску. | Отсутствие контроля над средой разработки и зависимостями. Дополнительные расходы, понесенные для Linux VM (VM может быть остановлен, если он не используется, чтобы избежать взимания платы). См. сведения о [ценах](https://azure.microsoft.com/pricing/details/virtual-machines/linux/). |
| [Локальная среда](#local) | Полный контроль над средой разработки и зависимостями. Запуск с любым инструментом сборки, окружающей среды или IDE по вашему выбору. | Это займет больше времени, чтобы начать работу. Необходимо пакеты SDK должны быть установлены, и среда должна быть установлена, если у вас еще нет. |
| [Лазурные databricks](#aml-databricks) | Идеально подходит для запуска крупномасштабных интенсивных рабочих процессов машинного обучения на масштабируемой платформе Apache Spark. | Излишество для экспериментального машинного обучения или небольших экспериментов и рабочих процессов. Дополнительные расходы, понесенные для Azure Databricks. См. сведения о [ценах](https://azure.microsoft.com/pricing/details/databricks/). |
| [Виртуальная машина Data Science (DSVM)](#dsvm) | Как и в случае облачных вычислений (Python и SDK предустановлены), но с дополнительными популярными данными науки и машинного обучения инструменты предварительно установлены. Легко масштабировать и сочетать с другими пользовательскими инструментами и рабочими процессами. | Более медленное начало работы по сравнению с облачным экземпляром вычислений. |

В этой статье также содержатся дополнительные советы по использованию следующих инструментов:

* [Jupyter ноутбуки](#jupyter): Если вы уже используете ноутбук Jupyter, SDK имеет некоторые дополнительные услуги, которые вы должны установить.

* [Визуальный студийный код:](#vscode)Если вы используете Visual Studio Code, [расширение Машинного обучения Azure](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) включает в себя обширную языковую поддержку Python, а также функции, чтобы сделать работу с Azure Machine Learning гораздо более удобной и продуктивной.

## <a name="prerequisites"></a>Предварительные требования

Рабочая область машинного обучения Azure. Для создания рабочего пространства [см.](how-to-manage-workspace.md) Рабочее пространство — это все, что вам нужно для начала работы с помощью [собственного облачного ноутбука,](#compute-instance) [DSVM](#dsvm)или [Azure Databricks.](#aml-databricks)

Для установки среды SDK для [локального компьютера,](#local) [сервера Jupyter Notebook](#jupyter) или [Visual Studio Code](#vscode) также необходимо:

- Либо Менеджер пакетов [Anaconda](https://www.anaconda.com/download/) или [Miniconda.](https://conda.io/miniconda.html)

- В Linux или macOS вам понадобится оболочка bash.

    > [!TIP]
    > Если в Linux или macOS вы используете оболочку, отличающуюся от bash (например, zsh), при выполнении некоторых команд могут возникнуть ошибки. Чтобы решить эту проблему, используйте команду `bash`, чтобы запустить bash и выполните там нужные команды.

- В Windows вам понадобится командная строка по умолчанию или командная строка Anaconda (устанавливается вместе с Anaconda и Miniconda).

## <a name="your-own-cloud-based-compute-instance"></a><a id="compute-instance"></a>Собственный экземпляр облачных вычислений

Экземпляр вычислений Azure Machine Learning [compute (предварительный просмотр)](concept-compute-instance.md) — это безопасная облачная рабочая станция Azure, которая предоставляет ученым данных блокнот Jupyter, JupyterLab и полностью подготовленную среду ML.

Нет ничего, чтобы установить или настроить для экземпляра вычислений.  Создайте его в любое время из рабочего пространства Azure Machine Learning. Предоставьте только имя и укажите тип Azure VM. Попробуйте сейчас с помощью этого [учебника: Настройка среды и рабочего пространства.](tutorial-1st-experiment-sdk-setup.md)

Подробнее о [вычисляемых экземплярах.](concept-compute-instance.md)

Чтобы остановить вычисления, [остановите экземпляр вычислений.](tutorial-1st-experiment-sdk-train.md#clean-up-resources)

## <a name="data-science-virtual-machine"></a><a id="dsvm"></a>Виртуальная машина для обработки и анализа данных

DSVM — это настраиваемый образ виртуальной машины. Он предназначен для обработки и анализа данных, и на нем предварительно настроены:

  - пакеты TensorFlow, PyTorch, Scikit-learn, XGBoost и пакет SDK для Машинного обучения Azure;
  - популярные инструменты для обработки и анализа данных, в том числе изолированная среда Spark и Drill;
  - инструменты Azure, такие как интерфейс командной строки Azure, AzCopy и Обозреватель службы хранилища;
  - интегрированные среды разработки, например Visual Studio Code и PyCharm;
  - Сервер Jupyter Notebook.

Пакет SDK для Машинного обучения Azure работает с версией DSVM для Ubuntu или Windows. Но если вы также планируете использовать Виртуальную машину для обработки и анализа данных в качестве цели для вычислений, это возможно только на Ubuntu.

Чтобы использовать DSVM в качестве среды разработки:

1. Создайте DSVM в любой из следующих сред:

    * На портале Azure:

        * [Создание виртуальной машины для обработки и анализа данных на платформе Ubuntu](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/dsvm-ubuntu-intro)

        * [Создание виртуальной машины Windows для обработки и анализа данных](https://docs.microsoft.com/azure/machine-learning/data-science-virtual-machine/provision-vm)

    * Интерфейс командной строки Azure:

        > [!IMPORTANT]
        > * Чтобы использовать Azure CLI, нужно сначала войти в подписку Azure с помощью команды `az login`.
        >
        > * При использовании команд на этом шаге необходимо указать имя группы ресурсов, имя виртуальной машины, имя пользователя и пароль.

        * Чтобы создать DSVM на платформе Ubuntu, воспользуйтесь приведенной ниже командой.

            ```azurecli-interactive
            # create a Ubuntu DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            # If you need to create a new resource group use: "az group create --name YOUR-RESOURCE-GROUP-NAME --location YOUR-REGION (For example: westus2)"
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:linux-data-science-vm-ubuntu:linuxdsvmubuntu:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --generate-ssh-keys --authentication-type password
            ```

        * Чтобы создать DSVM на платформе Windows, воспользуйтесь приведенной ниже командой.

            ```azurecli-interactive
            # create a Windows Server 2016 DSVM in your resource group
            # note you need to be at least a contributor to the resource group in order to execute this command successfully
            az vm create --resource-group YOUR-RESOURCE-GROUP-NAME --name YOUR-VM-NAME --image microsoft-dsvm:dsvm-windows:server-2016:latest --admin-username YOUR-USERNAME --admin-password YOUR-PASSWORD --authentication-type password
            ```

2. Пакет SDK для Машинного обучения Azure уже установлен в DSVM. Чтобы использовать среду Conda, которая содержит пакет SDK, используйте одну из следующих команд:

    * Виртуальная машина для обработки и анализа данных под управлением Ubuntu:

        ```bash
        conda activate py36
        ```

    * Виртуальная машина для обработки и анализа данных под управлением Windows:

        ```bash
        conda activate AzureML
        ```

1. Чтобы убедиться, у вас есть доступ к пакету SDK и проверить версию, используйте следующий код Python:

    ```python
    import azureml.core
    print(azureml.core.VERSION)
    ```

1. Чтобы настроить DSVM для использования рабочего пространства Azure Machine Learning, [см.](#workspace)

Дополнительные сведения о Виртуальных машинах для обработки и анализа данных см. [здесь](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="local-computer"></a><a id="local"></a>Локальный компьютер

При использовании локального компьютера (который также может быть удаленной виртуальной машиной) создайте среду Anaconda и установите SDK. Ниже приведен пример:

1. Скачать и установить [Anaconda](https://www.anaconda.com/distribution/#download-section) (Python 3.7 версия), если у вас еще нет.

1. Откройте запрос Anaconda и создайте среду со следующими командами:

    Выполнить следующую команду для создания среды.

    ```bash
    conda create -n myenv python=3.6.5
    ```

    Затем активируйте окружающую среду.

    ```bash
    conda activate myenv
    ```

    Этот пример создает среду с помощью python 3.6.5, но могут быть выбраны любые конкретные диверсии. Совместимость SDK не может быть гарантирована с некоторыми основными версиями (рекомендуется 3,5 евро), и рекомендуется попробовать другую версию/подрыв в среде Anaconda, если вы столкнетесь с ошибками. Скачивание компонентов и пакетов и последующее создание среды займет несколько минут.

1. Запустите следующие команды в новой среде, чтобы включить в среду ядро IPython. Это обеспечит ожидаемое поведение импорта ядра и упаковки при работе с ноутбуками Jupyter в средах Anaconda:

    ```bash
    conda install notebook ipykernel
    ```

    Затем запустите следующую команду для создания ядра:

    ```bash
    ipython kernel install --user --name myenv --display-name "Python (myenv)"
    ```

1. Используйте следующие команды для установки пакетов:

    Эта команда устанавливает базовый Azure Machine Learning `automl` SDK с ноутбуком и дополнительными средствами. Дополнительные `automl` большой установки, и могут быть удалены из скобки, если вы не собираетесь запускать автоматизированные эксперименты машинного обучения. Дополнительнотакжее `automl` также включает в себя Azure Machine Learning Data Prep SDK по умолчанию в качестве зависимости.

    ```bash
    pip install azureml-sdk[notebooks,automl]
    ```

   > [!NOTE]
   > * Если появится сообщение о том, что не удается удалить PyYAML, попробуйте использовать другую команду:
   >
   >   `pip install --upgrade azureml-sdk[notebooks,automl] --ignore-installed PyYAML`
   >
   > * Начиная с версии macOS Catalina, в качестве стандартной оболочки входа и интерактивной оболочки используется zsh (Z shell). В zsh выполните следующую команду, в которой используется escape-последовательность с символом "\\" (обратная косая черта).
   >
   >   `pip install --upgrade azureml-sdk\[notebooks,automl\]`

   Установка SDK займет несколько минут. Для получения дополнительной информации о вариантах [установки,](https://docs.microsoft.com/python/api/overview/azure/ml/install?view=azure-ml-py)см.

1. Установите другие пакеты для экспериментов по машинного обучения.

    Используйте одну из следующих команд и * \<замените новый пакет>* пакетом, который вы хотите установить. Установка пакетов `conda install` через требует, чтобы пакет был частью текущих каналов (новые каналы могут быть добавлены в облако Anaconda).

    ```bash
    conda install <new package>
    ```

    Кроме того, вы можете `pip`установить пакеты через .

    ```bash
    pip install <new package>
    ```

### <a name="jupyter-notebooks"></a><a id="jupyter"></a>Записные книжки Jupyter

Приложения Jupyter Notebook предоставляются компанией [Project Jupyter](https://jupyter.org/). Это решение предназначено для реализации интерактивного процесса кодирования, в котором создаются документы с динамическим кодом, описательным текстом и графикой. Приложения Jupyter Notebook также позволяют использовать результаты вашей работы совместно с другими пользователями, так как вы можете сохранять выходные данные разделов кода в документе. Jupyter Notebook можно устанавливать на различных платформах.

Процедура в [локальном компьютерном](#local) разделе устанавливает необходимые компоненты для запуска ноутбуков Jupyter в среде Anaconda.

Для включения этих компонентов в среду Jupyter Notebook:

1. Откройте подсказку Anaconda и активируйте среду.

    ```bash
    conda activate myenv
    ```

1. [Клоните репозиторий GitHub](https://aka.ms/aml-notebooks) для набора образцов тетрадей.

    ```bash
    git clone https://github.com/Azure/MachineLearningNotebooks.git
    ```

1. Запустите сервер Jupyter Notebook со следующей командой:

    ```bash
    jupyter notebook
    ```

1. Чтобы убедиться, что Jupyter Notebook может использовать SDK, создайте **новый** блокнот, выберите **Python 3** в качестве ядра, а затем запустите следующую команду в ячейке ноутбука:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

1. Если вы столкнулись с проблемами `ModuleNotFoundError`импорта модулей и получили, убедитесь, что ядро Jupyter подключено к правильному пути для вашей среды, запустив следующий код в ячейке ноутбука.

    ```python
    import sys
    sys.path
    ```

1. Чтобы настроить записную книжку Jupyter, чтобы использовать рабочее пространство Azure Machine Learning, перейдите в раздел [«Создание конфигурации рабочего пространства».](#workspace)

### <a name="visual-studio-code"></a><a id="vscode"></a>Visual Studio Code

Visual Studio Code является очень популярным редактором кроссплатформенного кода, который поддерживает обширный набор языков программирования и инструментов через расширения, доступные [на рынке Visual Studio.](https://marketplace.visualstudio.com/vscode) [Расширение Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai) устанавливает расширение [Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python) для кодирования во всех типах сред Python (виртуальных, Anaconda и т.д.). Кроме того, он предоставляет удобные функции для работы с ресурсами Машинного обучения Azure и запуска экспериментов по машинному обучению Azure, не выходя из Visual Studio Code.

Использование Visual Studio Code для разработки:

1. Установите расширение машинного обучения Azure [Azure Machine Learning](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai)для Visual Studio Code, см.

    Дополнительные сведения см. в статье [Приступая к работе с Azure Machine Learning for Visual Studio Code](tutorial-setup-vscode-extension.md).

1. Узнайте, как использовать Visual Studio Code для любого типа разработки Python, [см.](https://code.visualstudio.com/docs/python/python-tutorial)

    - Для выбора среды SDK Python, содержащей SDK, откройте VS Code, а затем выберите Ctrl-Shift-P (Linux и Windows) или Command-Shift-P (Mac).
        - Открывается __командная палитра.__

    - Введите __Python: Выберите переводчика,__ а затем выберите соответствующую среду

1. Чтобы проверить, что можно использовать SDK, создайте новый файл Python (.py), содержащий следующий код:

    ```python
    #%%
    import azureml.core
    azureml.core.VERSION
    ```
    Запустите этот код, нажав на CodeLens "Бегущий ячейки" или просто нажмите сдвиг-ввод.
<a name="aml-databricks"></a>

## <a name="azure-databricks"></a>Azure Databricks
Azure Databricks — среда, основанная на Apache Spark в облаке Azure. Он обеспечивает совместную среду на основе ноутбуков с процессором или графическим компьютерным кластером на основе графического процессора.

Как Azure Databricks работает с машинным обучением Azure:
+ Вы можете обучить модель с помощью Spark MLlib и развернуть модель в ACI/AKS из Azure Databricks.
+ Возможности [автоматизированного машинного обучения](concept-automated-ml.md) можно также использовать в специальном Azure ML SDK с Azure Databricks.
+ Вы можете использовать Azure Databricks в качестве вычислительной цели из [конвейера машинного обучения Azure.](concept-ml-pipelines.md)

### <a name="set-up-your-databricks-cluster"></a>Настройка кластера Databricks

Создайте [кластер Databricks.](https://docs.microsoft.com/azure/azure-databricks/quickstart-create-databricks-workspace-portal) Некоторые настройки применяются только при установке SDK для автоматизированного машинного обучения на Databricks.
**Создание кластера занимает несколько минут.**

Используйте следующие параметры.

| Параметр |Применяется к| Значение |
|----|---|---|
| Имя кластера |always| yourclustername |
| Среда выполнения Databricks |always|Не ML Runtime 6.0 (скала 2.11, искра 2.4.3) |
| Версия Python |always| 3 |
| Рабочие роли |always| 2 или больше |
| Типы виртуальных машин узла рабочей роли <br>(определяет макс и одновременных итераций) |Автоматическое Машинное обучение<br>только| Предпочитается виртуальная машина, оптимизированная для операций в памяти |
| Включение автомасштабирования |Автоматическое Машинное обучение<br>только| Снять пометку |

Дождитесь запуска кластера, прежде чем продолжать.

### <a name="install-the-correct-sdk-into-a-databricks-library"></a>Установите правильный SDK в библиотеку Databricks
После запуска кластера [создайте библиотеку](https://docs.databricks.com/user-guide/libraries.html#create-a-library) для присоединения соответствующего пакета Azure Machine Learning SDK к кластеру.

1. Нажмите правой кнопкой мыши текущую папку Workspace, где требуется хранить библиотеку. Выберите **Создать** > **библиотеку**.

1. Выберите **только один** вариант (никакая другая установка SDK не поддерживается)

   |Дополнительные&nbsp;услуги пакета&nbsp;SDK|Источник|Имя&nbsp;ПиПи&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;|
   |----|---|---|
   |Для Databricks| Отправка Python Egg или PyPI | azureml-sdk[databricks]|
   |Для Databricks -с-<br> автоматизированные возможности ML| Отправка Python Egg или PyPI | лазурмл-сдк -автомл|

   > [!Warning]
   > Никакие другие дополнительные услуги SDK не могут быть установлены. Выберите только один из предшествующих вариантов «databricks» или «автомл».

   * Не выбирайте **присоединение автоматически ко всем кластерам.**
   * Выберите **Прикрепите** рядом с именем кластера.

1. Мониторинг ошибок до изменения **статуса,** который может занять несколько минут.  Если этот шаг не удается:

   Попробуйте перезапустить кластер:
   1. В левой области щелкните **Кластеры**.
   1. Выберите имя кластера в таблице.
   1. На вкладке **Библиотеки** щелкните **Перезапустить**.

   Также рассмотрим:
   + В конфигурации AutoML при использовании Azure Databricks добавляются следующие параметры:
       1. ```max_concurrent_iterations```основано на количестве рабочих узлов в кластере.
        2. ```spark_context=sc```основано на контексте искры по умолчанию.
   + Или, если у вас есть старая версия SDK, отойдите ее из установленных libs кластера и перейти к мусору. Установите новую версию пакета SDK и перезапустите кластер. Если возникла проблема после перезагрузки, отсоедините и прикрепите кластер.

Если установка была успешной, импортная библиотека должна выглядеть как одна из них:

SDK для Databricks **_без_** автоматизированного машинного обучения ![Azure Machine Learning SDK для Databricks](./media/how-to-configure-environment/amlsdk-withoutautoml.jpg)

SDK для Databricks **With** автоматизированного машинного обучения ![SDK с автоматизированным машинным обучением, установленным на Databricks](./media/how-to-configure-environment/automlonadb.png)

### <a name="start-exploring"></a>Начало знакомства

Попробуйте продукт:
+ Хотя многие образцы ноутбуков доступны, **только [эти образцы ноутбуков](https://github.com/Azure/MachineLearningNotebooks/blob/master/how-to-use-azureml/azure-databricks) работают с Azure Databricks.**

+ Импортируйте эти образцы непосредственно из рабочей области. См. ![ниже: Выберите панель импорта импорта](./media/how-to-configure-environment/azure-db-screenshot.png)
![](./media/how-to-configure-environment/azure-db-import.png)

+ Узнайте, как [создать конвейер с Databricks в качестве обучаемого вычисления.](how-to-create-your-first-pipeline.md)

## <a name="create-a-workspace-configuration-file"></a><a id="workspace"></a>Создание файла конфигурации рабочей области

Файл конфигурации рабочего пространства — это файл JSON, который рассказывает SDK о том, как общаться с рабочим пространством Azure Machine Learning. В этого файла *config.json* следующий формат:

```json
{
    "subscription_id": "<subscription-id>",
    "resource_group": "<resource-group>",
    "workspace_name": "<workspace-name>"
}
```

Этот JSON-файл должен находиться в структуре каталогов, которые содержат сценарии Python или приложения Jupyter Notebook. Он может находиться в том же каталоге, подкаталоге с именем *.azureml* или родительском каталоге.

Чтобы использовать этот файл в коде, выполните `ws=Workspace.from_config()`. Этот код загружает данные из файла и подключается к рабочей области.

Существует три способа создать файл конфигурации.

* **Используйте [ws.write_config](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py)**: чтобы написать файл *config.json.* Файл содержит сведения о конфигурации вашей рабочей области. Вы можете скачать или скопировать файл *config.json* в другие среды разработки.

* **Загрузите файл**: На [портале Azure](https://ms.portal.azure.com), выберите **Скачать config.json** из раздела **Обзор** вашего рабочего пространства.

     ![Портал Azure](./media/how-to-configure-environment/configure.png)

* **Создайте файл программно:** В следующем фрагменте кода вы подключаетесь к рабочему пространству, предоставляя идентификатор подписки, группу ресурсов и имя рабочего пространства. Затем конфигурация рабочей области будет сохранена в файле.

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

    Этот код записывает файл конфигурации в файл *.azureml/config.json.*

## <a name="next-steps"></a>Дальнейшие действия

- [Обучить модели](tutorial-train-models-with-aml.md) по изучению машин Azure с помощью набора данных MNIST
- Просмотрите статью [What is the Azure Machine Learning SDK for Python?](https://docs.microsoft.com/python/api/overview/azure/ml/intro?view=azure-ml-py) (Что такое пакет SDK для Машинного обучения Azure для Python?)
