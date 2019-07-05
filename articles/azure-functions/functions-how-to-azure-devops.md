---
title: Постоянно получать обновления кода функции с помощью DevOps в Azure
description: Узнайте, как настроить конвейер DevOps в Azure, предназначенных для функций Azure.
author: ahmedelnably
manager: jeconnoc
ms.service: azure-functions
ms.topic: conceptual
ms.date: 04/18/2019
ms.author: aelnably
ms.custom: ''
ms.openlocfilehash: 9806a982982971b1b3ac9c28454e17813b2ad2a5
ms.sourcegitcommit: 9b80d1e560b02f74d2237489fa1c6eb7eca5ee10
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/01/2019
ms.locfileid: "67479867"
---
# <a name="continuous-delivery-using-azure-devops"></a>Непрерывная доставка с помощью DevOps в Azure

Функции можно автоматически развертывать приложение функции Azure с помощью [конвейеры Azure](/azure/devops/pipelines/).
Чтобы определить свой конвейер, можно использовать:

- Yaml-файл: Этот файл описывает конвейера, может иметь в этом разделе и разделе выпуска. Файл yaml-ФАЙЛ должен находиться в том же репозитории, что и приложение.

- шаблоны: Шаблоны готовы сделать задачи, построение или развертывание приложения.

## <a name="yaml-based-pipeline"></a>Конвейер на основе YAML

### <a name="build-your-app"></a>Создание приложения

Создание приложения в конвейерах Azure зависит от языка программирования приложения.
Каждый язык имеет определенной сборки действия, чтобы создать артефакт развертывания, который может использоваться для развертывания приложения-функции в Azure.

#### <a name="net"></a>.NET

Следующий пример можно использовать для создания файла YAML для создания приложения для .NET.

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

Следующий пример можно использовать для создания файла YAML для создания приложения Python, Python поддерживается только для функций Azure Linux:

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

Следующий пример можно использовать для создания файла YAML для упаковки приложения PowerShell, PowerShell поддерживается только для функций Windows Azure:

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

В зависимости от размещения операционной системы необходимо включить приведенный ниже yaml-ФАЙЛ в файл YAML.

#### <a name="windows-function-app"></a>Приложение Windows-функция

Следующий фрагмент кода можно использовать для развертывания приложения-функции Windows

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

#### <a name="linux-function-app"></a>Приложение-функция Linux

Следующий фрагмент кода можно использовать для развертывания приложения-функции Linux

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

Шаблоны в Azure DevOps, являются предварительно определенной группы задач, которые построение или развертывание приложения.

### <a name="build-your-app"></a>Создание приложения

Создание приложения в конвейерах Azure зависит от языка программирования приложения. Каждый язык имеет определенной сборки действия, чтобы создать артефакт развертывания, который может использоваться для обновления приложения-функции в Azure.
Чтобы использовать встроенные шаблоны сборки, при создании новой сборки конвейера, выберите **использовать классический редактор** для создания конвейера с помощью конструктора шаблонов

![Azure классический редактор конвейеров](media/functions-how-to-azure-devops/classic-editor.png)

После настройки исходного кода, сборки шаблонов поиска "функции Azure" и выберите шаблон, который соответствует языку вашего приложения.

![Шаблоны сборок функций Azure](media/functions-how-to-azure-devops/build-templates.png)

В некоторых случаях артефакты сборки имеют определенную структуру папок и необходимо проверить **имени корневой папки добавленное путей к архивам** параметр.

![Добавить в начало корневую папку](media/functions-how-to-azure-devops/prepend-root-folder.png)

#### <a name="javascript-apps"></a>Приложения JavaScript

Если приложение JavaScript с зависимостями от собственных модулей Windows, необходимо обновить:

- Версия, пул агентов **размещается в VS2017**

  ![Изменение агента сборки ОС](media/functions-how-to-azure-devops/change-agent.png)

### <a name="deploy-your-app"></a>Развертывание приложения

При создании нового конвейера выпуска, найдите шаблон выпуска "функции Azure".

![](media/functions-how-to-azure-devops/release-template.png)

Развертывание в слот развертывания шаблона выпуска не поддерживается.

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Создать конвейер с Azure, с помощью Azure CLI

С помощью `az functionapp devops-pipeline create` [команда](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), конвейер Azure будет создана для сборки и выпуска любые изменения кода в репозитории. Команда создаст новый файл YAML, который определяет конвейер сборки и выпуска и фиксирует его в репозиторий. Развертывание в слот развертывания с помощью команды интерфейса командной строки Azure не поддерживается.
Предварительные требования для этой команды зависят от расположения кода:

- Если код в GitHub:

    - Необходимо иметь **записи** разрешение для вашей подписки.

    - Вы являетесь администратором проекта в Azure DevOps.

    - Имеется разрешение на создание маркера личного доступа GitHub с достаточными разрешениями. [Требования к разрешениям личный маркер доступа GitHub.](https://aka.ms/azure-devops-source-repos)

    - Вы имеете право фиксация в главной ветви в репозитории GitHub для фиксации автоматически сгенерированных yaml-файл.

- Если код находится в репозитории Azure:

    - Необходимо иметь **записи** разрешение для вашей подписки.

    - Вы являетесь администратором проекта в Azure DevOps.

## <a name="next-steps"></a>Дальнейшие действия

+ [Обзор функций Azure](functions-overview.md)
+ [Общие сведения о Azure DevOps](/azure/devops/pipelines/)
