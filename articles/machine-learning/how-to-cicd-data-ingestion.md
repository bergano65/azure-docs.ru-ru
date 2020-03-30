---
title: DevOps для конвейера по проглатке данных
titleSuffix: Azure Machine Learning
description: Узнайте, как применять методы DevOps в реализации конвейера приема данных, используемой для подготовки данных для обучения модели.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: conceptual
ms.author: iefedore
author: eedorenko
manager: davete
ms.reviewer: larryfr
ms.date: 01/30/2020
ms.openlocfilehash: d987171d41bd6d80bab4cce91ef9ecec1f0dc7a4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80247186"
---
# <a name="devops-for-a-data-ingestion-pipeline"></a>DevOps для конвейера по проглатке данных

В большинстве сценариев решение для приема данных представляет собой состав скриптов, вызовов служб и конвейер, организующий все действия. В этой статье вы узнаете, как применять методы DevOps к жизненному циклу разработки общего конвейера приема данных. Конвейер готовит данные для обучения модели машинного обучения.

## <a name="the-solution"></a>Решение

Рассмотрим следующий рабочий процесс приема данных:

![данные-проглатывания-конвейер](media/how-to-cicd-data-ingestion/data-ingestion-pipeline.png)

В этом подходе обучающие данные хранятся в хранилище Azure blob. Конвейер Azure Data Factory изыскирует данные из контейнера ввода, преобразует их и сохраняет в контейнер выходной капли. Этот контейнер служит [хранилищем данных](concept-data.md) для службы машинного обучения Azure. После подготовки данных конвейер Data Factory ссылается на конвейер обучения машинного обучения для подготовки модели. В данном конкретном примере преобразование данных осуществляется блокнотом Python, работающим в кластере Azure Databricks. 

## <a name="what-we-are-building"></a>Что мы строим

Как и в любом программном решении, над ним работает команда (например, Data Engineers). 

![cicd-данные-ingestion](media/how-to-cicd-data-ingestion/cicd-data-ingestion.png)

Они сотрудничают и совместно размнают те же ресурсы Azure, такие как Azure Data Factory, Azure Databricks, учетная запись хранения Azure и такие. Сбор этих ресурсов является средой разработки. Инженеры по обработке данных вносят свой вклад в ту же базу исходного кода. Процесс непрерывной интеграции собирает код, проверяет его с помощью тестов качества кода, модульных тестов и создает артефакты, такие как проверенный код и шаблоны Azure Resource Manager. Непрерывный процесс доставки развертывает артефакты в среды ниже по течению. В этой статье показано, как автоматизировать процессы CI и CD с помощью [Azure Pipelines.](https://azure.microsoft.com/services/devops/pipelines/)

## <a name="source-control-management"></a>Управление управлением исходом

Члены группы работают несколько иными способами, чтобы объединиться с исходным кодом ноутбука Python и исходным кодом Azure Data Factory. Однако в обоих случаях код хранится в репозитории управления исходным кодом (например, Azure DevOps, GitHub, GitLab), и совместнотабитор обычно основан на некоторой модели ветвления (например, [GitFlow).](https://datasift.github.io/gitflow/IntroducingGitFlow.html)

### <a name="python-notebook-source-code"></a>Python Ноутбук Исходный код

Инженеры по обработке данных работают с исходным кодом ноутбука Python либо локально в IDE (например, [Visual Studio Code),](https://code.visualstudio.com)либо непосредственно в рабочем пространстве Databricks. Последнее дает возможность отладить код в среде разработки. В любом случае код будет объединен в репозиторий после политики ветвления. Настоятельно рекомендуется хранить код в `.py` файлах, `.ipynb` а не в формате ноутбука Jupyter. Это улучшает читаемость кода и позволяет автоматически проверять качество кода в процессе CI.

### <a name="azure-data-factory-source-code"></a>Исходный код фабрики данных Azure

Исходный код конвейеров Azure Data Factory представляет собой набор файлов json, генерируемых рабочим пространством. Обычно инженеры по обработке данных работают с визуальным конструктором в рабочей области Azure Data Factory, а не с файлами исходного кода напрямую. Наработайте рабочее пространство с репозиторием управления исходным элементом, как это описано в [документации Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/source-control#author-with-azure-repos-git-integration) С этой конфигурацией инженеры данных могут совместно работать над исходным кодом после предпочтительного ветвления рабочего процесса.    

## <a name="continuous-integration-ci"></a>Непрерывная интеграция (CI)

Конечная цель процесса непрерывной интеграции состоит в том, чтобы собрать совместную работу группы из исходного кода и подготовить ее к развертыванию в среды ниже по течению. Как и в управлении исходным кодом, этот процесс отличается для блокнотов Python и конвейеров Azure Data Factory. 

### <a name="python-notebook-ci"></a>Python Ноутбук CI

Процесс CI для ноутбуков Python получает код из ветви совместной работы (например, ***мастер*** или ***разрабатывает)*** и выполняет следующие действия:
* Код линтинг
* Модульное тестирование
* Сохранение кода как артефакта

Следующий фрагмент кода демонстрирует реализацию этих шагов в конвейере Azure DevOps ***yaml:***

```yaml
steps:
- script: |
   flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
  workingDirectory: '$(Build.SourcesDirectory)'
  displayName: 'Run flake8 (code style analysis)'  
  
- script: |
   python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
  displayName: 'Run unit tests'

- task: PublishTestResults@2
  condition: succeededOrFailed()
  inputs:
    testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
    testRunTitle: 'Linting & Unit tests'
    failTaskOnFailedTests: true
  displayName: 'Publish linting and unit test results'

- publish: $(Build.SourcesDirectory)
    artifact: di-notebooks

```

Конвейер использует ***flake8*** для сделать код Python linting. Он выполняет модульные тесты, определенные в исходном коде, и публикует результаты анализа и проверки, чтобы они были доступны на экране выполнения провода Azure:

![линтинг-модуль-тесты](media/how-to-cicd-data-ingestion/linting-unit-tests.png)

Если тестирование линьки и единицы успешно, конвейер скопирует исходный код в репозиторий артефактов, который будет использоваться последующими шагами развертывания.

### <a name="azure-data-factory-ci"></a>Фабрика данных Azure CI

Процесс CI для конвейера Azure Data Factory является узким местом во всей истории CI/CD для конвейера по сбору данных. Нет ***никакой постоянной*** интеграции. Развертываемый артефакт для фабрики данных Azure — это набор шаблонов управления ресурсами Azure. Единственный способ создать эти шаблоны — нажать кнопку ***«Публикация»*** в рабочем пространстве Фабрики данных Azure. Здесь нет автоматизации.
Инженеры по обработке данных объединяют исходный код из ветвей функций в ветвь совместной работы, например, ***осваивают*** или ***разрабатывают.*** Затем кто-то с предоставленными разрешениями нажимает кнопку ***публикации*** для генерации шаблонов менеджера ресурсов Azure из исходного кода в отрасли совместной работы. При нажатии кнопки рабочее пространство проверяет конвейеры (думаю, что она будет проходить по средством тестирования и модульного тестирования), генерирует шаблоны Azure Resource Manager (думайте о нем по мере создания) и сохраняет генерируемые шаблоны в технической ветви ***adf_publish*** в том же репозитории кода (думаю о нем как о публикации артефактов). Эта ветвь создается автоматически рабочим пространством Фабрики данных Azure. Этот процесс подробно описан в [документации Фабрики данных Azure.](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)

Важно убедиться, что генерируемые шаблоны Менеджера ресурсов Azure являются агностиками среды. Это означает, что все значения, которые могут отличаться от между средами, параметризованы. Фабрика данных Azure достаточно умна, чтобы предоставить большинство таких значений, как параметры. Например, в следующем шаблоне свойства подключения к рабочему пространству машинного обучения Azure разоблачаются в качестве параметров:

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        "AzureMLService_servicePrincipalKey": {
            "value": ""
        },
        "AzureMLService_properties_typeProperties_subscriptionId": {
            "value": "0fe1c235-5cfa-4152-17d7-5dff45a8d4ba"
        },
        "AzureMLService_properties_typeProperties_resourceGroupName": {
            "value": "devops-ds-rg"
        },
        "AzureMLService_properties_typeProperties_servicePrincipalId": {
            "value": "6e35e589-3b22-4edb-89d0-2ab7fc08d488"
        },
        "AzureMLService_properties_typeProperties_tenant": {
            "value": "72f988bf-86f1-41af-912b-2d7cd611db47"
        }
    }
}
```

Однако может потребоваться разоблачить пользовательские свойства, которые не обрабатываются рабочей областью Фабрики данных Azure по умолчанию. В сценарии этой статьи конвейер Azure Data Factory вызывает блокнот Python, обрабатывая данные. Ноутбук принимает параметр с именем файла данных ввода.

```Python
import pandas as pd
import numpy as np

data_file_name = getArgument("data_file_name")
data = pd.read_csv(data_file_name)

labels = np.array(data['target'])
...
```

Это название отличается для ***Дев***, ***ЗА***, ***UAT***, и ***PROD*** средах. В сложном конвейере с несколькими действиями может быть несколько пользовательских свойств. Это хорошая практика, чтобы собрать все эти значения в одном месте и определить их как ***переменные трубопровода:***

![adf-переменные](media/how-to-cicd-data-ingestion/adf-variables.png)

Действия конвейера могут относиться к переменным конвейера, фактически используя их:

![adf-ноутбук-параметры](media/how-to-cicd-data-ingestion/adf-notebook-parameters.png)

Рабочее пространство Фабрики данных Azure ***не*** разоблачает переменные конвейера, как параметры шаблонов azure Resource Manager по умолчанию. В рабочей области используется [шаблон параметризации по умолчанию,](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) диктующий, какие свойства конвейера должны подвергаться параметрам шаблона Azure Resource Manager. Чтобы добавить переменные конвейера в список, обновите раздел "Microsoft.DataFactory/фабрики/трубопроводы" [шаблона параметризации по умолчанию](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#default-parameterization-template) со следующим фрагментом и поместите файл результата json в корень исходной папки:

```json
"Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "variables": {
                "*": {
                    "defaultValue": "="
                }
            }
        }
    }
```

Это заставит рабочее пространство Фабрики данных Azure добавить переменные в список параметров при нажатии кнопки ***публикации:***

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "factoryName": {
            "value": "devops-ds-adf"
        },
        ...
        "data-ingestion-pipeline_properties_variables_data_file_name_defaultValue": {
            "value": "driver_prediction_train.csv"
        }        
    }
}
```

Значения в файле json — это значения по умолчанию, настроенные в определении конвейера. Ожидается, что при развертывании шаблона ресурсов Azure Manager они будут переопределены значениями целевой среды.

## <a name="continuous-delivery-cd"></a>Непрерывная доставка (CD)

Непрерывная доставка переносит артефакты и развертывает их в первую целевую среду. Это гарантирует, что решение работает при проведении тестов. В случае успеха, он продолжает в следующую среду. Конвейер CD Azure состоит из нескольких этапов, представляющих среды. Каждый этап содержит [развертывания](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) и [задания,](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) выполняющие следующие действия:
* Развертывание ноутбука Python в рабочее пространство Azure Databricks
* Развертывание конвейера фабрики данных Azure 
* Запуск конвейера
* Проверка результата приема данных

Этапы конвейера могут быть настроены с [утверждениями](https://docs.microsoft.com/azure/devops/pipelines/process/approvals?view=azure-devops&tabs=check-pass) и [воротами,](https://docs.microsoft.com/azure/devops/pipelines/release/approvals/gates?view=azure-devops) которые обеспечивают дополнительный контроль за тем, как процесс развертывания развивается через цепочку сред.

### <a name="deploy-a-python-notebook"></a>Развертывание ноутбука Python

Следующий фрагмент кода определяет [развертывание](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) azure Pipeline, которое копирует блокнот Python в кластер Databricks:

```yaml
- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'       
```            

Артефакты, производимые CI, автоматически копируются агенту развертывания и доступны в папке ***$(Pipeline.Workspace).*** В этом случае задача развертывания относится к артефакту ***ди-ноутбуков,*** содержащему блокнот Python. Это [развертывание](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) использует [расширение Databricks Azure DevOps](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks) для копирования файлов ноутбуков в рабочее пространство Databricks.
Этап ***Deploy_to_QA*** содержит ссылку на переменную группу ***devops-ds-qa-vg,*** определяемую в проекте Azure DevOps. Шаги на этом этапе относятся к переменным из этой переменной группы (например, $(DATABRICKS_URL), $(DATABRICKS_TOKEN)). Идея заключается в том, что следующий этап (например, ***Deploy_to_UAT)*** будет работать с теми же переменными именами, определенными в его собственной переменной группе, с которой разобьётся UAT.

### <a name="deploy-an-azure-data-factory-pipeline"></a>Развертывание конвейера фабрики данных Azure

Развертываемый артефакт для фабрики данных Azure — это шаблон управления ресурсами Azure. Таким образом, он будет развернут с задачей ***развертывания группы ресурсов Azure,*** как это показано в следующем фрагменте:

```yaml
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
```
Значение параметра файла данных происходит от переменной $(DATA_FILE_NAME), определяемой в группе переменных этапов. Аналогичным образом, все параметры, определенные в ***ARMTemplateForFactory.json*** могут быть переопределены. Если это не так, то используются значения по умолчанию.

### <a name="run-the-pipeline-and-check-the-data-ingestion-result"></a>Запустите конвейер и проверьте результат приема данных

Следующим шагом является обеспечение работы развернутого решения. Следующее определение задания выполняет конвейер Azure Data Factory со [скриптом PowerShell](https://github.com/microsoft/DataOps/tree/master/adf/utils) и выполняет блокнот Python в кластере Azure Databricks. Ноутбук проверяет, правильно ли были проглочены данные, и проверяет файл данных результата с именем $(bin_FILE_NAME).

```yaml
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'
```

Заключительная задача в задании проверяет результат выполнения ноутбука. При возврате ошибки состояние выполнения конвейера приводит к ошибке.

## <a name="putting-pieces-together"></a>Свод кусков вместе

Результатом данной статьи является конвейер CI/CD Azure, который состоит из следующих этапов:
* CI
* Развертывание в ЗА
    * Развертывание в Databricks и развертывание в ADF
    * Интеграционный тест

Он содержит ряд этапов ***развертывания,*** равных количеству целевых сред, которые у вас есть. Каждый этап ***развертывания*** содержит два [развертывания,](https://docs.microsoft.com/azure/devops/pipelines/process/deployment-jobs?view=azure-devops) которые работают параллельно, и [задание,](https://docs.microsoft.com/azure/devops/pipelines/process/phases?view=azure-devops&tabs=yaml) которое выполняется после развертывания для тестирования решения на среде.

Образец реализации трубопровода собран в следующем фрагменте ***ямля:***

```yaml
variables:
- group: devops-ds-vg

stages:
- stage: 'CI'
  displayName: 'CI'
  jobs:
  - job: "CI_Job"
    displayName: "CI Job"
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'
    - script: pip install --upgrade flake8 flake8_formatter_junit_xml
      displayName: 'Install flake8'
    - checkout: self
    - script: |
       flake8 --output-file=$(Build.BinariesDirectory)/lint-testresults.xml --format junit-xml  
    workingDirectory: '$(Build.SourcesDirectory)'
    displayName: 'Run flake8 (code style analysis)'  
    - script: |
       python -m pytest --junitxml=$(Build.BinariesDirectory)/unit-testresults.xml $(Build.SourcesDirectory)
    displayName: 'Run unit tests'
    - task: PublishTestResults@2
    condition: succeededOrFailed()
    inputs:
        testResultsFiles: '$(Build.BinariesDirectory)/*-testresults.xml'
        testRunTitle: 'Linting & Unit tests'
        failTaskOnFailedTests: true
    displayName: 'Publish linting and unit test results'    

    # The CI stage produces two artifacts (notebooks and ADF pipelines).
    # The pipelines Azure Resource Manager templates are stored in a technical branch "adf_publish"
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/code/dataingestion
      artifact: di-notebooks
    - checkout: git://${{variables['System.TeamProject']}}@adf_publish    
    - publish: $(Build.SourcesDirectory)/$(Build.Repository.Name)/devops-ds-adf
      artifact: adf-pipelines

- stage: 'Deploy_to_QA'
  displayName: 'Deploy to QA'
  variables:
  - group: devops-ds-qa-vg
  jobs:
  - deployment: "Deploy_to_Databricks"
    displayName: 'Deploy to Databricks'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: UsePythonVersion@0
              inputs:
                versionSpec: '3.x'
                addToPath: true
                architecture: 'x64'
              displayName: 'Use Python3'

            - task: configuredatabricks@0
              inputs:
                url: '$(DATABRICKS_URL)'
                token: '$(DATABRICKS_TOKEN)'
              displayName: 'Configure Databricks CLI'    

            - task: deploynotebooks@0
              inputs:
                notebooksFolderPath: '$(Pipeline.Workspace)/di-notebooks'
                workspaceFolder: '/Shared/devops-ds'
              displayName: 'Deploy (copy) data processing notebook to the Databricks cluster'             
  - deployment: "Deploy_to_ADF"
    displayName: 'Deploy to ADF'
    timeoutInMinutes: 0
    environment: qa
    strategy:
      runOnce:
        deploy:
          steps:
            - task: AzureResourceGroupDeployment@2
              displayName: 'Deploy ADF resources'
              inputs:
                azureSubscription: $(AZURE_RM_CONNECTION)
                resourceGroupName: $(RESOURCE_GROUP)
                location: $(LOCATION)
                csmFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateForFactory.json'
                csmParametersFile: '$(Pipeline.Workspace)/adf-pipelines/ARMTemplateParametersForFactory.json'
                overrideParameters: -data-ingestion-pipeline_properties_variables_data_file_name_defaultValue "$(DATA_FILE_NAME)"
  - job: "Integration_test_job"
    displayName: "Integration test job"
    dependsOn: [Deploy_to_Databricks, Deploy_to_ADF]
    pool:
      vmImage: 'ubuntu-latest'
    timeoutInMinutes: 0
    steps:
    - task: AzurePowerShell@4
      displayName: 'Execute ADF Pipeline'
      inputs:
        azureSubscription: $(AZURE_RM_CONNECTION)
        ScriptPath: '$(Build.SourcesDirectory)/adf/utils/Invoke-ADFPipeline.ps1'
        ScriptArguments: '-ResourceGroupName $(RESOURCE_GROUP) -DataFactoryName $(DATA_FACTORY_NAME) -PipelineName $(PIPELINE_NAME)'
        azurePowerShellVersion: LatestVersion
    - task: UsePythonVersion@0
      inputs:
        versionSpec: '3.x'
        addToPath: true
        architecture: 'x64'
      displayName: 'Use Python3'

    - task: configuredatabricks@0
      inputs:
        url: '$(DATABRICKS_URL)'
        token: '$(DATABRICKS_TOKEN)'
      displayName: 'Configure Databricks CLI'    

    - task: executenotebook@0
      inputs:
        notebookPath: '/Shared/devops-ds/test-data-ingestion'
        existingClusterId: '$(DATABRICKS_CLUSTER_ID)'
        executionParams: '{"bin_file_name":"$(bin_FILE_NAME)"}'
      displayName: 'Test data ingestion'

    - task: waitexecution@0
      displayName: 'Wait until the testing is done'                

```

## <a name="next-steps"></a>Дальнейшие действия

* [Система управления версиями в Фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/source-control)
* [Непрерывная интеграция и доставка на фабрике данных Azure](https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment)
* [DevOps для Azure Databricks](https://marketplace.visualstudio.com/items?itemName=riserrad.azdo-databricks)
