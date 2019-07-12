---
title: Постоянно получать обновления кода функции с помощью Azure DevOps — "функции Azure"
description: Сведения о настройке конвейера DevOps в Azure, предназначенному "функции Azure".
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.openlocfilehash: 0fdad0caa2deef0d7d55b30a85632f72f4ff0ecc
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67594462"
---
# <a name="continuous-delivery-by-using-azure-devops"></a>Непрерывная поставка с помощью DevOps в Azure

Функции можно автоматически развертывать приложения функций Azure с помощью [конвейеры Azure](/azure/devops/pipelines/).

У вас есть два варианта для определения конвейера:

- **Файл YAML**: Файл YAML описание конвейера. Файл может содержать раздел действия сборки и выпуска раздела. Yaml-файл должен быть в том же репозитории, что и приложение.
- **Шаблон**: Шаблоны представляют собой готовые задачи, которые сборки или развертывания приложения.

## <a name="yaml-based-pipeline"></a>Конвейер на основе YAML

Для создания конвейера на основе YAML, сначала создайте свое приложение и затем разверните приложение.

### <a name="build-your-app"></a>Создание приложения

Как создать приложение в конвейерах Azure зависит от языка программирования для вашего приложения. Каждый язык имеет определенной сборки действия, которые создают артефакт развертывания. Артефакт развертывания используется для развертывания приложения-функции в Azure.

#### <a name="net"></a>.NET

Чтобы создать yaml-файл, чтобы создать приложение .NET, можно использовать приведенный ниже:

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
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Следующий пример можно использовать для создания файла YAML для создания приложения JavaScript:

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
    name: 'drop'
```

#### <a name="python"></a>Python

Следующий пример можно использовать для создания файла YAML для создания приложения Python. Python поддерживается только для функций Azure Linux.

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
    name: 'drop'
```
#### <a name="powershell"></a>PowerShell

Следующий пример можно использовать для создания файла YAML для упаковки приложения PowerShell. PowerShell поддерживается только для функций Windows Azure.

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
    name: 'drop'
```

### <a name="deploy-your-app"></a>Развертывание приложения

Необходимо включить один из следующих примеров YAML в файле YAML в зависимости от размещения операционной системы.

#### <a name="windows-function-app"></a>Приложения-функции Windows

Следующий фрагмент кода можно использовать для развертывания приложения-функции Windows:

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

#### <a name="linux-function-app"></a>Приложения-функции Linux

Следующий фрагмент кода можно использовать для развертывания приложения-функции Linux:

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

Шаблоны в Azure DevOps — это стандартные группы задач, сборки или развертывания приложения.

### <a name="build-your-app"></a>Создание приложения

Как создать приложение в конвейерах Azure зависит от языка программирования для вашего приложения. Каждый язык имеет определенной сборки действия, которые создают артефакт развертывания. Артефакт развертывания используется для обновления приложения-функции в Azure.

Чтобы использовать встроенные шаблоны сборки, создавая новый конвейер сборки, выберите **использовать классический редактор** для создания конвейера с помощью конструктора шаблонов.

![Выберите классический редактор конвейеров в Azure](media/functions-how-to-azure-devops/classic-editor.png)

После настройки исходного кода, шаблоны сборок для поиска для функций Azure. Выберите шаблон, который соответствует языку вашего приложения.

![Выберите шаблон сборки "функции Azure"](media/functions-how-to-azure-devops/build-templates.png)

В некоторых случаях артефакты сборки имеют определенную структуру папок. Может потребоваться выбрать **имени корневой папки добавленное путей к архивам** "флажок".

![Параметр, чтобы добавить в начало имени корневой папки](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Приложения JavaScript

Если приложение JavaScript имеет зависимость от собственных модулей Windows, необходимо обновить пуле версию агента для **размещается в VS2017**.

![Обновите версию пула агента](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Развертывание приложения

Когда вы создаете новый конвейер выпуска, найдите нужный шаблон выпуска "функции Azure".

![Найдите шаблон выпуска функций Azure](media/functions-how-to-azure-devops/release-template.png)

Развертывание в слот развертывания шаблона выпуска не поддерживается.

## <a name="create-a-build-pipeline-by-using-the-azure-cli"></a>Создайте конвейер сборки с помощью Azure CLI

Чтобы создать конвейер сборки в Azure, используйте `az functionapp devops-pipeline create` [команда](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create). Для сборки и выпуска любые изменения кода, внесенные в репозитории создается конвейером сборки. Команда создает файл YAML, определяющий конвейер сборки и выпуска и затем фиксирует ее в репозиторий. Предварительные требования для этой команды зависят от расположения кода.

- Если код в GitHub:

    - Необходимо иметь **записи** разрешений для подписки.

    - Необходимо быть администратором проекта в DevOps в Azure.

    - Необходимо иметь разрешения на создание GitHub личный маркер доступа (PAT), обладающей достаточными полномочиями. Дополнительные сведения см. в разделе [GitHub PAT требования к разрешениям.](https://aka.ms/azure-devops-source-repos)

    - Необходимо иметь разрешения для фиксации в главную ветвь в репозитории GitHub, и вы можете фиксировать автоматически сгенерированных yaml-файл.

- Если код находится в репозитории Azure:

    - Необходимо иметь **записи** разрешений для подписки.

    - Необходимо быть администратором проекта в DevOps в Azure.

## <a name="next-steps"></a>Следующие шаги

- Просмотрите [Обзор функций Azure](functions-overview.md).
- Просмотрите [Общие сведения о Azure DevOps](/azure/devops/pipelines/).
