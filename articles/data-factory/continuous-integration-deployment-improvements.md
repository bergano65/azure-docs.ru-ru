---
title: Автоматическая публикация для непрерывной интеграции и доставки
description: Узнайте, как опубликовать для непрерывной интеграции и доставки автоматически.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: nabhishek
ms.author: abnarain
ms.reviewer: maghan
manager: weetok
ms.topic: conceptual
ms.date: 02/02/2021
ms.openlocfilehash: 7b9e07c6bd2cb52858550fe5c34b2660c4d977eb
ms.sourcegitcommit: eb546f78c31dfa65937b3a1be134fb5f153447d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/02/2021
ms.locfileid: "99431286"
---
# <a name="automated-publishing-for-continuous-integration-and-delivery"></a>Автоматическая публикация для непрерывной интеграции и доставки

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Обзор

Непрерывная интеграция — это методика автоматического тестирования каждого изменения, внесенного в базу кода, и как можно раньше непрерывной поставки проходит тестирование, которое происходит во время непрерывной интеграции и передает изменения в промежуточную или рабочую систему.

В Фабрике данных Azure под непрерывными интеграцией и поставкой (CI/CD) подразумевается перемещение конвейеров Фабрики данных из одной среды (разработки, тестирования, рабочей) в другую. Фабрика данных Azure использует [шаблоны Azure Resource Manager](../azure-resource-manager/templates/overview.md) для хранения конфигурации различных сущностей Фабрики данных Azure (конвейеры, наборы данных, потоки данных и т. д.). Существуют два рекомендуемых метода перемещения фабрики данных в другую среду.

- Автоматическое развертывание с помощью интеграции фабрики данных с [Azure pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines).
- Отправка шаблона Resource Manager вручную с помощью интеграции пользовательского интерфейса Фабрики данных с Azure Resource Manager.

Дополнительные сведения см. [в статье непрерывная интеграция и доставка в фабрике данных Azure](continuous-integration-deployment.md).

В этой статье мы рассмотрим улучшения непрерывного развертывания и функцию автоматической публикации для CI/CD.

## <a name="continuous-deployment-improvements"></a>Усовершенствования непрерывного развертывания

Функция автоматической публикации принимает функции шаблона *проверить все* и экспорт *Azure Resource Manager (ARM)* из интерфейса ADF и делает логику доступной через общедоступный пакет NPM [@microsoft/azure-data-factory-utilities](https://www.npmjs.com/package/@microsoft/azure-data-factory-utilities) . Это позволяет программно запускать эти действия вместо того, чтобы открывать пользовательский интерфейс ADF и выполнять нажатие кнопки. Благодаря этому конвейер CI/CD будет работать с непрерывной интеграцией справедливо.

### <a name="current-cicd-flow"></a>Текущий поток CI/CD

1. Каждый пользователь вносит изменения в свои частные ветви.
2. Принудительная отправка в главный доступ запрещена, пользователи должны создать запрос на включение внесенных изменений.
3. Пользователи должны загрузить пользовательский интерфейс ADF и нажать кнопку Опубликовать, чтобы развернуть изменения в фабрике данных и создать шаблоны ARM в ветви Publish.
4. Конвейер выпуска DevOps настроен для создания нового выпуска и развертывания шаблона ARM каждый раз, когда новое изменение оттуда передается в ветвь Publish.

![Текущий поток CI/CD](media/continuous-integration-deployment-improvements/current-ci-cd-flow.png)

### <a name="manual-step"></a>Этот этап выполняется вручную

В текущей последовательности CI/CD UX является посредником для создания шаблона ARM, поэтому пользователь должен перейти в пользовательский интерфейс ADF и вручную нажать кнопку Опубликовать, чтобы запустить создание шаблона ARM и удалить его в ветви publish, что является следствием атаки.

### <a name="the-new-cicd-flow"></a>Новый поток CI/CD

1. Каждый пользователь вносит изменения в свои частные ветви.
2. Принудительная отправка в главный доступ запрещена, пользователи должны создать запрос на включение внесенных изменений.
3. **Сборка конвейера Azure DevOps активируется каждый раз, когда выполняется новая фиксация в Master, проверяет ресурсы и создает шаблон ARM в качестве артефакта, если проверка завершается.**
4. Конвейер выпуска DevOps настроен для создания нового выпуска и развертывания шаблона ARM каждый раз, когда будет доступна новая сборка. 

![Новый поток CI/CD](media/continuous-integration-deployment-improvements/new-ci-cd-flow.png)

### <a name="what-changed"></a>Что изменилось?

- Теперь у нас есть процесс сборки, использующий конвейер сборки DevOps.
- В конвейере сборки используется пакет Адфутилитиес NPM, который проверит все ресурсы и создаст шаблоны ARM (один и связанный шаблоны).
- Конвейер сборки будет отвечать за проверку ресурсов ADF и создание шаблона ARM вместо пользовательского интерфейса ADF (кнопка "опубликовать").
- Определение выпуска DevOps теперь будет использовать этот новый конвейер сборки вместо артефакта Git.

> [!NOTE]
> Можно продолжить использовать существующий механизм (adf_publish branch) или использовать новый поток. Поддерживаются оба варианта. 

## <a name="package-overview"></a>Основные сведения о пакете

В пакете в настоящее время доступно две команды:
- Экспорт шаблона ARM
- Проверить

### <a name="export-arm-template"></a>Экспорт шаблона ARM

Запустите NPM запустить Export <rootFolder> <factoryId> [outputFolder], чтобы экспортировать шаблон ARM, используя ресурсы указанной папки. Эта команда выполняет проверку, а также перед созданием шаблона ARM. Ниже приведен пример:

```
npm run start export C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory ArmTemplateOutput
```

- RootFolder — это обязательное поле, представляющее место расположения ресурсов фабрики данных.
- Факторид — это обязательное поле, представляющее идентификатор ресурса фабрики данных в формате: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /провидерс/Микрософт.датафактори/факториес/ <dfName> ".
- OutputFolder — это необязательный параметр, указывающий относительный путь для сохранения созданного шаблона ARM.
 
> [!NOTE]
> Созданный шаблон ARM не опубликован в `Live` версии фабрики. Развертывание следует выполнять с помощью конвейера CI/CD. 
 

### <a name="validate"></a>Проверить

Запустите NPM запустить запустить проверку <rootFolder> <factoryId> , чтобы проверить все ресурсы указанной папки. Ниже приведен пример:
    
```
npm run start validate C:\DataFactories\DevDataFactory /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/DevDataFactory
```

- RootFolder — это обязательное поле, представляющее место расположения ресурсов фабрики данных.
- Факторид — это обязательное поле, представляющее идентификатор ресурса фабрики данных в формате: "/Subscriptions/ <subId> /ResourceGroups/ <rgName> /провидерс/Микрософт.датафактори/факториес/ <dfName> ".


## <a name="create-an-azure-pipeline"></a>Создание конвейера Azure

Хотя пакеты NPM могут использоваться различными способами, одним из основных преимуществ является использование [конвейера Azure](https://nam06.safelinks.protection.outlook.com/?url=https:%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fget-started%2Fwhat-is-azure-pipelines%3Fview%3Dazure-devops%23:~:text%3DAzure%2520Pipelines%2520is%2520a%2520cloud%2Cit%2520available%2520to%2520other%2520users.%26text%3DAzure%2520Pipelines%2520combines%2520continuous%2520integration%2Cship%2520it%2520to%2520any%2520target.&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000268277%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=jo%2BkIvSBiz6f%2B7kmgqDN27TUWc6YoDanOxL9oraAbmA%3D&reserved=0). При каждом слиянии в ветви совместной работы можно активировать конвейер, который сначала проверяет весь код, а затем экспортирует шаблон ARM в [артефакт сборки](https://nam06.safelinks.protection.outlook.com/?url=https%3A%2F%2Fdocs.microsoft.com%2F%2Fazure%2Fdevops%2Fpipelines%2Fartifacts%2Fbuild-artifacts%3Fview%3Dazure-devops%26tabs%3Dyaml%23how-do-i-consume-artifacts&data=04%7C01%7Cabnarain%40microsoft.com%7C5f064c3d5b7049db540708d89564b0bc%7C72f988bf86f141af91ab2d7cd011db47%7C1%7C1%7C637423607000278113%7CUnknown%7CTWFpbGZsb3d8eyJWIjoiMC4wLjAwMDAiLCJQIjoiV2luMzIiLCJBTiI6Ik1haWwiLCJXVCI6Mn0%3D%7C1000&sdata=dN3t%2BF%2Fzbec4F28hJqigGANvvedQoQ6npzegTAwTp1A%3D&reserved=0) , который может использоваться конвейером выпуска. **Отличие от текущего процесса CI/CD заключается в том, что вы указываете конвейер выпуска на этот артефакт вместо существующей `adf_publish` ветви.**

Чтобы приступить к работе, выполните следующие действия.

1.  Откройте проект Azure DevOps и перейдите по адресу "конвейеры". Выберите "создать конвейер".

    ![Новый конвейер](media/continuous-integration-deployment-improvements/new-pipeline.png)
    
2.  Выберите репозиторий, в котором вы хотите сохранить сценарий YAML конвейера. Рекомендуется сохранить его в папке *сборки* в том же репозитории ресурсов ADF. Убедитесь, что имеется **package.js** файла в репозитории, который содержит имя пакета (как показано в примере ниже).

    ```json
    {
        "scripts":{
            "build":"node node_modules/@microsoft/azure-data-factory-utilities/lib/index"
        },
        "dependencies":{
            "@microsoft/azure-data-factory-utilities":"^0.1.2"
        }
    } 
    ```
    
3.  Выберите *начальный конвейер*. Если вы отправили или объединили файл YAML (как показано в приведенном ниже примере), можно также указать непосредственно на нем и изменить его. 

    ![Начальный конвейер](media/continuous-integration-deployment-improvements/starter-pipeline.png) 

    ```yaml
    # Sample YAML file to validate and export an ARM template into a Build Artifact
    # Requires a package.json file located in the target repository
    
    trigger:
    - main #collaboration branch
    
    pool:
      vmImage: 'ubuntu-latest'
    
    steps:
    
    # Installs Node and the npm packages saved in your package.json file in the build
    
    - task: NodeTool@0
      inputs:
        versionSpec: '10.x'
      displayName: 'Install Node.js'
    
    - task: Npm@1
      inputs:
        command: 'install'
        verbose: true
      displayName: 'Install npm package'
    
    # Validates all of the ADF resources in the repository. You will get the same validation errors as when "Validate All" is clicked
    # Enter the appropriate subscription and name for the source factory 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build validate $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName'
      displayName: 'Validate'
    
    # Validate and then generate the ARM template into the destination folder. Same as clicking "Publish" from UX
    # The ARM template generated is not published to the ‘Live’ version of the factory. Deployment should be done using a CI/CD pipeline. 
    
    - task: Npm@1
      inputs:
        command: 'custom'
        customCommand: 'run build export $(Build.Repository.LocalPath) /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/testResourceGroup/providers/Microsoft.DataFactory/factories/yourFactoryName "ArmTemplate"'
      displayName: 'Validate and Generate ARM template'
    
    # Publish the Artifact to be used as a source for a release pipeline
    
    - task: PublishPipelineArtifact@1
      inputs:
        targetPath: '$(Build.Repository.LocalPath)/ArmTemplate'
        artifact: 'ArmTemplates'
        publishLocation: 'pipeline'
    ```

4.  Введите код YAML. Рекомендуется взять файл YAML и использовать его в качестве отправной точки.
5.  Сохраните и запустите. При использовании YAML он будет срабатывать каждый раз при обновлении "Main" ветви.

## <a name="next-steps"></a>Дальнейшие действия

Дополнительные сведения о непрерывной интеграции и доставке в фабрике данных: 

- [Непрерывная интеграция и доставка в фабрике данных Azure](continuous-integration-deployment.md).
