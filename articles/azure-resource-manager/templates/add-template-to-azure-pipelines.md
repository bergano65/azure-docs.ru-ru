---
title: CI/CD с Azure Pipelines и шаблонами
description: В этой статье описывается настройка непрерывной интеграции в Azure Pipelines с помощью проектов развертывания группы ресурсов Azure в Visual Studio для развертывания шаблонов диспетчер ресурсов.
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d8eff1c7efae319106eb8a85af7823a820a0da39
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "82084657"
---
# <a name="integrate-arm-templates-with-azure-pipelines"></a>Интеграция шаблонов ARM с Azure Pipelines

Visual Studio предоставляет проект группы ресурсов Azure для создания шаблонов Azure Resource Manager (ARM) и их развертывания в подписке Azure. Этот проект можно интегрировать с Azure Pipelines для непрерывной интеграции и непрерывного развертывания (CI/CD).

Существует два способа развертывания шаблонов с помощью Azure Pipelines.

* **Добавьте задачу, запускающую скрипт Azure PowerShell**. Этот вариант обеспечивает согласованность во время жизненного цикла разработки, поскольку используется тот же сценарий, который включен в проект Visual Studio (Deploy-AzureResourceGroup.ps1). Скрипт помещает артефакты из проекта в учетную запись хранения, к которой диспетчер ресурсов имеет доступ. Артефакты — это элементы в проекте, такие как связанные шаблоны, скрипты и двоичные файлы приложения. Затем сценарий развертывает шаблон.

* **Добавьте задачи для копирования и развертывания задач**. Этот параметр предлагает удобную альтернативу скрипту проекта. В конвейере настраиваются две задачи. Одна задача является стадией артефактов, а другая задача развертывает шаблон.

В этой статье показаны оба подхода.

## <a name="prepare-your-project"></a>Подготовка проекта

В этой статье предполагается, что проект Visual Studio и организация Azure DevOps готовы к созданию конвейера. Ниже показано, как обеспечить готовность к работе.

* У вас есть организация Azure DevOps. Если у вас ее нет, [создайте ее бесплатно](/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops). Если у вашей команды уже есть организация Azure DevOps, убедитесь, что вы являетесь администратором проекта Azure DevOps, который вы хотите использовать.

* Вы настроили [Подключение службы](/azure/devops/pipelines/library/connect-to-azure?view=azure-devops) к подписке Azure. Задачи в конвейере выполняются под удостоверением субъекта-службы. Действия по созданию подключения см. в разделе [Создание проекта DevOps](deployment-tutorial-pipeline.md#create-a-devops-project).

* У вас есть проект Visual Studio, созданный из стартового шаблона **группы ресурсов Azure** . Сведения о создании этого типа проекта см. в статье [Создание и развертывание групп ресурсов Azure с помощью Visual Studio](create-visual-studio-deployment-project.md).

* Проект Visual Studio [подключен к проекту Azure DevOps](/azure/devops/repos/git/share-your-code-in-git-vs-2017?view=azure-devops).

## <a name="create-pipeline"></a>Создание конвейера

1. Если вы ранее не добавили конвейер, необходимо создать новый конвейер. В Организации Azure DevOps выберите **конвейеры** и **Новый конвейер**.

   ![Добавить новый конвейер](./media/add-template-to-azure-pipelines/new-pipeline.png)

1. Укажите место хранения кода. На следующем рисунке показано, как выбрать **Azure Repos Git**.

   ![Выбор источника кода](./media/add-template-to-azure-pipelines/select-source.png)

1. В этом источнике выберите репозиторий с кодом для проекта.

   ![Выбор репозитория](./media/add-template-to-azure-pipelines/select-repo.png)

1. Выберите тип создаваемого конвейера. Можно выбрать **начальный конвейер**.

   ![Выбор конвейера](./media/add-template-to-azure-pipelines/select-pipeline.png)

Вы можете добавить задачу Azure PowerShell или скопировать файл и развернуть задачи.

## <a name="azure-powershell-task"></a>Задача Azure PowerShell

В этом разделе показано, как настроить непрерывное развертывание с помощью одной задачи, запускающей скрипт PowerShell в проекте. Следующий файл YAML создает [задачу Azure PowerShell](/azure/devops/pipelines/tasks/deploy/azure-powershell?view=azure-devops):

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

Если для задачи задано значение `AzurePowerShell@3` , конвейер использует команды из модуля AzureRM для проверки подлинности подключения. По умолчанию скрипт PowerShell в проекте Visual Studio использует модуль AzureRM. Если вы обновили скрипт для использования [модуля AZ](/powershell/azure/new-azureps-module-az), задайте для задачи значение `AzurePowerShell@4` .

```yaml
steps:
- task: AzurePowerShell@4
```

Для `azureSubscription` Укажите имя созданного подключения к службе.

```yaml
inputs:
    azureSubscription: '<your-connection-name>'
```

Для `scriptPath` укажите относительный путь от файла конвейера к скрипту. Чтобы увидеть путь, можно просмотреть репозиторий.

```yaml
ScriptPath: '<your-relative-path>/<script-file-name>.ps1'
```

Если не требуется выполнять промежуточное размещение артефактов, просто передайте имя и расположение группы ресурсов, которые будут использоваться для развертывания. Сценарий Visual Studio создает группу ресурсов, если она еще не существует.

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>'
```

Если необходимо разместить артефакты в существующей учетной записи хранения, используйте:

```yaml
ScriptArguments: -ResourceGroupName '<resource-group-name>' -ResourceGroupLocation '<location>' -UploadArtifacts -ArtifactStagingDirectory '$(Build.StagingDirectory)' -StorageAccountName '<your-storage-account>'
```

Теперь, когда вы понимаете, как создать задачу, давайте проверим действия по изменению конвейера.

1. Откройте конвейер и замените содержимое своим YAML:

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

   ![Сохранение конвейера](./media/add-template-to-azure-pipelines/save-pipeline.png)

1. Укажите сообщение для фиксации и зафиксируйте его непосредственно в **master**.

1. При нажатии на кнопку **сохранить**конвейер сборки запускается автоматически. Вернитесь к сводке для конвейера сборки и просмотрите состояние.

   ![Просмотр результатов](./media/add-template-to-azure-pipelines/view-results.png)

Вы можете выбрать выполняющийся в данный момент конвейер для просмотра сведений о задачах. По завершении вы увидите результаты для каждого шага.

## <a name="copy-and-deploy-tasks"></a>Задачи копирования и развертывания

В этом разделе показано, как настроить непрерывное развертывание с помощью двух задач для размещения артефактов и развертывания шаблона.

В следующем YAML показана [задача копирования файлов Azure](/azure/devops/pipelines/tasks/deploy/azure-file-copy?view=azure-devops).

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

Существует несколько частей этой задачи для пересмотра вашей среды. `SourcePath`Указывает расположение артефактов относительно файла конвейера. В этом примере файлы находятся в папке с именем `AzureResourceGroup1` проекта.

```yaml
SourcePath: '<path-to-artifacts>'
```

Для `azureSubscription` Укажите имя созданного подключения к службе.

```yaml
azureSubscription: '<your-connection-name>'
```

Для хранилища и имени контейнера укажите имена учетной записи хранения и контейнера, которые будут использоваться для хранения артефактов. Учетная запись хранения должна существовать.

```yaml
storage: '<your-storage-account-name>'
ContainerName: '<container-name>'
```

В следующем YAML показана [задача развертывания шаблона Azure Resource Manager](https://github.com/microsoft/azure-pipelines-tasks/blob/master/Tasks/AzureResourceManagerTemplateDeploymentV3/README.md).

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

Существует несколько частей этой задачи для пересмотра вашей среды.

- `deploymentScope`: Выберите область развертывания из параметров: `Management Group` , `Subscription` и `Resource Group` . Используйте **группу ресурсов** в этом пошаговом руководстве. Дополнительные сведения об областях см. в разделе [Области развертывания](deploy-rest.md#deployment-scope).

- `ConnectedServiceName`: Укажите имя созданного подключения к службе.

    ```yaml
    ConnectedServiceName: '<your-connection-name>'
    ```

- `subscriptionName`: Укажите идентификатор целевой подписки. Это свойство применяется только к области развертывания группы ресурсов и области развертывания подписки.

- `resourceGroupName`и `location` : укажите имя и расположение группы ресурсов, в которую требуется выполнить развертывание. Задача создает группу ресурсов, если она не существует.

    ```yaml
    resourceGroupName: '<resource-group-name>'
    location: '<location>'
    ```

Задача развертывания ссылается на шаблон с именем `WebSite.json` и файл параметров с именем WebSite.parameters.jsв. Используйте имена шаблонов и файлов параметров.

Теперь, когда вы понимаете, как создавать задачи, давайте проверим действия по изменению конвейера.

1. Откройте конвейер и замените содержимое своим YAML:

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

1. Щелкните **Сохранить**.

1. Укажите сообщение для фиксации и зафиксируйте его непосредственно в **master**.

1. При нажатии на кнопку **сохранить**конвейер сборки запускается автоматически. Вернитесь к сводке для конвейера сборки и просмотрите состояние.

   ![Просмотр результатов](./media/add-template-to-azure-pipelines/view-results.png)

Вы можете выбрать выполняющийся в данный момент конвейер для просмотра сведений о задачах. По завершении вы увидите результаты для каждого шага.

## <a name="next-steps"></a>Дальнейшие действия

Пошаговый процесс использования Azure Pipelines с шаблонами ARM см. в разделе [учебник. Непрерывная интеграция шаблонов Azure Resource Manager с Azure pipelines](deployment-tutorial-pipeline.md).
