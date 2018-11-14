---
title: Настройка среды разработки для машинного обучения Azure | Документы Майкрософт
description: Узнайте, как настроить среду разработки при работе со службой машинного обучения Azure. Изучив этот документ, вы научитесь использовать среды Conda, создавать файлы конфигурации и настраивать Jupyter Notebook, записные книжки Azure, интегрированные среды разработки, редакторы кода и виртуальную машину для обработки и анализа данных.
services: machine-learning
author: rastala
ms.author: roastala
ms.service: machine-learning
ms.component: core
ms.reviewer: larryfr
manager: cgronlun
ms.topic: conceptual
ms.date: 11/6/2018
ms.openlocfilehash: 8ce411e424d538a4a1f94300bfe5510658017f56
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51238333"
---
# <a name="configure-a-development-environment-for-azure-machine-learning"></a>Настройка среды разработки для Машинного обучения Azure

Из этой статьи вы узнаете, как настраивать среду разработки для работы со Службой машинного обучения Azure, в том числе выполнять следующие действия:

- Создание файла конфигурации, который связывает вашу среду с рабочей областью службы "Машинное обучение Azure".
- Настройка следующих сред разработки:
  - Jupyter Notebook на локальном компьютере;
  - Visual Studio Code.
  - пользовательский редактор кода.
- Настройка [виртуальной среды conda](https://conda.io/docs/user-guide/tasks/manage-environments.html) и ее использование для Машинного обучения Azure. Мы рекомендуем применить Continuum Anaconda, чтобы изолировать рабочую среду и избежать конфликтов зависимостей между пакетами.

## <a name="prerequisites"></a>Предварительные требования

- Настройте рабочую область Службы машинного обучения Azure. Выполните инструкции из статьи о [начале работы со Службой машинного обучения Azure](quickstart-get-started.md).
- Установите диспетчер пакетов [Continuum Anaconda](https://www.anaconda.com/download/) или [Miniconda](https://conda.io/miniconda.html).
- Если вы используете Visual Studio Code, добавьте к нему [расширение Python](https://code.visualstudio.com/docs/python/python-tutorial).

> [!NOTE]
> Представленные в этой статье команды оболочки можно протестировать в bash (в Linux и Mac OS) или в командной строке (в Windows).

## <a name="create-a-workspace-configuration-file"></a>Создание файла конфигурации рабочей области

Пакет SDK Машинного обучения Azure использует файл конфигурации рабочей области для связи с рабочей областью Службы машинного обучения Azure.

- Чтобы создать файл конфигурации, выполните инструкции в [кратком руководстве по Машинному обучению Azure](quickstart-get-started.md).
  - Описанный в нем процесс позволяет создать файл `config.json` в Записных книжках Azure. Файл содержит сведения о конфигурации вашей рабочей области.
  - Скачайте или скопируйте файл `config.json` в тот же каталог, где находятся скрипты и (или) записные книжки, ссылающиеся на него.

- Вы также можете создать файл вручную, выполнив следующие действия:

    1. Откройте рабочую область на [портале Azure](https://portal.azure.com). Копируйте __имя рабочей области__, __группу ресурсов__ и __ИД подписки__. Эти значения используются для создания файла конфигурации.
        ![портал Azure](./media/how-to-configure-environment/configure.png)

    1. Создайте файл с приведенным ниже кодом Python и запустите его в том же каталоге, где расположены скрипты или записные книжки, которые ссылаются на рабочую область.

        ```python
        from azureml.core import Workspace

        subscription_id ='<subscription-id>'
        resource_group ='<resource-group>'
        workspace_name = '<workspace-name>'

        try:
           ws = Workspace(subscription_id = subscription_id, resource_group = resource_group, workspace_name = workspace_name)
           ws.write_config()
           print('Library configuration succeeded')
        except:
           print('Workspace not found')
        ```
        Этот код позволяет создать файл `aml_config/config.json` со следующим содержимым:

        ```json
        {
        "subscription_id": "<subscription-id>",
        "resource_group": "<resource-group>",
        "workspace_name": "<workspace-name>"
        }
        ```
        Вы можете скопировать каталог `aml_config` либо только файл `config.json` в другой каталог, который ссылается на рабочую область.

       > [!NOTE]
       > Другие скрипты и (или) записные книжки, расположенные в том же каталоге или ниже в дереве файловой системы, позволяют загрузить рабочую область с помощью команды `ws=Workspace.from_config()`.

## <a name="azure-notebooks-and-data-science-virtual-machines"></a>Записные книжки Azure и виртуальные машины для обработки и анализа данных

Записные книжки Azure и Виртуальные машины для обработки и анализа данных (DSVM) Azure предварительно настроены для работы со Службой машинного обучения Azure. В этих средах уже есть все необходимые компоненты, например пакет SDK для Машинного обучения Azure.

### <a name="azure-notebooks"></a>Azure Notebook

- Записные книжки Azure — это служба Jupyter Notebook в облаке Azure.
- Для использования этих сред вам по-прежнему требуется файл конфигурации рабочей области.

Пример использования Записных книжек Azure со Службой машинного обучения Azure см. в статье о [начале работы со Службой машинного обучения Azure](quickstart-get-started.md).

### <a name="data-science-virtual-machines"></a>Виртуальная машина для обработки и анализа данных

- Виртуальная машина для обработки и анализа данных — это настраиваемый образ виртуальной машины, специально подготовленный для задач обработки и анализа данных. Сюда входят:
  - популярные средства для обработки и анализа данных;
  - интегрированные среды разработки, например PyCharm и RStudio;
  - пакеты, например Jupyter Notebook и Tensorflow.

Виртуальная машина для обработки и анализа данных включает несколько предварительно установленных сред Anaconda. Чтобы использовать пакет SDK Python для Службы машинного обучения Azure без установки пакетов, откройте командную строку (оболочку) и используйте одну з следующих команд для активации окружения:

* В виртуальная машине для обработки и анализа данных на базе __Ubuntu__ используйте команду:

    ```shell
    conda activate py36
    ```

* В виртуальная машине для обработки и анализа данных на базе __Windows__ используйте команду:

    ```shell
    conda activate AzureML
    ```

Активировав среду, вы можете сразу же импортировать пакет SDK Python для Службы машинного обучения Azure в используемое средство сборки, не устанавливая пакет.

```python
import azureml.core
print(azureml.core.VERSION)
```

Дополнительные сведения о Виртуальных машинах для обработки и анализа данных см. в [этой статье](https://azure.microsoft.com/services/virtual-machines/data-science-virtual-machines/).

## <a name="configure-jupyter-notebooks-on-your-computer"></a>Настройка Jupyter Notebook на локальном компьютере

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

   См. справочные документы для Python по классам и методам в следующих пакетах SDK:
   + [Пакет SDK службы "Машинное обучение Azure" для Python](https://aka.ms/aml-sdk)
   + [Пакет SDK azureml-dataprep службы "Машинное обучение Azure"](https://aka.ms/data-prep-sdk)

   > [!NOTE]
   > Если появится сообщение о том, что не удается удалить `PyYAML`, попробуйте использовать другую команду:
   >
   > `pip install --upgrade azureml-sdk[notebooks,automl] azureml-dataprep --ignore-installed PyYAML`

   Установка пакета SDK может занять несколько минут.

1. Установите пакеты для экспериментирования в машинном обучении. Используйте следующую команду, заменив в ней `<new package>` именем пакета, который хотите установить:

    ```shell
    conda install <new package>
    ```

1. Установите сервер Jupyter Notebook с поддержкой conda и включите экспериментальные мини-приложения (для просмотра сведений о выполнении). Используйте следующие команды:

    ```shell
    # install Jupyter
    conda install nb_conda

    # install experiment widget
    jupyter nbextension install --py --user azureml.train.widgets

    # enable experiment widget
    jupyter nbextension enable --py --user azureml.train.widgets
    ```

1. Откройте Jupyter Notebook, выполнив следующую команду:

    ```shell
    jupyter notebook
    ```

1. Откройте новую записную книжку, выберите ядро myenv и убедитесь, что установлен пакет SDK для Машинного обучения Azure. Выполните приведенную ниже команду в ячейке записной книжки.

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-visual-studio-code"></a>Откройте Visual Studio Code.

1. Откройте окно командной строки или оболочку.

1. Создайте среду conda, выполнив следующие команды:

    ```shell
    # create a new conda environment with Python 3.6, numpy and cython
    conda create -n myenv Python=3.6 cython numpy

    # activate the conda environment
    conda activate myenv

    # If you are running Mac OS you should run
    source activate myenv
    ```

1. Установите пакет SDK для Машинного обучения Azure и пакет SDK для подготовки данных, выполнив следующую команду:

    ```shell
    pip install --upgrade azureml-sdk[automl] azureml-dataprep
    ```

1. Установите средства Visual Studio Code для расширения искусственного интеллекта См. дополнительные сведения об [инструментах для искусственного интеллекта](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.vscode-ai).

1. Установите пакеты для экспериментирования в машинном обучении. Используйте следующую команду, заменив в ней `<new package>` именем пакета, который хотите установить:

    ```shell
    conda install <new package>
    ```

1. Запустите Visual Studio Code и нажмите сочетание клавиш **CTRL+SHIFT+P** (в среде Windows) или **COMMAND+SHIFT+P** (в среде Mac), чтобы открыть **палитру команд**. Введите _Python: Select Interpreter_ (Python: выбор интерпретатора) и выберите только что созданную среду conda.

   > [!NOTE]
   > Visual Studio Code автоматически учитывает среды conda на компьютере. Дополнительные сведения см. в документации по [Visual Studio Code](https://code.visualstudio.com/docs/python/environments#_conda-environments).

1. Проверьте конфигурацию. Для этого создайте с помощью Visual Studio Code новый файл скрипта Python с приведенным ниже кодом и выполните этот скрипт:

    ```python
    import azureml.core
    azureml.core.VERSION
    ```

## <a name="configure-a-custom-code-editor"></a>Настройка пользовательского редактора кода

С пакетом SDK для Машинного обучения Azure можно использовать любой редактор кода по своему усмотрению.

1. Создайте среду conda, как описано выше в шаге 2 раздела о [настройке Visual Studio Code](#configure-visual-studio-code).
1. Выполните инструкции для каждого редактора, в котором вам нужно использовать среду conda. Например, здесь есть [инструкции для PyCharm](https://www.jetbrains.com/help/pycharm/2018.2/conda-support-creating-conda-virtual-environment.html).

## <a name="next-steps"></a>Дополнительная информация

- [Обучение модели в машинном обучении Azure с помощью набора данных MNIST](tutorial-train-models-with-aml.md)
