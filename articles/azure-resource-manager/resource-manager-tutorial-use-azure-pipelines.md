---
title: Непрерывная интеграция с Azure Pipelines | Документация Майкрософт
description: Узнайте, как непрерывно создавать, тестировать и развертывать шаблоны Azure Resource Manager.
services: azure-resource-manager
documentationcenter: ''
author: mumian
manager: carmonm
editor: ''
ms.service: azure-resource-manager
ms.workload: multiple
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 06/12/2019
ms.topic: tutorial
ms.author: jgao
ms.openlocfilehash: 462d9cd6d2a911e660221621ebde5829e928cf00
ms.sourcegitcommit: fad368d47a83dadc85523d86126941c1250b14e2
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 09/19/2019
ms.locfileid: "71122227"
---
# <a name="tutorial-continuous-integration-of-azure-resource-manager-templates-with-azure-pipelines"></a>Руководство по Непрерывная интеграция шаблонов Azure Resource Manager с Azure Pipelines

Узнайте, как использовать Azure Pipelines для непрерывного создания и развертывания шаблона проектов Azure Resource Manager.

Azure DevOps предоставляет сервисы для разработчиков, которые помогают рабочим группам планировать работу, совместно разрабатывать код, а также создавать и развертывать приложения. Разработчики могут работать в облаке с помощью Azure DevOps Services. Azure DevOps предоставляет интегрированный набор функций, доступ к которым можно получить через веб-браузер или клиент IDE. Azure Pipeline является одной из таких функций. Azure Pipelines — это полнофункциональная служба непрерывной интеграции (CI) и непрерывной доставки (CD). Она работает с вашим основным провайдером Git и может развертываться в большинстве крупных облачных служб. Затем вы можете автоматизировать сборку, тестирование и развертывание своего кода в Microsoft Azure, Google Cloud Platform или Amazon Web Services.

Это руководство предназначено для разработчиков шаблонов Azure Resource Manager, которые являются новыми службами Azure DevOps и Azure Pipelines. Если вы уже работали с GitHub и DevOps, можете перейти к разделу [Создание конвейера](#create-a-pipeline).

> [!NOTE]
> Выберите имя проекта. Когда вы изучите руководство, замените любой из **AzureRmPipeline** именем вашего проекта.

В рамках этого руководства рассматриваются следующие задачи:

> [!div class="checklist"]
> * Подготовка репозитория GitHub
> * Создание проекта Azure DevOps.
> * Создание конвейера Azure
> * Проверка развертывания конвейера
> * Обновление шаблона и повторное развертывание
> * Очистка ресурсов

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись Azure](https://azure.microsoft.com/free/), прежде чем начинать работу.

## <a name="prerequisites"></a>Предварительные требования

Для работы с этой статьей необходимо иметь следующее.

* **Учетная запись GitHub** — где ее можно использовать для репозитория шаблонов. Если у вас ее нет, вы можете [создать ее бесплатно](https://github.com). Подробнее об использовании репозиториев GitHub см. в статье [Сборка репозиториев GitHub](/azure/devops/pipelines/repos/github).
* **Установка Git**. В этой инструкции руководства используется *Git Bash* или *Git Shell*. Инструкции см. в разделе [Установка Git]( https://www.atlassian.com/git/tutorials/install-git).
* **Организация Azure DevOps**. Если у вас ее нет, вы можете создать ее бесплатно. См. статью [Создание организации или коллекции проектов]( https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops).
* **[Visual Studio Code](https://code.visualstudio.com/) с расширением средств диспетчера ресурсов**. См. в разделе [Краткое руководство. Создание шаблона Azure Resource Manager c помощью Visual Studio Code](./resource-manager-quickstart-create-templates-use-visual-studio-code.md#prerequisites).

## <a name="prepare-a-github-repository"></a>Подготовка репозитория GitHub

GitHub используется для хранения исходного кода вашего проекта, включая шаблоны диспетчера ресурсов. Другие поддерживаемые репозитории доступны в [репозиториях, поддерживаемых Azure DevOps](/azure/devops/pipelines/repos/?view=azure-devops#supported-repository-types).

### <a name="create-a-github-repository"></a>Создание репозитория GitHub

Если у вас нет учетной записи GitHub, см. [Необходимые компоненты](#prerequisites).

1. Вход в [GitHub](https://github.com).
2. Выберите в правом верхнем углу изображение своей учетной записи, а затем выберите **Ваши репозитории**.

    ![Диспетчеры ресурсов Azure Resource Manager, Azure DevOps и Azure Pipelines создают репозиторий GitHub](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-repository.png)

1. Нажмите зеленую кнопку **Новый**.
1. В поле **Имя репозитория** введите имя репозитория.  Например, **AzureRmPipeline-repo**. Не забудьте заменить любой из **AzureRmPipeline** именем вашего проекта. Вы можете выбрать **общедоступный** или **частный** для просмотра этого руководства. Затем выберите **Создать репозиторий**.
1. Введите URL-адрес. URL-адрес репозитория имеет следующий формат:

    ```url
    https://github.com/[YourAccountName]/[YourRepositoryName]
    ```

Этот репозиторий называется *удаленным репозиторием*. Каждый из разработчиков одного и того же проекта может клонировать свой собственный *локальный репозиторий* и применить изменения к удаленному репозиторию.

### <a name="clone-the-remote-repository"></a>Клонирование репозитория

1. Откройте Git Shell или Git Bash.  См. раздел [Предварительные требования](#prerequisites).
1. Убедитесь, что вашей текущей папкой является **github**.
1. Выполните следующую команду:

    ```bash
    git clone https://github.com/[YourAccountName]/[YourGitHubRepositoryName]
    cd [YourGitHubRepositoryName]
    mkdir CreateAzureStorage
    cd CreateAzureStorage
    pwd
    ```

    Замените **[YourAccountName]** именем своей учетной записи GitHub, а **[YourGitHubRepositoryName]** замените именем своего репозитория, которое вы создали при выполнении предыдущей процедуры.

    На следующих снимках экрана продемонстрирован пример.

    ![Azure Resource Manager, Azure DevOps и Azure Pipelines создают GitHub Bash](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-github-bash.png)

Папка **CreateAzureStorage** —это папка, в которой хранится шаблон. С помощью команды **pwd** отображается путь к папке. Путь — это расположение, в котором будет сохраняться шаблон при выполнении следующей процедуры.

### <a name="download-a-quickstart-template"></a>Загрузите шаблон быстрого запуска

Вместо создания шаблона можно загрузить [шаблон быстрого запуска]( https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json). С помощью этого шаблона создается учетная запись хранения Azure.

1. Откройте Visual Studio Code. См. раздел [Предварительные требования](#prerequisites).
2. Откройте шаблон со следующим URL-адресом:

    ```URL
    https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json
    ```

3. Сохраните файл как **azuredeploy.json** в папку **CreateAzureStorage**. И имя папки, и имя файла используются так, как они есть в конвейере.  После изменения этих имен необходимо обновить имена, используемые в конвейере.

### <a name="push-the-template-to-the-remote-repository"></a>Отправьте шаблон в удаленный репозиторий

Azuredeploy.json был добавлен в локальный репозиторий. Затем загрузите шаблон в удаленный репозиторий.

1. Откройте *Git Shell* или *Git Bash*, если он не открыт.
1. Перейдите в папку CreateAzureStorage в вашем локальном репозитории.
1. Убедитесь, что файл **azuredeploy.json** находится в папке.
1. Выполните следующую команду:

    ```bash
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Может появиться предупреждение о LF. На него можно не обращать внимания. **Образец** — это главная ветвь.  Обычно вы создаете ветвь для каждого обновления. Чтобы упростить работу с руководством, используете основную ветвь напрямую.
1. Перейдите из браузера в свой репозиторий GitHub.  URL-адрес: **https://github.com/ [YourAccountName]/[YourGitHubRepository]** . Вы увидите папку **CreateAzureStorage**, а внутри папки — **Azuredeploy.json**.

Вы создали репозиторий GitHub и загрузили в него шаблон.

## <a name="create-a-devops-project"></a>Создание проекта DevOps

Организация DevOps необходима, прежде чем вы сможете перейти к выполнению следующей процедуры.  Если у вас его нет, см. [Необходимые компоненты](#prerequisites).

1. Вход в [Azure DevOps](https://dev.azure.com).
1. Выберите организацию DevOps слева.

    ![Azure Resource Manager, Azure DevOps и Azure Pipelines создают проект Azure DevOps](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-create-devops-project.png)

1. Щелкните **Создать проект**. Если у вас нет проектов, страница создания проекта откроется автоматически.
1. Введите следующие значения.

    * **Имя проекта**: введите имя проекта. Вы можете использовать имя проекта, которое вы выбрали в самом начале руководства.
    * **Управление версиями**: Выберите **Git**. Вам может потребоваться развернуть **Расширенный**, чтобы ознакомиться с **Управлением версиями**.

    Для других свойств используйте значения по умолчанию.
1. Щелкните **Создать проект**.

Создайте подключение к службе, которое используется для развертывания проектов в Azure.

1. Выберите **Параметры проекта** в нижней части левого меню.
1. Выберите **Подключения к службе** в **Pipelines**.
1. Выберите **Новое подключение к службе**, а затем выберите **AzureResourceManager**.
1. Введите следующие значения.

    * **Имя подключения**: ввод имени подключения. Например, **AzureRmPipeline-conn**. Запишите это имя, оно вам понадобится при создании конвейера.
    * **Уровень области**: выбор **Подписки**.
    * **Подписка**: выберите свою подписку.
    * **Группа ресурсов**. Оставьте пустым.
    * **Разрешите всем контейнерам использовать это подключение**. (выбрано)
1. Нажмите кнопку **ОК**.

## <a name="create-a-pipeline"></a>Создание конвейера

До этого момента мы выполнили следующие задачи.  Если вы пропустили предыдущие разделы, потому что уже работали с GitHub и DevOps, перед продолжением необходимо выполнить следующие задачи.

- Создайте репозиторий GitHub и сохраните [этот шаблон](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-storage-account-create/azuredeploy.json) в папке **CreateAzureStorage** в репозитории.
- Создайте проект DevOps и создайте подключение службы Azure Resource Manager.

Чтобы создать конвейер с шагом для развертывания шаблона:

1. Выберите **Pipelines** в меню слева.
1. Выберите **Создание конвейера**.
1. На вкладке **Подключить** выберите **GitHub**. Если потребуется, введите учетные данные GitHub, а затем следуйте инструкциям. Если вы видите следующий экран, выберите **Выбрать только репозитории** и убедитесь, что ваш репозиторий находится в списке, прежде чем выбрать **Утвердить и установить**.

    ![Azure Resource Manager, Azure DevOps, Azure Pipelines выбирают только репозитории](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-only-select-repositories.png)

1. На вкладке **Выбрать** выберите свой репозиторий.  Имя по умолчанию: **[YourAccountName]/[YourGitHubRepositoryName]** .
1. На вкладке **Настроить** выберите **Простейший конвейер**. Он отобразит файл конвейера **azure-pipelines.yml**в два этапа с помощью сценария.
1. Замените раздел **Шаги** следующим YAML:

    ```yaml
    steps:
    - task: AzureResourceGroupDeployment@2
      inputs:
        azureSubscription: '[YourServiceConnectionName]'
        action: 'Create Or Update Resource Group'
        resourceGroupName: '[EnterANewResourceGroupName]'
        location: 'Central US'
        templateLocation: 'Linked artifact'
        csmFile: 'CreateAzureStorage/azuredeploy.json'
        deploymentMode: 'Incremental'
    ```

    Он должен выглядеть так:

    ![YAML Azure Resource Manager, Azure DevOps, Azure Pipelines](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-yml.png)

    Выполните следующие изменения:

    * **azureSubscription**: обновите значение с помощью подключения к службе, созданного при выполнении предыдущей процедуры;
    * **действие**: действие **Создание или изменение группы ресурсов** выполняет 2 действия: 1. создает группу ресурсов, если указано новое имя группы ресурсов; 2. развертывает указанный шаблон;
    * **resourceGroupName**: укажите имя новой группы ресурсов; Например, **AzureRmPipeline-rg**;
    * **расположение**: укажите расположение для группы ресурсов;
    * **templateLocation**: если указан **связанный артефакт**, задача ищет файл шаблона непосредственно из подключенного репозитория;
    * **csmFile** — это путь к файлу шаблона. Вам не нужно указывать файл параметров шаблона, потому что все параметры, определенные в шаблоне, имеют значения по умолчанию.

    Подробнее о задаче см. в разделе [Задача развертывания группы ресурсов Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment)
1. Выберите **Сохранить и запустить**.
1. Еще раз выберите **Сохранить и запустить**. Копия файла YAML сохраняется в подключенном хранилище. Вы можете увидеть файл YAML, перейдя в свой репозиторий.
1. Убедитесь, что конвейер успешно выполнен.

    ![YAML Azure Resource Manager, Azure DevOps, Azure Pipelines](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-status.png)

## <a name="verify-the-deployment"></a>Проверка развертывания

1. Войдите на [портале Azure](https://portal.azure.com).
1. Откройте группу ресурсов. Имя — это то, что вы указали в файле YAML конвейера.  Вы ознакомитесь с одной созданной учетной записью хранения.  Имя учетной записи хранения начинается с **store**.
1. Выберите имя учетной записи хранения, чтобы открыть ее.
1. Выберите **Свойства**. Учтите, что **SKU** является **Standard_LRS**.

    ![Проверка Azure Resource Manager, Azure DevOps, Azure Pipelines](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-portal-verification.png)

## <a name="update-and-redeploy"></a>Обновление и повторное развертывание

При обновлении шаблона и отправке изменений в удаленный репозиторий, конвейер автоматически обновляет ресурсы, в данном случае учетную запись хранения.

1. Откройте **azuredeploy.json** из своего локального репозитория в Visual Studio Code.
1. Обновите **defaultValue** из **storageAccountType** до **Standard_GRS**. Экран должен выглядеть так:

    ![YAML обновление Azure Resource Manager, Azure DevOps, Azure Pipelines](./media/resource-manager-tutorial-use-azure-pipelines/azure-resource-manager-devops-pipelines-update-yml.png)

1. Сохраните изменения.
1. Отправьте изменения в удаленный репозиторий, выполнив следующие команды из Git Bash/Shell.

    ```bash
    git pull origin master
    git add .
    git commit -m “Add a new create storage account template.”
    git push origin master
    ```

    Первая команда синхронизирует локальный репозиторий с удаленным репозиторием. Не забудьте, что в удаленный репозиторий был добавлен файл YAML конвейера.

    После обновления главной ветви удаленного репозитория конвейер снова запустится.

Чтобы проверить изменения, можно проверить SKU учетной записи хранения.  См. раздел [Проверка развертывания](#verify-the-deployment).

## <a name="clean-up-resources"></a>Очистка ресурсов

Если ресурсы Azure больше не нужны, их можно удалить. Для этого необходимо удалить группу ресурсов.

1. На портале Azure в меню слева выберите **Группа ресурсов**.
2. В поле **Filter by name** (Фильтровать по имени) введите имя группы ресурсов.
3. Выберите имя группы ресурсов.
4. В главном меню выберите **Удалить группу ресурсов**.

Кроме того, также можно удалить репозиторий GitHub и проект DevOps Azure.

## <a name="next-steps"></a>Дополнительная информация

Работая с этим руководством, вы создадите конвейер Azure DevOps, чтобы развертывать шаблон Azure Resource Manager. Чтобы узнать, как развертывать ресурсы Azure в нескольких регионах и как использовать безопасное развертывание, ознакомьтесь со следующей статьей.

> [!div class="nextstepaction"]
> [Руководство. Использование диспетчера развертывания Azure с шаблонами Resource Manager (закрытая предварительная версия)](./deployment-manager-tutorial.md)
