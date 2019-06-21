---
title: CI/CD с конвейеры Azure и шаблонов Resource Manager
description: В этой статье описывается настройка непрерывной интеграции в конвейерах Azure с помощью проектов развертывания группы ресурсов Azure в Visual Studio можно развертывать шаблоны диспетчера ресурсов.
author: tfitzmac
ms.service: azure-resource-manager
ms.topic: article
ms.date: 06/12/2019
ms.author: tomfitz
ms.openlocfilehash: b70b38904c0373c53c3731dd0442511116d9c4de
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191464"
---
# <a name="integrate-resource-manager-templates-with-azure-pipelines"></a>Интеграция шаблонов Resource Manager с конвейерами Azure

Visual Studio предоставляет проект группы ресурсов Azure для создания шаблонов и их развертывание в подписке Azure. Этот проект можно интегрировать с конвейерами Azure для непрерывной интеграции и непрерывного развертывания (CI/CD).

Развертывание шаблонов с помощью конвейеров Azure двумя способами:

* **Добавить задачу, которая выполняет сценарий Azure PowerShell**. Этот параметр имеет преимущество при предоставлении согласованность на протяжении всего жизненного цикла разработки, так как используется тот же сценарий, который включен в проект Visual Studio (Deploy-AzureResourceGroup.ps1). Скрипт этапы артефакты проекта в учетную запись хранилища с доступом к Resource Manager. Артефакты — это элементы в проекте, такие как связанные шаблоны, сценарии и двоичные файлы приложения. Затем сценарий развертывает шаблон.

* **Добавление задач на копирование и развертывание задачи**. Этот вариант предлагает удобную альтернативу в проект скрипта. Настройте две задачи в конвейере. Одна задача размещает артефакты и другая задача развертывает шаблон.

В этой статье показаны оба подхода.

## <a name="prepare-your-project"></a>Подготовка проекта

В этой статье предполагается, что в проект Visual Studio и Azure DevOps организации готовы к созданию конвейера. Ниже показано, как убедиться, что вы будете готовы:

* У вас есть организации DevOps в Azure. Если у вас нет, [создать бесплатно](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Если команда уже имеет организации DevOps в Azure, убедитесь, что вы являетесь администратором проекта DevOps в Azure, который вы хотите использовать.

* Вы настроили [подключение к службе](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) свою подписку Azure. Задачи в конвейере выполняются с идентификатором субъекта-службы. Действия, чтобы создать подключение, см. в разделе [создать проект DevOps](resource-manager-tutorial-use-azure-pipelines.md#create-a-devops-project).

* У вас есть проект Visual Studio, который был создан из **группы ресурсов Azure** шаблон начального уровня. Сведения о создании этого типа проекта, см. в разделе [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

* Проект Visual Studio [подключения в проект Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Создание конвейера

1. Если конвейер не был добавлен ранее, необходимо создать новый конвейер. В вашей организации DevOps в Azure, выберите **конвейеры** и **новый конвейер**.

   ![Добавить новый конвейер](./media/vs-resource-groups-project-devops-pipelines/new-pipeline.png)

1. Укажите, где хранится ваш код. На следующем рисунке показана выбрав **Azure репозиториев Git**.

   ![Выберите исходный код](./media/vs-resource-groups-project-devops-pipelines/select-source.png)

1. Из этого источника выберите репозиторий, который содержит код для проекта.

   ![Выберите репозиторий](./media/vs-resource-groups-project-devops-pipelines/select-repo.png)

1. Выберите тип конвейер, чтобы создать. Можно выбрать **конвейера Starter**.

   ![Выберите конвейер](./media/vs-resource-groups-project-devops-pipelines/select-pipeline.png)

Вы готовы добавить задачу Azure PowerShell или копирование файла и развертывание задачи.

## <a name="azure-powershell-task"></a>Задачи по Azure PowerShell

В этом разделе показано, как настроить непрерывное развертывание с помощью отдельной задачи, которая выполняет сценарий PowerShell в вашем проекте. Создает следующий файл YAML [задач Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

```yaml
pool:
  name: Hosted Windows 2019 with VS2019
  demands: azureps

steps:
- task: AzurePowerShell@3
  inputs:
    azureSubscription: 'demo-deploy-sp'
    ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
    ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' 
    azurePowerShellVersion: LatestVersion
```

Если задано задачи `AzurePowerShell@3`, этот конвейер использует команды из модуля AzureRM для проверки подлинности подключения. По умолчанию сценарий PowerShell в проект Visual Studio использует модуль AzureRM. Если вы обновили сценарий для использования [модуль Az](/powershell/azure/new-azureps-module-az), равным задачи `AzurePowerShell@4`.

```yaml
steps:
- task: AzurePowerShell@4
```

Для `azureSubscription`, укажите имя созданного подключения службы.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Для `scriptPath`, укажите относительный путь из конвейера файла скрипта. Она позволяет заглянуть в репозиторий для просмотра пути.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Если не требуется для размещения артефактов, просто передайте имя и расположение группы ресурсов для развертывания. Скрипт Visual Studio создает группу ресурсов, если он еще не существует.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Если вам нужно размещения артефактов в существующую учетную запись хранения, используйте:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Теперь, что вы знаете, как создать задачу, давайте разберем действий по изменению конвейера.

1. Откройте свой конвейер и замените его содержимое в yaml-ФАЙЛ:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019
     demands: azureps

   steps:
   - task: AzurePowerShell@3
     inputs:
       azureSubscription: 'demo-deploy-sp'
       ScriptPath: 'AzureResourceGroupDemo/Deploy-AzureResourceGroup.ps1'
       ScriptArguments: -ResourceGroupName 'demogroup' -ResourceGroupLocation 'centralus' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName 'stage3a4176e058d34bb88cc'
       azurePowerShellVersion: LatestVersion
   ```

1. Щелкните **Сохранить**.

   ![Сохранение конвейера](./media/vs-resource-groups-project-devops-pipelines/save-pipeline.png)

1. Введите сообщение для фиксации и непосредственно к фиксации **master**.

1. При выборе **Сохранить**, автоматически выполняется через конвейер сборки. Вернуться к сводке для конвейера сборки и посмотреть состояние.

   ![Просмотр результатов](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Вы можете выбрать текущего выполняемого конвейера, чтобы просмотреть сведения о задачах. По завершении отобразятся результаты для каждого шага.

## <a name="copy-and-deploy-tasks"></a>Скопируйте и развертывание задачи

В этом разделе показано, как настроить непрерывное развертывание с помощью двух задач этапа артефакты и развертывания шаблона. 

Ниже YAML [задачи копирования файлов Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops):

```yaml
- task: AzureFileCopy@3
  displayName: 'Stage files'
  inputs:
    SourcePath: 'AzureResourceGroup1'
    azureSubscription: 'demo-deploy-sp'
    Destination: 'AzureBlob'
    storage: 'stage3a4176e058d34bb88cc'
    ContainerName: 'democontainer'
    outputStorageUri: 'artifactsLocation'
    outputStorageContainerSasToken: 'artifactsLocationSasToken'
    sasTokenTimeOutInMinutes: '240'
```

Существует несколько частей этой задачи, чтобы изменить для вашей среды. `SourcePath` Указывает расположение артефактов по отношению к файлу конвейера. В этом примере файлы существуют в папку с именем `AzureResourceGroup1` которого было имя проекта.

```yaml
SourcePath: '<path-to-artifacts>'
```

Для `azureSubscription`, укажите имя созданного подключения службы.

```yaml
azureSubscription: '<your-connection-name>'
```

Для хранения и контейнер по имени укажите имена учетной записи хранения и контейнер, который вы хотите использовать для хранения артефактов. Учетная запись хранения должна существовать.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Ниже YAML [задачи развертывания группы ресурсов Azure](/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment?view=azure-devops):

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    azureSubscription: 'demo-deploy-sp'
    resourceGroupName: 'demogroup'
    location: 'centralus'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
```

Существует несколько частей этой задачи, чтобы изменить для вашей среды. Для `azureSubscription`, укажите имя созданного подключения службы.

```yaml
azureSubscription: '<your-connection-name>'
```

Для `resourceGroupName` и `location`, укажите имя и расположение группы ресурсов, которые вы хотите развернуть. Задача создает группу ресурсов, если он не существует.

```yaml
resourceGroupName: '<resource-group-name>'
location: '<location>'
```

Ссылки на задачи развертывания в шаблон с именем `WebSite.json` и файл параметров с именем WebSite.parameters.json. Используйте имена файлов шаблонов и параметров.

Теперь, что вы знаете, как создавать задачи, давайте разберем действий по изменению конвейера.

1. Откройте свой конвейер и замените его содержимое в yaml-ФАЙЛ:

   ```yaml
   pool:
     name: Hosted Windows 2019 with VS2019

   steps:
   - task: AzureFileCopy@3
     displayName: 'Stage files'
     inputs:
       SourcePath: 'AzureResourceGroup1'
       azureSubscription: 'demo-deploy-sp'
       Destination: 'AzureBlob'
       storage: 'stage3a4176e058d34bb88cc'
       ContainerName: 'democontainer'
       outputStorageUri: 'artifactsLocation'
       outputStorageContainerSasToken: 'artifactsLocationSasToken'
       sasTokenTimeOutInMinutes: '240'
   - task: AzureResourceGroupDeployment@2
     displayName: 'Deploy template'
     inputs:
       azureSubscription: 'demo-deploy-sp'
       resourceGroupName: demogroup
       location: 'centralus'
       templateLocation: 'URL of the file'
       csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
       csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
       overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
   ```

1. Щелкните **Сохранить**.

1. Введите сообщение для фиксации и непосредственно к фиксации **master**.

1. При выборе **Сохранить**, автоматически выполняется через конвейер сборки. Вернуться к сводке для конвейера сборки и посмотреть состояние.

   ![Просмотр результатов](./media/vs-resource-groups-project-devops-pipelines/view-results.png)

Вы можете выбрать текущего выполняемого конвейера, чтобы просмотреть сведения о задачах. По завершении отобразятся результаты для каждого шага.

## <a name="next-steps"></a>Дальнейшие действия

Поэтапный процесс по использованию Azure конвейеров с помощью шаблонов Resource Manager см. в разделе [руководства: Непрерывная интеграция шаблонов Azure Resource Manager с конвейерами Azure](resource-manager-tutorial-use-azure-pipelines.md).
