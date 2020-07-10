---
title: Экспорт проекта Jupyter Notebook из Записных книжек Azure (предварительная версия)
description: Быстрый экспорт проекта Jupyter Notebook.
ms.topic: quickstart
ms.date: 06/29/2020
ms.openlocfilehash: ad37db7e9bdb1251a3e62bd567960979f556b489
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2020
ms.locfileid: "85832038"
---
# <a name="quickstart-export-a-jupyter-notebook-project-in-azure-notebooks-preview"></a>Краткое руководство. Экспорт проекта Jupyter Notebook в Записные книжки Azure (предварительная версия)

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

В этом кратком руководстве показано, как скачать проект Записных книжек Azure для использования в других решениях Jupyter Notebook. 

## <a name="prerequisites"></a>Предварительные требования
Существующий проект Записных книжек Azure.

## <a name="export-an-azure-notebooks-project"></a>Экспорт проекта Записных книжек Azure

1. Перейдите в службу [Записные книжки Azure](https://notebooks.azure.com) и выполните вход. Дополнительные сведения см. в [кратком руководстве по входу в Записные книжки Azure](quickstart-sign-in-azure-notebooks.md).

1. Вверху на общедоступной странице профиля щелкните **Мои проекты**:

    ![Ссылка "Мои проекты" в верхней части окна браузера](media/quickstarts/my-projects-link.png)

1. Выберите проект.
1. Нажмите кнопку "Скачать", чтобы активировать скачивание ZIP-файла, содержащего все файлы проекта.
1. Или на странице определенного проекта нажмите кнопку "Скачать проект", чтобы скачать все файлы этого проекта.

Скачанные файлов проекта можно использовать с другими решениями Jupyter Notebook. Ниже приведены некоторые варианты, описанные в следующих разделах. 
- [Visual Studio Code](#use-notebooks-in-visual-studio-code)
- [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces)
- [Машинное обучение Azure](#use-notebooks-with-azure-machine-learning)
- [Службы лабораторий Azure](#use-azure-lab-services)
- [GitHub](#use-github)

## <a name="create-an-environment-for-notebooks"></a>Создание среды для записных книжек

Если вы хотите создать среду, соответствующую Записным книжкам Azure (предварительная версия) можно использовать файл скрипта, доступный в GitHub.

1. Перейдите в [репозиторий GitHub](https://github.com/microsoft/AzureNotebooks) Записных книжек Azure или [непосредственно откройте папку среды](https://aka.ms/aznbrequirementstxt).
1. В командной строке перейдите в каталог, который вы хотите использовать для проектов.
1. Скачайте содержимое папки среды и следуйте инструкциям в файле README, чтобы установить зависимости пакета Записных книжек Azure.


## <a name="use-notebooks-in-visual-studio-code"></a>Использование Записных книжек в Visual Studio Code

[VS Code](https://code.visualstudio.com/) — это бесплатный редактор кода, который можно использовать локально или можно подключать к удаленным вычислительным ресурсам. В сочетании с расширением Python он предлагает полную среду для разработки на Python, включая полнофункциональный нативный интерфейс для работы с записными книжками Jupyter. 

![Поддержка Jupyter Notebook в VS Code](media/vs-code-jupyter-notebook.png)

[Скачанные](#export-an-azure-notebooks-project) файлы проекта можно использовать с VS Code. См. руководства по [работе с Jupyter Notebook в Visual Studio Code](https://code.visualstudio.com/docs/python/jupyter-support) и [обработке и анализу данных в Visual Studio Code](https://code.visualstudio.com/docs/python/data-science-tutorial).

Вы также можете использовать [скрипт среды Записных книжек Azure](#create-an-environment-for-notebooks) с Visual Studio Code для создания среды, соответствующей Azure Notebooks предварительной версии.

## <a name="use-notebooks-in-visual-studio-codespaces"></a>Использование Записных книжек в Visual Studio Codespaces

Служба Visual Studio Codespaces предоставляет облачные среды, в которых можно редактировать записные книжки с помощью Visual Studio Code или веб-браузера. Она предлагает те же возможности для работы с Jupyter, что и VS Code, но без необходимости устанавливать какие-либо компоненты на устройстве. Если вы не хотите настраивать локальную среду и предпочитаете облачное решение, создать пространство кода будет отличным решением. Чтобы начать работу:

1. [Скачайте](#export-an-azure-notebooks-project) файлы проекта.
1. [Создайте репозиторий GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) для хранения записных книжек. 
1. [Добавьте файлы](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) в репозиторий.
1. Настройте Visual Studio Codespaces в [браузере](https://docs.microsoft.com/visualstudio/online/how-to/browser), [Visual Studio](https://docs.microsoft.com/visualstudio/online/how-to/vside) или [Visual Studio Code](https://docs.microsoft.com/visualstudio/online/how-to/vscode).

## <a name="use-notebooks-with-azure-machine-learning"></a>Использование Записных книжек с Машинным обучением Azure

Машинное обучение Azure — это комплексная платформа машинного обучения, с помощью которой пользователи могут быстро создавать и развертывать модели в Azure. Машинное обучение Azure позволяет запускать записные книжки Jupyter Notebook на виртуальной машине или в общей вычислительной среде кластера. Если вам требуется облачное решение для рабочей нагрузки машинного обучения с возможностями отслеживания экспериментов, управления наборами данных и пр., мы рекомендуем использовать Машинное обучение Azure. Чтобы начать работу с Машинным обучением Azure:

1. [Скачайте](#export-an-azure-notebooks-project) файлы проекта.
1. [Создайте рабочую область](../machine-learning/how-to-manage-workspace.md) на портале Azure.

   ![создать рабочую область;](../machine-learning/media/how-to-manage-workspace/create-workspace.gif)
 
1. Откройте [Студию машинного обучения Azure (предварительная версия)](https://ml.azure.com/).
1. В области навигации слева выберите **Записные книжки**.
1. Нажмите кнопку **Отправить файлы** и отправьте файлы проекта, скачанные из Записных книжек Azure.

Дополнительные сведения о Машинном обучении Azure и запуске записных книжек Jupyter Notebook см. в [этой документации](../machine-learning/how-to-run-jupyter-notebooks.md) или модуле [Введение в машинное обучение Azure](https://docs.microsoft.com/learn/modules/intro-to-azure-machine-learning-service/) на сайте Microsoft Learn.


## <a name="use-azure-lab-services"></a>Использование Служб лабораторий Azure

[Службы лабораторий Azure](https://azure.microsoft.com/services/lab-services/) позволяют преподавателям легко настраивать и предоставлять доступ по требованию к предварительно настроенным виртуальным машинам для всей аудитории. Если вам нужно работать с записными книжками Jupyter Notebook и облачными средами вычислений в специализированной среде класса, Службы лаборатории являются отличным вариантом.

![Изображение](../lab-services/media/tutorial-setup-classroom-lab/new-lab-button.png)

 [Скачанные](#export-an-azure-notebooks-project) файлы проекта можно использовать в Службах лабораторий Azure. См. статью [Настройка лаборатории для обучения обработке и анализу данных с использованием Python и Jupyter Notebook](../lab-services/class-type-jupyter-notebook.md).

## <a name="use-github"></a>Использование GitHub

GitHub предоставляет бесплатное решение для хранения записных книжек (и других файлов) с возможностью управления версиями, позволяя совместно использовать записные книжки с другими пользователями. Если вам нужно совместно использовать проекты и работать с другими пользователями, GitHub — это отличный вариант, который можно сочетать с [Visual Studio Codespaces](#use-notebooks-in-visual-studio-codespaces) для разработки. Чтобы начать работу с GitHub:

1. [Скачайте](#export-an-azure-notebooks-project) файлы проекта.
1. [Создайте репозиторий GitHub](https://help.github.com/github/getting-started-with-github/create-a-repo) для хранения записных книжек. 
1. [Добавьте файлы](https://help.github.com/github/managing-files-in-a-repository/adding-a-file-to-a-repository) в репозиторий.

## <a name="next-steps"></a>Дальнейшие действия

- [Узнайте об использовании Python в Visual Studio Code](https://code.visualstudio.com/docs/python/python-tutorial)
- [Узнайте о Машинном обучении Azure и записных книжках Jupyter Notebook](../machine-learning/how-to-run-jupyter-notebooks.md)
- [Узнайте о Visual Studio Codespaces](https://visualstudio.microsoft.com/services/visual-studio-codespaces/)
- [Узнайте о Службах лабораторий Azure](https://azure.microsoft.com/services/lab-services/)
- [Узнайте о GitHub](https://help.github.com/github/getting-started-with-github/)