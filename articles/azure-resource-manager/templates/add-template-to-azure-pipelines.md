---
title: CI/CD с лазурными трубопроводами и шаблонами
description: Описывает, как настроить непрерывную интеграцию в конвейеры Azure, используя проекты развертывания группы ресурсов Azure в Visual Studio для развертывания шаблонов диспетчера ресурсов.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 7617bf47595fce7baa533b0f7cc94a1803ddd349
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80153460"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Интеграция шаблонов ARM с Azure Pipelines

Visual Studio предоставляет проект группы ресурсов Azure для создания шаблонов Azure Resource Manager (ARM) и их развертывания в подписке Azure. Этот проект можно интегрировать с Azure Pipelines для непрерывной интеграции и непрерывного развертывания (CI/CD).

Развертывание шаблонов с помощью azure Pipelines— существует два способа:

* **Добавьте задачу, которая выполняет сценарий Azure PowerShell.** Преимущество этой опции заключается в обеспечении согласованности на протяжении всего жизненного цикла разработки, поскольку используется тот же скрипт, который включен в проект Visual Studio (Deploy-AzureResourceGroup.ps1). Скрипт этапирует артефакты из проекта в учетную запись хранения, к которым может получить доступ менеджер ресурсов. Артефакты — это элементы проекта, такие как связанные шаблоны, скрипты и файлы приложений. Затем скрипт развертывает шаблон.

* **Добавление задач для копирования и развертывания задач.** Эта опция предлагает удобную альтернативу сценарию проекта. Вы настраиваете две задачи в конвейере. Одна задача ставит артефакты, а другая задача развертывает шаблон.

В этой статье показаны оба подхода.

## <a name="prepare-your-project"></a>Подготовка проекта

Эта статья предполагает, что ваш проект Visual Studio и организация Azure DevOps готовы к созданию конвейера. Следующие шаги показывают, как убедиться, что вы готовы:

* У вас есть организация Azure DevOps. Если у вас его нет, [создайте его бесплатно.](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops) Если в вашей команде уже есть организация Azure DevOps, убедитесь, что вы являетесь администратором проекта Azure DevOps, который вы хотите использовать.

* Вы настроили [подключение службы](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) к подписке Azure. Задачи в конвейере выполняются под идентификацией директора службы. Для шагов по созданию соединения [см.](template-tutorial-use-azure-pipelines.md#create-a-devops-project)

* У вас есть проект Visual Studio, созданный на основе шаблона стартера **Группы ресурсов Azure.** Для получения информации о создании этого типа проекта [см. Создание и развертывание групп ресурсов Azure через Visual Studio.](create-visual-studio-deployment-project.md)

* Проект Visual Studio [подключен к проекту Azure DevOps.](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops)

## <a name="create-pipeline"></a>Создание конвейера

1. Если ранее конвейер не добавлял, необходимо создать новый конвейер. Из организации Azure DevOps выберите **трубопроводы** и **новый конвейер.**

   ![Добавление нового конвейера](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Укажите, где хранится ваш код. На следующем изображении показан выбор **Azure Repos Git.**

   ![Выберите источник кода](./media/add-template-to-azure-pipelines/select-source.png)

1. Из этого источника выберите репозиторий с кодом для проекта.

   ![Выберите репозиторий](./media/add-template-to-azure-pipelines/select-repo.png)

1. Выберите тип конвейера для создания. Вы можете выбрать **конвейер Starter.**

   ![Выберите конвейер](./media/add-template-to-azure-pipelines/select-pipeline.png)

Вы готовы либо добавить задачу Azure PowerShell, либо файл копирования и задачи развертывания.

## <a name="azure-powershell-task"></a>Задача Azure PowerShell

В этом разделе показано, как настроить непрерывное развертывание с помощью одной задачи, которая выполняет сценарий PowerShell в проекте. Следующий файл YAML создает [задачу Azure PowerShell:](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops)

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

При выполнении задачи `AzurePowerShell@3`конвейер использует команды из модуля AzureRM для проверки подлинности соединения. По умолчанию в скрипте PowerShell в проекте Visual Studio используется модуль AzureRM. Если вы обновили свой скрипт для использования [модуля Az,](/powershell/azure/new-azureps-module-az)установите задачу. `AzurePowerShell@4`

```yaml
steps:
- task: AzurePowerShell@4
```

Для, `azureSubscription`укажите имя соединения обслуживания вы создали.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Для `scriptPath`этого укажите относительный путь от файла конвейера к скрипту. Вы можете заглянуть в репозиторий, чтобы увидеть путь.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Если вам не нужно ставить артефакты, просто передайте имя и местоположение группы ресурсов для развертывания. Скрипт Visual Studio создает группу ресурсов, если она еще не существует.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Если необходимо поставить артефакты в существующую учетную запись хранения, используйте:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Теперь, когда вы понимаете, как создать задачу, давайте пройдем через шаги по отсечению конвейера.

1. Откройте конвейер и замените содержимое yAML:

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

1. Нажмите кнопку **Сохранить**.

   ![Сохранение конвейера](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Предоставьте сообщение для коммита и совершите непосредственно **освоить.**

1. При выборе **Сохранить**автоматически запускается конвейер сборки. Вернитесь к сводке для конвейера сборки и наблюдайте за состоянием.

   ![Просмотр результатов](./media/add-template-to-azure-pipelines/view-results.png)

Можно выбрать работающий в настоящее время конвейер, чтобы узнать подробности о задачах. Когда он заканчивается, вы видите результаты для каждого шага.

## <a name="copy-and-deploy-tasks"></a>Копирование и развертывание задач

В этом разделе показано, как настроить непрерывное развертывание с помощью двух задач для постановки артефактов и развертывания шаблона.

На следующем YAML [отображается задача копирования файла Azure:](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops)

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

Есть несколько частей этой задачи, которую необходимо пересмотреть для вашей среды. Указывает `SourcePath` расположение артефактов относительно файла конвейера. В этом примере файлы существуют `AzureResourceGroup1` в папке с именем, которая была названа проектом.

```yaml
SourcePath: '<path-to-artifacts>'
```

Для, `azureSubscription`укажите имя соединения обслуживания вы создали.

```yaml
azureSubscription: '<your-connection-name>'
```

Для хранения и названия контейнера укажите имена учетной записи хранения и контейнера, который вы хотите использовать для хранения артефактов. Учетная запись хранилища должна существовать.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

Следующее YAML показывает [задачу развертывания шаблона шаблона управления ресурсами Azure:](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md)

```yaml
- task: AzureResourceGroupDeployment@2
  displayName: 'Deploy template'
  inputs:
    deploymentScope: 'Resource Group'
    ConnectedServiceName: 'demo-deploy-sp'
    subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
    action: 'Create Or Update Resource Group'
    resourceGroupName: 'demogroup'
    location: 'Central US'
    templateLocation: 'URL of the file'
    csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
    csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
    overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
    deploymentMode: 'Incremental'
```

Есть несколько частей этой задачи, которую необходимо пересмотреть для вашей среды.

- `deploymentScope`: Выберите область развертывания из `Management Group` `Subscription` вариантов: и `Resource Group`. Используйте **группу ресурсов** в этой прогулке. Дополнительные сведения об областях см. в разделе [Области развертывания](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Укажите имя созданного вами подключения к службе.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Предоставьте идентификатор целевой подписки. Это свойство применяется только к области развертывания группы ресурсов и области развертывания подписки.

- `resourceGroupName`и: `location`укажите имя и местоположение группы ресурсов, в какую группу вы хотите развернуть. Задача создает группу ресурсов, если она не существует.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Задача развертывания связывается с `WebSite.json` названным шаблоном и файлом параметров под названием WebSite.parameters.json. Используйте имена шаблонов и файлов параметров.

Теперь, когда вы понимаете, как создавать задачи, давайте пройдем через шаги по отсечению конвейера.

1. Откройте конвейер и замените содержимое yAML:

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
        deploymentScope: 'Resource Group'
        ConnectedServiceName: 'demo-deploy-sp'
        subscriptionName: '01234567-89AB-CDEF-0123-4567890ABCDEF'
        action: 'Create Or Update Resource Group'
        resourceGroupName: 'demogroup'
        location: 'Central US'
        templateLocation: 'URL of the file'
        csmFileLink: '$(artifactsLocation)WebSite.json$(artifactsLocationSasToken)'
        csmParametersFileLink: '$(artifactsLocation)WebSite.parameters.json$(artifactsLocationSasToken)'
        overrideParameters: '-_artifactsLocation $(artifactsLocation) -_artifactsLocationSasToken "$(artifactsLocationSasToken)"'
        deploymentMode: 'Incremental'
   ```

1. Нажмите кнопку **Сохранить**.

1. Предоставьте сообщение для коммита и совершите непосредственно **освоить.**

1. При выборе **Сохранить**автоматически запускается конвейер сборки. Вернитесь к сводке для конвейера сборки и наблюдайте за состоянием.

   ![Просмотр результатов](./media/add-template-to-azure-pipelines/view-results.png)

Можно выбрать работающий в настоящее время конвейер, чтобы узнать подробности о задачах. Когда он заканчивается, вы видите результаты для каждого шага.

## <a name="next-steps"></a>Дальнейшие действия

Для [поэтапного](template-tutorial-use-azure-pipelines.md)процесса использования трубопроводов Azure с шаблонами ARM см.
