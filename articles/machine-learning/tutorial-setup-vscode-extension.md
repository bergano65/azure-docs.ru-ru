---
title: Руководство по настройке расширения Visual Studio Code
titleSuffix: Azure Machine Learning
description: Сведения о настройке расширения Машинного обучения Azure для Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 02/24/2020
ms.openlocfilehash: 583071ee22e4fb9cffc741520b1583790002a5bf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77604859"
---
# <a name="set-up-azure-machine-learning-visual-studio-code-extension"></a>Настройка расширения Машинного обучения Azure для Visual Studio Code

Узнайте, как настраивать расширение Машинного обучения Azure для Visual Studio Code и выполнять скрипты с его помощью.

В этом руководстве вы выполнили следующие задачи.

> [!div class="checklist"]
> * Установка расширения Машинного обучения Azure для Visual Studio Code.
> * Вход в учетную запись Azure в Visual Studio Code.
> * Применение расширения Машинного обучения Azure для выполнения тестового скрипта.

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure. Если у вас ее нет, зарегистрируйтесь для использования [бесплатной или платной версии службы "Машинное обучение Azure"](https://aka.ms/AMLFree)
- Visual Studio Code. Если у вас нет Visual Studio Code, [установите его](https://code.visualstudio.com/docs/setup/setup-overview).
- [Python 3](https://www.python.org/downloads/);

## <a name="install-the-extension"></a>Установка расширения

1. Откройте Visual Studio Code.
1. Выберите значок **Расширения** на **панели действий**, чтобы открыть представление "Расширения".
1. В представлении "Расширения" выполните поиск по фразе Azure Machine Learning (Машинное обучение Azure).
1. Выберите пункт **Установить**.

    > [!div class="mx-imgBorder"]
    > ![Установка расширения Машинного обучения Azure для VS Code](./media/tutorial-setup-vscode-extension/install-aml-vscode-extension.PNG)

> [!NOTE]
> Либо вы можете установить расширение Машинного обучения Azure из Visual Studio Marketplace, [скачав установщик напрямую](https://aka.ms/vscodetoolsforai). 

Остальные шаги в этом руководстве проверялись для расширения **версии 0.6.8**.

## <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

Чтобы подготавливать ресурсы и выполнять рабочие нагрузки в Azure, необходимо выполнить вход с учетной записью Azure. Чтобы упростить управление учетными записями, Машинное обучение Azure автоматически устанавливает расширение учетной записи Azure. Сведения о расширении учетной записи Azure см. [здесь](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Откройте палитру команд. Для этого на панели меню выберите **Вид > Палитра команд**. 
1. Введите команду "Azure: Sign In" в палитре команд, чтобы начать процесс входа.

## <a name="run-a-machine-learning-model-training-script-in-azure"></a>Запуск скрипта обучения модели машинного обучения в Azure

Теперь, когда вы вошли в Azure со своей учетной записью, выполните описанные в этом разделе действия, чтобы применить расширение для обучения модели машинного обучения.

1. Скачайте [репозиторий Visual Studio Code Tools for AI](https://github.com/microsoft/vscode-tools-for-ai/archive/master.zip) и разверните эти средства в любом расположении на компьютере.
1. Откройте каталог `mnist-vscode-docs-sample` в Visual Studio Code.
1. Щелкните значок **Azure** на панели действий.
1. Щелкните значок **Run Experiment** (Выполнение эксперимента) в верхней части страницы представления Машинного обучения Azure.

    > [!div class="mx-imgBorder"]
    > ![Выполнение эксперимента](./media/tutorial-setup-vscode-extension/run-experiment.PNG)

1. Когда развернется палитра команд, следуйте предложенным инструкциям.

    1. Выберите подписку Azure.
    1. Щелкните **Create a new Azure ML workspace** (Создание рабочей области машинного обучения Azure).
    1. Выберите тип задания **TensorFlow Single-Node Training** (Обучение TensorFlow с одним узлом).
    1. Введите скрипт `train.py` для обучения. Этот файл содержит код для модели машинного обучения, которая классифицирует изображения с рукописными цифрами.
    1. В качестве требований укажите следующие пакеты.

        ```text
        pip: azureml-defaults; conda: python=3.6.2, tensorflow=1.15.0
        ```

1. На этом этапе в текстовом редакторе отображается файл конфигурации, аналогичный показанному ниже. Эта конфигурация содержит сведения для запуска задания обучения, например файл с кодом для обучения модели и любые зависимости Python, которые вы указали на предыдущем шаге.

    ```json
    {
        "workspace": "WS01311608",
        "resourceGroup": "WS01311608-rg1",
        "location": "South Central US",
        "experiment": "WS01311608-exp1",
        "compute": {
            "name": "WS01311608-com1",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS01311608-com1-rc1",
            "condaDependencies": [
                "python=3.6.2",
                "tensorflow=1.15.0"
            ],
            "pipDependencies": [
                "azureml-defaults"
            ]
        }
    }
    ```

1. Если конфигурация вас устраивает, отправьте эксперимент. Для этого откройте палитру команд и введите следующую команду:

    ```text
    Azure ML: Submit Experiment
    ```

    Это действие передает `train.py` и файл конфигурации в рабочую область Машинного обучения Azure. Затем задание обучения запускается на вычислительном ресурсе в Azure.

### <a name="track-the-progress-of-the-training-script"></a>Отслеживание хода выполнения скрипта обучения

Выполнение скрипта может занять несколько минут. Чтобы отслеживать ход его выполнения, выполните приведенные ниже действия.

1. Щелкните значок **Azure** на панели действий.
1. Разверните узел подписки.
1. Разверните текущий узел эксперимента. Он находится внутри узла `{workspace}/Experiments/{experiment}`, где значения для рабочей области и эксперимента совпадают со свойствами, определенными в файле конфигурации.
1. В список включены все запуски для эксперимента, а также их состояние. Чтобы получить самое последнее состояние, щелкните значок обновления в верхней части представления "Машинное обучение Azure".

    > [!div class="mx-imgBorder"]
    > ![Отслеживание хода выполнения эксперимента](./media/tutorial-setup-vscode-extension/track-experiment-progress.PNG)

### <a name="download-the-trained-model"></a>Скачивание обученной модели

После завершения эксперимента выходными данными является обученная модель. Чтобы скачать выходные данные на локальный компьютер, выполните приведенные ниже шаги.

1. Щелкните правой кнопкой мыши последний запуск и выберите **Download Outputs** (Скачать выходные данные модели).

    > [!div class="mx-imgBorder"]
    > ![Скачивание обученной модели](./media/tutorial-setup-vscode-extension/download-trained-model.PNG)

1. Выберите расположение для сохранения выходных данных.
1. Папка с именем запуска скачивается локально. Перейдите на эту страницу.
1. Файлы моделей расположены в каталоге `outputs/outputs/model`.

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. Обучение и развертывание модели TensorFlow для классификации изображений с использованием расширения Машинного обучения Azure для Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
