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
ms.openlocfilehash: 86f1c36bd5f972a6ebd573019a22b0c0d07dc480
ms.sourcegitcommit: 2028fc790f1d265dc96cf12d1ee9f1437955ad87
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/30/2019
ms.locfileid: "64928097"
---
# <a name="continuous-delivery-using-azure-devops"></a>Непрерывная доставка с помощью DevOps в Azure

Функции можно автоматически развертывать приложение функции Azure с помощью [конвейеры Azure](/azure/devops/pipelines/).
Чтобы определить свой конвейер, можно использовать:

- Yaml-файл: Этот файл описывает конвейера, может иметь в этом разделе и разделе выпуска. Файл yaml-ФАЙЛ должен находиться в том же репозитории, что и приложение.

- шаблоны: Шаблоны готовы сделать задачи, построение или развертывание приложения.

## <a name="yaml-based-pipeline"></a>Конвейер на основе YAML

### <a name="build-your-app"></a>Создайте свое приложение

Создание приложения в конвейерах Azure зависит от языка программирования приложения.
Каждый язык имеет определенной сборки действия, чтобы создать артефакт развертывания, который может использоваться для развертывания приложения-функции в Azure.

#### <a name="net"></a>.NET

Следующий пример можно использовать для создания файла YAML для создания приложения для .NET.

```yaml
jobs:
  - job: Build
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="javascript"></a>JavaScript

Следующий пример можно использовать для создания файла YAML для создания приложения JavaScript:

```yaml
jobs:
  - job: Build
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
    name: 'drop'
```

#### <a name="python"></a>Python

Следующий пример можно использовать для создания файла YAML для создания приложения Python, Python поддерживается только для функций Azure Linux:

```yaml
jobs:
  - job: Build
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
    rootFolderOrFile: "$(System.DefaultWorkingDirectory)/publish_output/s"
    includeRootFolder: false
    archiveFile: "$(System.DefaultWorkingDirectory)/build$(Build.BuildId).zip"
- task: PublishBuildArtifacts@1
  inputs:
    PathtoPublish: '$(System.DefaultWorkingDirectory)/$(Build.BuildId).zip'
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
```

## <a name="template-based-pipeline"></a>Конвейер на основе шаблона

Шаблоны в Azure DevOps, являются предварительно определенной группы задач, которые построение или развертывание приложения.

### <a name="build-your-app"></a>Создайте свое приложение

Создание приложения в конвейерах Azure зависит от языка программирования приложения. Каждый язык имеет определенной сборки действия, чтобы создать артефакт развертывания, который может использоваться для обновления приложения-функции в Azure.
Чтобы использовать встроенные шаблоны сборки, при создании новой сборки конвейера, выберите **использовать классический редактор** для создания конвейера с помощью конструктора шаблонов

![Azure классический редактор конвейеров](media/functions-how-to-azure-devops/classic-editor.png)

После настройки исходного кода, сборки шаблонов поиска "функции Azure" и выберите шаблон, который соответствует языку вашего приложения.

![Шаблоны сборок функций Azure](media/functions-how-to-azure-devops/build-templates.png)

#### <a name="javascript-apps"></a>Приложения JavaScript

Если приложение JavaScript с зависимостями от собственных модулей Windows, необходимо обновить:

- Версия, пул агентов **размещается в VS2017**

  ![Изменение агента сборки ОС](media/functions-how-to-azure-devops/change-agent.png)

- Скрипт в **сборки расширений** шаг в шаблоне, чтобы `IF EXIST *.csproj dotnet build extensions.csproj --output ./bin`

  ![Скрипт изменений](media/functions-how-to-azure-devops/change-script.png)

### <a name="deploy-your-app"></a>Развертывание приложения

При создании нового конвейера выпуска, найдите шаблон выпуска "функции Azure".

![](media/functions-how-to-azure-devops/release-template.png)

## <a name="creating-an-azure-pipeline-using-the-azure-cli"></a>Создать конвейер с Azure, с помощью Azure CLI

С помощью `az functionapp devops-pipeline create` [команда](/cli/azure/functionapp/devops-pipeline#az-functionapp-devops-pipeline-create), конвейер Azure будет создана для сборки и выпуска любые изменения кода в репозитории. Команда создаст новый файл YAML, который определяет конвейер сборки и выпуска и фиксирует его в репозиторий.
Предварительные требования для этой команды зависят от расположения кода:

- Если код в GitHub:

    - Необходимо иметь **записи** разрешение для вашей подписки.

    - Вы являетесь администратором проекта в Azure DevOps.

    - Имеется разрешение на создание маркера личного доступа GitHub с достаточными разрешениями. [Требования к разрешениям личный маркер доступа GitHub.](https://aka.ms/azure-devops-source-repos)

    - Вы имеете право фиксация в главной ветви в репозитории GitHub, чтобы зафиксировать yaml-файл создан.

- Если код находится в репозитории Azure:

    - Необходимо иметь **записи** разрешение для вашей подписки.

    - Вы являетесь администратором проекта в Azure DevOps.

## <a name="next-steps"></a>Дальнейшие действия

+ [Обзор функций Azure](functions-overview.md)
+ [Общие сведения о Azure DevOps](/azure/devops/pipelines/)
