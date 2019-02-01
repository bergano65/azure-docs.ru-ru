---
title: Использование с Visual Studio Code
titleSuffix: Azure Machine Learning service
description: Узнайте, как установить Машинное обучение Azure для Visual Studio Code, и создайте простой эксперимент в Машинном обучении Azure.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: shwinne
author: swinner95
ms.date: 12/04/2018
ms.custom: seodec18
ms.openlocfilehash: d6f49eb0359db6184b5ecd146d7328a64611a9f9
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55245573"
---
# <a name="get-started-with-azure-machine-learning-for-visual-studio-code"></a>Приступая к работе с Azure Machine Learning for Visual Studio Code

В этой статье вы узнаете, как установить расширение **Azure Machine Learning for Visual Studio Code** и создать первый эксперимент с помощью Службы машинного обучения Azure в Visual Studio Code (VS Code).

Используйте расширение Azure Machine Learning for Visual Studio Code в Visual Studio Code, чтобы с помощью Службы машинного обучения Azure можно было подготовить данные, обучить и протестировать модели машинного обучения на локальных и удаленных целевых объектах вычислений, развернуть эти модели и отслеживать пользовательские метрики и эксперименты.

## <a name="prerequisite"></a>Предварительные требования


+ Если у вас еще нет подписки Azure, создайте бесплатную учетную запись Azure, прежде чем начинать работу. Опробуйте [бесплатную или платную версию Службы машинного обучения Azure](http://aka.ms/AMLFree).

+ Необходимо установить Visual Studio Code. VS Code — это легковесный, но мощный редактор исходного кода, который работает на компьютере. В нем реализована поддержка Python и других языков.  [Сведения об установке VS Code](https://code.visualstudio.com/docs/setup/setup-overview).

+ [Установка Python версии 3.5 или более поздней](https://www.anaconda.com/download/).


## <a name="install-the-azure-machine-learning-for-vs-code-extension"></a>Установка расширения Azure Machine Learning for VS Code

При установке расширения **Azure Machine Learning for Visual Studio Code** автоматически устанавливаются два других расширения (при наличии доступа к Интернету). Это расширения [Azure Account](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account) и [Microsoft Python](https://marketplace.visualstudio.com/items?itemName=ms-python.python).

Для работы со службой "Машинное обучение Azure" нам нужно преобразовать VS Code в интегрированную среду разработки на языке Python. Для работы с [Python в Visual Studio Code](https://code.visualstudio.com/docs/languages/python) требуется расширение Microsoft Python, которое автоматически устанавливается вместе с расширением Azure Machine Learning for Visual Studio Code. Это расширение превращает VS Code в мощную интегрированную среду разработки, которая работает в любой операционной системе с различными интерпретаторами Python. Оно использует все возможности VS Code, предоставляя функции автозаполнения и IntelliSense, анализа кода, отладки и модульного тестирования, а также возможность легко переключаться между окружениями Python, включая виртуальные окружения и окружения conda. Пошаговые инструкции по редактированию, запуску и отладке кода Python см. в [руководстве по началу работы с Python](https://code.visualstudio.com/docs/python/python-tutorial).

**Чтобы установить расширение Azure Machine Learning for Visual Studio Code, сделайте следующее.**

1. Запустите VS Code.

1. В браузере перейдите по ссылке: [Расширение Azure Machine Learning for Visual Studio Code (предварительная версия)](https://aka.ms/vscodetoolsforai)

1. На этой странице нажмите **Install** (Установить). 

1. На вкладке расширений нажмите **Установить**.

1. В VS Code откроется приветственная страница расширения, и на панели действий появится символ Azure.

   ![Значок Azure на панели действий Visual Studio Code](./media/vscode-tools-for-ai/azure-activity-bar.png)

1. В диалоговом окне нажмите **Sign In** (Войти) и следуйте инструкциям во всплывающем окне, чтобы выполнить проверку подлинности в Azure. 
   
   Расширение Azure Account, которое было установлено вместе с расширением Azure Machine Learning for Visual Studio Code, поможет выполнить аутентификацию вашей учетной записи Azure. Список команд см. на странице [расширения Azure Account ](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-account).

> [!Tip] 
> Ознакомьтесь с расширением [IntelliCode для VS Code (предварительная версия)](https://go.microsoft.com/fwlink/?linkid=2006060). IntelliCode предоставляет набор возможностей на основе искусственного интеллекта для функции IntelliSense в языке Python, например определение наиболее подходящих объектов для автозаполнения на основе текущего контекста кода.

## <a name="install-the-sdk"></a>Установка пакета SDK

1. Убедитесь, что у вас установлен интерпретатор Python версии 3.5 или более поздней, который распознан VS Code. Если вы установили Python только сейчас, перезапустите VS Code и выберите интерпретатор Python, воспользовавшись инструкциями в руководстве https://code.visualstudio.com/docs/python/python-tutorial.

1. В VS Code откройте палитру команд с помощью клавиш **CTRL+SHIFT+P**.

1. Введите Install Azure ML SDK, чтобы найти команду установки пакета SDK с помощью pip. Будет создано локальное частное окружение Python, содержащее все компоненты, требуемые Visual Studio Code для работы со службой "Машинное обучение Azure".

   ![Установка пакета SDK Машинного обучения Azure для Python](./media/vscode-tools-for-ai/install-sdk.png)

1. Во встроенном окне терминала укажите используемый интерпретатор Python или нажмите клавишу **ВВОД**, чтобы использовать интерпретатор Python по умолчанию.

   ![Выбор интерпретатора](./media/vscode-tools-for-ai/python.png)

## <a name="get-started-with-azure-machine-learning"></a>Начало работы со службой "Машинное обучение Azure"

Прежде чем начать обучение и развертывание моделей машинного обучения с помощью VS Code, необходимо создать [рабочую область в облачной Службе машинного обучения Azure](concept-azure-machine-learning-architecture.md#workspace), которая будет содержать модели и ресурсы. Ознакомьтесь со сведениями о создании рабочей области и создайте в ней свой первый эксперимент.

1. Щелкните значок Azure на панели действий Visual Studio Code. Появится боковая панель "Машинное обучение Azure".

   [![Установка](./media/vscode-tools-for-ai/CreateaWorkspace.gif)](./media/vscode-tools-for-ai/CreateaWorkspace.gif#lightbox)


1. Щелкните правой кнопкой мыши подписку Azure и выберите **Create Workspace** (Создать рабочую область). Появится список. В примере, показанном на анимированном изображении, имя подписки — Free Trial, а рабочее пространство — TeamWorkspace. 

1. Выберите существующую группу ресурсов из списка или создайте новую с помощью мастера в палитре команд.

1. В поле введите уникальное и понятное имя новой рабочей области. На снимках экрана имя рабочей области — TeamWorkspace.

1. Нажмите клавишу ВВОД для создания рабочей области. Она появится в древовидном представлении под именем подписки.

1. Щелкните правой кнопкой мыши имя рабочей области и в контекстном меню выберите **Create Experiment** (Создать эксперимент).  Эксперименты используются для отслеживания выполняемых операций в службе "Машинное обучение Azure".

1. В поле введите имя эксперимента. На снимках экрана имя эксперимента — MNIST.
 
1. Нажмите клавишу ВВОД для создания эксперимента. Он отобразится в древовидной структуре под именем рабочей области.

1. В рабочей области щелкните правой кнопкой мыши эксперимент и выберите Set as Active Experiment (Установить в качестве активного эксперимента). **Активным** называется эксперимент, который вы сейчас используете. Папка, открытая в VS Code, будет связана с этим экспериментом в облаке. В этой папке должны находиться локальные скрипты Python.

   Теперь все выполняемые операции связаны с экспериментом, благодаря чему все ключевые метрики будут храниться в журнале эксперимента, а обученные модели будут автоматически отправляться в службу "Машинное обучение Azure" и храниться вместе с метриками и журналами эксперимента.

   [![Присоединение папки в VS Code](./media/vscode-tools-for-ai/CreateAnExperiment.gif)](./media/vscode-tools-for-ai/CreateAnExperiment.gif#lightbox)

### <a name="use-keyboard-shortcuts"></a>Использование сочетаний клавиш

Как и для большинства функций VS Code, для службы "Машинное обучение Azure" в VS Code предусмотрены сочетания клавиш. Самое важное сочетание клавиш, которое нужно запомнить, — это CTRL+SHIFT+P для вызова палитры команд. Из палитры вы получаете доступ ко всем функциям VS Code, включая сочетания клавиш для самых распространенных операций.

[![Сочетания клавиш для расширения Azure Machine Learning for VS Code](./media/vscode-tools-for-ai/commands.gif)](./media/vscode-tools-for-ai/commands.gif#lightbox)

## <a name="next-steps"></a>Дополнительная информация

Теперь вы можете использовать Visual Studio Code для работы со службой "Машинное обучение Azure".

См. сведения о том, как [создавать вычислительные ресурсы и развертывать модели из Visual Studio Code](how-to-vscode-train-deploy.md).
