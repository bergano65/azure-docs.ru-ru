---
title: Непрерывное обновление кода приложения функции с помощью Azure DevOps
description: Узнайте, как настроить конвейер Azure DevOps, нацеленный на функции Azure.
author: ahmedelnably
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 1358ac667903e5a1a3f00e4f069a448f0cfdc8f7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 12/28/2019
ms.locfileid: "75531587"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Непрерывная поставка с помощью Azure DevOps

Вы можете автоматически развернуть функцию в приложении "функции Azure" с помощью [Azure pipelines](/azure/devops/pipelines/).

Существует два варианта определения конвейера:

- **Файл YAML**: файл YAML описывает конвейер. Файл может содержать раздел шаги сборки и раздел выпуска. Файл YAML должен находиться в том же репозитории, что и приложение.
- **Шаблон**. шаблоны — это готовые задачи, которые создают или развертывают приложение.

## <a name="yaml-based-pipeline"></a>Конвейер на основе YAML

Чтобы создать конвейер на основе YAML, сначала выполните сборку приложения, а затем разверните приложение.

### <a name="build-your-app"></a>Создайте свое приложение

Создание приложения в Azure Pipelines зависит от языка программирования приложения. Каждый язык имеет определенные шаги сборки, которые создают артефакт развертывания. Артефакт развертывания используется для развертывания приложения-функции в Azure.

#### <a name="net"></a>.NET

Чтобы создать файл YAML для создания приложения .NET, можно использовать следующий пример:

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- script: |
    dotnet restore
    dotnet build --configuration Release
- task: DotNetCoreCLI@2
  inputs:
    command: publish
    arguments: '--configuration Release --output publish_output'
    projects: '*.csproj'
    publishWebProjects: false
    modifyOutputPath: true
    zipAfterPublish: false
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

#### <a name="javascript"></a>JavaScript

Чтобы создать файл YAML для создания приложения JavaScript, можно использовать следующий пример:

```yaml
pool:
      vmImage: ubuntu-16.04 # Use 'VS2017-Win2016' if you have Windows native +Node modules
steps:
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    npm install 
    npm run build --if-present
    npm prune --production
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

#### <a name="python"></a>Python

Следующий пример можно использовать для создания файла YAML для создания приложения Python. Python поддерживается только для функций Linux Azure. YAML для Python 3,7 можно создать, заменив все экземпляры 3,6 на 3,7 в этом YAML.

```yaml
pool:
      vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.6 as required by functions"
  inputs:
    versionSpec: '3.6'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    python3.6 -m venv worker_venv
    source worker_venv/bin/activate
    pip3.6 install setuptools
    pip3.6 install -r requirements.txt
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```
#### <a name="powershell"></a>PowerShell

Чтобы создать файл YAML для упаковки приложения PowerShell, можно использовать следующий пример. PowerShell поддерживается только для функций Windows Azure.

```yaml
pool:
      vmImage: 'VS2017-Win2016'
steps:
- task: ArchiveFiles@2
  displayName: "Archive files"
  inputs:
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip'
    artifactName: 'drop'
```

### <a name="deploy-your-app"></a>Развертывание приложения

В зависимости от ОС размещения в файл YAML необходимо включить один из следующих примеров YAML.

#### <a name="windows-function-app"></a>Приложение функции Windows

Для развертывания приложения-функции Windows можно использовать следующий фрагмент кода:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionApp
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

#### <a name="linux-function-app"></a>Приложение функции Linux

Для развертывания приложения-функции Linux можно использовать следующий фрагмент:

```yaml
steps:
- task: AzureFunctionApp@1
  inputs:
    azureSubscription: '<Azure service connection>'
    appType: functionAppLinux
    appName: '<Name of function app>'
    #Uncomment the next lines to deploy to a deployment slot
    #Note that deployment slots is not supported for Linux Dynamic SKU
    #deployToSlotOrASE: true
    #resourceGroupName: '<Resource Group Name>'
    #slotName: '<Slot name>'
```

## <a name="template-based-pipeline"></a>Конвейер на основе шаблона

Шаблоны в Azure DevOps — это стандартные группы задач, которые создают или развертывают приложение.

### <a name="build-your-app"></a>Создайте свое приложение

Создание приложения в Azure Pipelines зависит от языка программирования приложения. Каждый язык имеет определенные шаги сборки, которые создают артефакт развертывания. Артефакт развертывания используется для обновления приложения-функции в Azure.

Чтобы использовать встроенные шаблоны сборки, при создании нового конвейера сборки выберите **использовать классический редактор** для создания конвейера с помощью шаблонов конструктора.

![Выберите Azure Pipelines классический редактор.](media/functions-how-to-azure-devops/classic-editor.png)

После настройки источника кода найдите шаблоны сборок службы "функции Azure". Выберите шаблон, соответствующий языку приложения.

![Выбор шаблона сборки функций Azure](media/functions-how-to-azure-devops/build-templates.png)

В некоторых случаях артефакты сборки имеют определенную структуру папок. Может потребоваться установить флажок **имя корневой папки в начале для архивных путей** .

![Параметр, позволяющий добавить имя корневой папки в начало](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Приложения JavaScript

Если приложение JavaScript зависит от собственных модулей Windows, необходимо обновить версию пула агентов до **HOSTED VS2017**.

![Обновление версии пула агентов](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Развертывание приложения

При создании нового конвейера выпуска выполните поиск шаблона выпуска функций Azure.

![Поиск шаблона выпуска функций Azure](media/functions-how-to-azure-devops/release-template.png)

Развертывание в слот развертывания не поддерживается в шаблоне выпуска.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Создание конвейера сборки с помощью Azure CLI

Чтобы создать конвейер сборки в Azure, используйте [команду](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create)`az functionapp devops-pipeline create`. Конвейер сборки создается для сборки и освобождения всех изменений кода, внесенных в репозиторий. Команда создает новый файл YAML, который определяет конвейер сборки и выпуска, а затем фиксирует его в репозитории. Необходимые условия для этой команды зависят от расположения кода.

- Если ваш код находится на GitHub:

    - У вас должны быть разрешения на **запись** для своей подписки.

    - Вы должны быть администратором проекта в Azure DevOps.

    - Необходимо иметь разрешения на создание личного маркера доступа GitHub (PAT) с достаточными разрешениями. Дополнительные сведения см. в разделе [требования к разрешениям GITHUB Pat.](https://aka.ms/azure-devops-source-repos)

    - Необходимо иметь разрешения на фиксацию в главной ветви в репозитории GitHub, чтобы можно было зафиксировать автоматически созданный файл YAML.

- Если ваш код находится в Azure Repos:

    - У вас должны быть разрешения на **запись** для своей подписки.

    - Вы должны быть администратором проекта в Azure DevOps.

## <a name="next-steps"></a>Дальнейшие действия

- Ознакомьтесь с [обзором функций Azure](functions-overview.md).
- Ознакомьтесь с [обзором Azure DevOps](/azure/devops/pipelines/).
