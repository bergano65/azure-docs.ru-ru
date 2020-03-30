---
title: Постояннообновляйте код приложения функции с помощью Azure DevOps
description: Узнайте, как настроить конвейер Azure DevOps, нацеленный на функции Azure.
author: craigshoemaker
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: cshoe
ms.openlocfilehash: 5e2fc8fb06248e2cdad9067c56647da6d9626b50
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255751"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Непрерывная доставка с помощью Azure DevOps

Функцию можно автоматически развернуть в приложении Azure Functions с помощью [Azure Pipelines.](/azure/devops/pipelines/)

У вас есть два варианта определения конвейера:

- **Файл YAML:** файл YAML описывает конвейер. Файл может иметь раздел шагов сборки и раздел выпуска. Файл YAML должен находиться в том же репо, что и приложение.
- **Шаблон:** Шаблоны — это готовые задачи, которые создают или развертывают ваше приложение.

## <a name="yaml-based-pipeline"></a>Конвейер на основе YAML

Чтобы создать конвейер на основе YAML, сначала создайте приложение, а затем разместите приложение.

### <a name="build-your-app"></a>Создание приложения

Способ построения приложения в Azure Pipelines зависит от языка программирования приложения. Каждый язык имеет определенные шаги сборки, которые создают артефакт развертывания. Артефакт развертывания используется для развертывания приложения функции в Azure.

# <a name="c"></a>[C\#](#tab/csharp)

Вы можете использовать следующий пример для создания файла YAML для создания приложения .NET:

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
    modifyOutputPath: false
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

# <a name="javascript"></a>[Javascript](#tab/javascript)

Вы можете использовать следующий пример для создания файла YAML для создания приложения JavaScript:

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

# <a name="python"></a>[Python](#tab/python)

Вы можете использовать один из следующих образцов для создания файла YAML для создания приложения для конкретной версии Python. Python поддерживается только для функциональных приложений, работающих на Linux.

**Версия 3.7**

```yaml
pool:
  vmImage: ubuntu-16.04
steps:
- task: UsePythonVersion@0
  displayName: "Setting python version to 3.7 as required by functions"
  inputs:
    versionSpec: '3.7'
    architecture: 'x64'
- bash: |
    if [ -f extensions.csproj ]
    then
        dotnet build extensions.csproj --output ./bin
    fi
    pip install --target="./.python_packages/lib/site-packages" -r ./requirements.txt
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

**Версия 3.6**

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
    pip install --target="./.python_packages/lib/python3.6/site-packages" -r ./requirements.txt
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

# <a name="powershell"></a>[PowerShell](#tab/powershell)

Вы можете использовать следующий пример для создания файла YAML для упаковки приложения PowerShell. PowerShell поддерживается только для функций Windows Azure.

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

---

### <a name="deploy-your-app"></a>Развертывание приложения

Вы должны включить один из следующих образцов YAML в файл YAML, в зависимости от хостинговой ОС.

#### <a name="windows-function-app"></a>Функциональное приложение Windows

Для развертывания приложения функции Windows можно использовать следующий фрагмент:

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

#### <a name="linux-function-app"></a>Функциональное приложение Linux

Вы можете использовать следующий фрагмент для развертывания приложения функции Linux:

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

## <a name="template-based-pipeline"></a>Конвейер на основе шаблонов

Шаблоны в Azure DevOps являются предопределенными группами задач, которые создают или развертывают приложение.

### <a name="build-your-app"></a>Создание приложения

Способ построения приложения в Azure Pipelines зависит от языка программирования приложения. Каждый язык имеет определенные шаги сборки, которые создают артефакт развертывания. Артефакт развертывания используется для обновления приложения функции в Azure.

Чтобы использовать встроенные шаблоны сборки при создании нового конвейера сборки, выберите **Используйте классический редактор** для создания конвейера с помощью шаблонов конструкторов.

![Выберите классический редактор Azure Pipelines](media/functions-how-to-azure-devops/classic-editor.png)

После настройки источника кода ищите шаблоны сборки функций Azure. Выберите шаблон, который соответствует языку приложения.

![Выберите шаблон сборки функций Azure](media/functions-how-to-azure-devops/build-templates.png)

В некоторых случаях артефакты сборки имеют определенную структуру папок. Возможно, вам придется выбрать **имя корневой папки Prepend для проверки путей архива.**

![Возможность подготовки имени корневой папки](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>JavaScript приложения

Если приложение JavaScript имеет зависимость от нативных модулей Windows, необходимо обновить версию **пула агентов до хостинга VS2017.**

![Обновление версии пула агентов](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Развертывание приложения

При создании нового конвейера выпуска ищите шаблон выпуска Azure Functions.

![Поиск шаблона выпуска функций Azure](media/functions-how-to-azure-devops/release-template.png)

Развертывание в слот развертывания не поддерживается в шаблоне выпуска.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Создание конвейера сборки с помощью Azure CLI

Для создания конвейера сборки в `az functionapp devops-pipeline create` Azure используйте [команду](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Конвейер сборки создан для создания и выпуска любых изменений кода, внесенных в репо. Команда генерирует новый файл YAML, который определяет конвейер сборки и выпуска, а затем фиксирует его в репо. Предпосылки для этой команды зависят от местоположения кода.

- Если ваш код находится в GitHub:

    - Вы должны иметь разрешения на **написание** подписки.

    - Вы должны быть администратором проекта в Azure DevOps.

    - Вы должны иметь разрешения на создание токена личного доступа GitHub (PAT), который имеет достаточное количество разрешений. Для получения дополнительной [GitHub PAT permission requirements.](https://aka.ms/azure-devops-source-repos) информации см.

    - Вы должны иметь разрешения на фиксацию в основной ветви в репозитории GitHub, чтобы вы могли совершить авторизажированный файл YAML.

- Если ваш код находится в Azure Repos:

    - Вы должны иметь разрешения на **написание** подписки.

    - Вы должны быть администратором проекта в Azure DevOps.

## <a name="next-steps"></a>Дальнейшие действия

- Просмотрите [обзор функций Azure](functions-overview.md).
- Просмотрите [обзор Azure DevOps](/azure/devops/pipelines/).
