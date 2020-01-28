---
title: Руководство. Настройка расширения Машинного обучения Azure для Visual Studio Code
titleSuffix: Azure Machine Learning
description: Сведения о настройке расширения Машинного обучения Azure для Visual Studio Code
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
author: luisquintanilla
ms.author: luquinta
ms.date: 01/16/2019
ms.openlocfilehash: 4000fcc80d507d3b1e871d7f3288fc3b77693c76
ms.sourcegitcommit: 276c1c79b814ecc9d6c1997d92a93d07aed06b84
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/16/2020
ms.locfileid: "76157465"
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
- Установите простой кроссплатформенный редактор кода [Visual Studio Code](https://code.visualstudio.com/docs/setup/setup-overview). 

## <a name="install-the-extension"></a>Установка расширения

1. Откройте Visual Studio Code.
1. Выберите значок **Расширения** на **панели действий**, чтобы открыть представление "Расширения".
1. В представлении "Расширения" выполните поиск по фразе Azure Machine Learning (Машинное обучение Azure).
1. Выберите пункт **Установить**.

> [!NOTE]
> Либо вы можете установить расширение Машинного обучения Azure из Visual Studio Marketplace, [скачав установщик напрямую](https://aka.ms/vscodetoolsforai). 

Остальные шаги в этом руководстве проверялись для расширения **версии 0.6.8**.

## <a name="sign-in-to-your-azure-account"></a>Вход в учетную запись Azure

Чтобы подготавливать ресурсы и выполнять рабочие нагрузки в Azure, необходимо выполнить вход с учетной записью Azure. Чтобы упростить управление учетными записями, Машинное обучение Azure автоматически устанавливает расширение учетной записи Azure. Сведения о расширении учетной записи Azure см. [здесь](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

1. Откройте палитру команд. Для этого на панели меню выберите **Вид > Палитра команд**. 
1. Введите команду "Azure: Sign In" в текстовое поле, чтобы начать процесс входа.

## <a name="run-a-script-in-azure"></a>Запуск скрипта в Azure

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
        "workspace": "WS12191742",
        "resourceGroup": "WS12191742-rg2",
        "location": "South Central US",
        "experiment": "WS12191742-exp2",
        "compute": {
            "name": "WS12191742-com2",
            "vmSize": "Standard_D1_v2, Cores: 1; RAM: 3.5GB;"
        },
        "runConfiguration": {
            "filename": "WS12191742-com2-rc1",
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

1. Щелкните **Отправка эксперимента**, чтобы запустить эксперимент в Azure. Это действие передает `train.py` и файл конфигурации в рабочую область Машинного обучения Azure. Затем задание обучения запускается на вычислительном ресурсе в Azure.
1. Через несколько минут создается локальный каталог с именем `output`, который содержит обученную модель TensorFlow.

## <a name="next-steps"></a>Дальнейшие действия

* [Руководство. Обучение и развертывание модели TensorFlow для классификации изображений с использованием расширения Машинного обучения Azure для Visual Studio Code](tutorial-train-deploy-image-classification-model-vscode.md).
