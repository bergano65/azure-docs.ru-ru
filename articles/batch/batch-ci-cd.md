---
title: Использование Azure Pipelines для создания и развертывания решений HPC
description: Узнайте, как развернуть конвейер сборки или выпуска для приложения HPC, работающего в пакетной службе Azure.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: how-to
ms.openlocfilehash: 72f976071a5fc65c8e96f6b3cd5c0094785e287b
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726848"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Использование Azure Pipelines для создания и развертывания решений HPC

Службы Azure DevOps Services предоставляют широкий спектр средств, используемых командами разработчиков при создании пользовательских приложений. Средства, предоставляемые Azure DevOps, позволяют реализовать автоматизированное создание и тестирование решений для высокопроизводительных вычислений. В этой статье показано, как с помощью Azure Pipelines настроить непрерывную интеграцию (CI) и непрерывное развертывание (CD) для решения высокопроизводительных вычислений, развернутого в пакетной службе Azure.

Azure Pipelines предоставляет ряд современных процессов CI и CD для создания, развертывания, тестирования и мониторинга программного обеспечения. Эти процессы ускоряют доставку программного обеспечения, позволяя сосредоточиться на коде, а не на поддержке инфраструктуры и операций.

## <a name="create-an-azure-pipeline"></a>Создание конвейера Azure

В этом примере мы создадим конвейер сборки и выпуска для развертывания инфраструктуры пакетной службы Azure и выпуска пакета приложения. Если код разрабатывается локально, поток развертывания в целом выглядит так:

![Схема потока развертывания в конвейере](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Настройка

Для выполнения действий, описанных в этой статье, требуются организация Azure DevOps и командный проект.

* [Создание организации Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Создание проекта в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Система управления версиями для среды

Система управления версиями позволяет командам отслеживать изменения, внесенные в базу кода, и проверять предыдущие версии кода.

Как правило, программный код не мыслится без системы управления версиями. Помимо этого, требуется базовая инфраструктура. Это приводит нас к инфраструктуре как коду, которая предполагает использование шаблонов Azure Resource Manager или других альтернатив с открытым кодом для декларативного определения базовой инфраструктуры.

Этот пример во многом зависит от ряда шаблонов Resource Manager (документов JSON) и существующих двоичных файлов. Вы можете скопировать эти примеры в репозиторий и отправить их в Azure DevOps.

Ниже представлена структура базы кода, используемой в этом образце.

* Папка **arm-templates**, содержащая несколько шаблонов Azure Resource Manager. Шаблоны описываются далее в этой статье.
* Папка **client-application**, которая является копией образца [Обработка файлов .NET пакетной службы Azure с помощью ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial). Для этой статьи она не требуется.
* Папка **hpc-application**, которая является версией [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip) для 64-разрядных версий Windows.
* Папка **pipelines**. Содержит файл YAML, описывающий процесс сборки. Описывается далее в статье.

В этом разделе предполагается, что вы знакомы с управлением версиями и разработкой шаблонов Resource Manager. Если вы не знакомы с этими понятиями, дополнительные сведения см. на следующих страницах.

* [Что такое система управления версиями?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

В этом примере для развертывания решения используется несколько шаблонов Resource Manager. Для этого применяется ряд шаблонов возможностей (аналогичных модулям), которые реализуют определенный набор функций. Мы также используем шаблон комплексного решения, который отвечает за объединение базовых возможностей. Такой подход имеет ряд преимуществ.

* Шаблоны базовых возможностей можно подвергать модульному тестированию по отдельности.
* Шаблоны базовых возможностей можно определять как стандартные внутри организации и повторно использовать в нескольких решениях.

В этом примере имеется шаблон комплексного решения (deployment.json), который развертывает три шаблона. Базовые шаблоны — это шаблоны возможностей, отвечающие за развертывание определенного аспекта решения.

![Пример структуры связанных шаблонов с использованием шаблонов Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Первый шаблон, который мы рассмотрим, — это шаблон учетной записи хранения Azure. Нашему решению требуется учетная запись хранения для развертывания приложения в учетной записи пакетной службы. При создании шаблонов Resource Manager для учетных записей хранения следует учитывать сведения, изложенные в [справочном руководстве по шаблонам Resource Manager для типов ресурсов Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "accountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('accountName')]",
            "sku": {
                "name": "Standard_LRS"
            },
            "apiVersion": "2018-02-01",
            "location": "[resourceGroup().location]",
            "properties": {}
        }
    ],
    "outputs": {
        "blobEndpoint": {
          "type": "string",
          "value": "[reference(resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))).primaryEndpoints.blob]"
        },
        "resourceId": {
          "type": "string",
          "value": "[resourceId('Microsoft.Storage/storageAccounts', parameters('accountName'))]"
        }
    }
}
```

Далее будет рассмотрен шаблон учетной записи пакетной службы Azure. Учетная запись пакетной службы Azure выступает в качестве платформы для выполнения множества приложений в пулах (группах компьютеров). При создании шаблонов Resource Manager для учетных записей пакетной службы следует учитывать сведения, изложенные в [справочном руководстве по шаблонам Resource Manager для типов ресурсов Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
            }
        },
        "storageAccountId": {
           "type": "string",
           "metadata": {
                "description": "ID of the Azure Storage Account"
            }
        }
    },
    "variables": {},
    "resources": [
        {
            "name": "[parameters('batchAccountName')]",
            "type": "Microsoft.Batch/batchAccounts",
            "apiVersion": "2017-09-01",
            "location": "[resourceGroup().location]",
            "properties": {
              "poolAllocationMode": "BatchService",
              "autoStorage": {
                  "storageAccountId": "[parameters('storageAccountId')]"
              }
            }
          }
    ],
    "outputs": {}
}
```

В следующем шаблоне показан пример создания пула пакетной службы Azure (серверных компьютеров для обработки приложений). При создании шаблонов Resource Manager для пулов учетных записей пакетной службы следует учитывать сведения, изложенные в [справочном руководстве по шаблонам Resource Manager для типов ресурсов Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "batchAccountName": {
           "type": "string",
           "metadata": {
                "description": "Name of the Azure Batch Account"
           }
        },
        "batchAccountPoolName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account Pool"
             }
         }
    },
    "variables": {},
    "resources": [
        {
            "name": "[concat(parameters('batchAccountName'),'/', parameters('batchAccountPoolName'))]",
            "type": "Microsoft.Batch/batchAccounts/pools",
            "apiVersion": "2017-09-01",
            "properties": {
                "deploymentConfiguration": {
                    "virtualMachineConfiguration": {
                        "imageReference": {
                            "publisher": "Canonical",
                            "offer": "UbuntuServer",
                            "sku": "18.04-LTS",
                            "version": "latest"
                        },
                        "nodeAgentSkuId": "batch.node.ubuntu 18.04"
                    }
                },
                "vmSize": "Standard_D1_v2"
            }
          }
    ],
    "outputs": {}
}
```

Наконец, имеется шаблон, который действует аналогично оркестратору. Он отвечает за развертывание шаблонов возможностей.

Вы также можете узнать больше о создании связанных шаблонов Azure Resource Manager в [отдельной статье](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md).

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "templateContainerUri": {
           "type": "string",
           "metadata": {
                "description": "URI of the Blob Storage Container containing the Azure Resouce Manager templates"
            }
        },
        "templateContainerSasToken": {
           "type": "string",
           "metadata": {
                "description": "The SAS token of the container containing the Azure Resouce Manager templates"
            }
        },
        "applicationStorageAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Storage Account"
            }
         },
        "batchAccountName": {
            "type": "string",
            "metadata": {
                 "description": "Name of the Azure Batch Account"
            }
         },
         "batchAccountPoolName": {
             "type": "string",
             "metadata": {
                  "description": "Name of the Azure Batch Account Pool"
              }
          }
    },
    "variables": {},
    "resources": [
        {
            "apiVersion": "2017-05-10",
            "name": "storageAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/storageAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "accountName": {"value": "[parameters('applicationStorageAccountName')]"}
                }
            }
        },  
        {
            "apiVersion": "2017-05-10",
            "name": "batchAccountDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "storageAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccount.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "storageAccountId": {"value": "[reference('storageAccountDeployment').outputs.resourceId.value]"}
                }
            }
        },
        {
            "apiVersion": "2017-05-10",
            "name": "poolDeployment",
            "type": "Microsoft.Resources/deployments",
            "dependsOn": [
                "batchAccountDeployment"
            ],
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                  "uri": "[concat(parameters('templateContainerUri'), '/batchAccountPool.json', parameters('templateContainerSasToken'))]",
                  "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "batchAccountName": {"value": "[parameters('batchAccountName')]"},
                    "batchAccountPoolName": {"value": "[parameters('batchAccountPoolName')]"}
                }
            }
        }
    ],
    "outputs": {}
}
```

#### <a name="the-hpc-solution"></a>Решение HPC

Инфраструктуру и программное обеспечение можно определить как код и разместить вместе в одном репозитории.

Для этого решения в качестве пакета приложения используется ffmpeg. Пакет ffmpeg можно скачать [здесь](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Пример структуры репозитория GIT](media/batch-ci-cd/git-repository.jpg)

Этот репозиторий состоит из четырех основных разделов:

* папки **arm-templates**, в которой хранится инфраструктура как код;
* папки **hpc-application**, которая содержит двоичные файлы ffmpeg;
* папки **pipelines**, которая содержит определение конвейера сборки;
* **Необязательно**: папки **client-application**, в которой будет храниться код приложения .NET. Мы не используем ее в примере, но в собственном проекте вам может потребоваться выполнять запуски пакетного приложения HPC через клиентское приложение.

> [!NOTE]
> Это лишь один из примеров структуры для базы кода. Такой подход служит для демонстрации того, что код приложения, инфраструктуры и конвейера хранится в одном репозитории.

Теперь, когда исходный код настроен, можно начать первую сборку.

## <a name="continuous-integration"></a>Непрерывная интеграция

[Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops) в составе служб Azure DevOps Services помогает реализовать конвейер сборки, тестирования и развертывания для приложений.

На этом этапе конвейера обычно выполняются тесты для проверки кода и сборки соответствующих компонентов программного обеспечения. Число и типы тестов, а также дополнительные задачи зависят от общей стратегии сборки и выпуска.

## <a name="preparing-the-hpc-application"></a>Подготовка приложения HPC

В этом примере мы рассмотрим папку **hpc-application**. Папка **hpc-application** — это программное обеспечение ffmpeg, которое будет выполняться из учетной записи пакетной службы Azure.

1. Перейдите к разделу "Сборки" Azure Pipelines в вашей организации Azure DevOps. Создайте **конвейер**.

    ![Создание конвейера сборки](media/batch-ci-cd/new-build-pipeline.jpg)

1. Создать конвейер сборки можно двумя способами:

    а. [С помощью визуального конструктора](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Для этого на странице **Новый конвейер** щелкните "Использовать визуальный конструктор".

    b. [С помощью сборок YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Вы можете создать конвейер YAML, выбрав элемент Azure Repos или GitHub на странице "Новый конвейер". Кроме того, можно сохранить приведенный ниже пример в системе управления версиями и сослаться на существующий файл YAML, щелкнув в визуальном конструкторе и использовав шаблон YAML.

    ```yml
    # To publish an application into Azure Batch, we need to
    # first zip the file, and then publish an artifact, so that
    # we can take the necessary steps in our release pipeline.
    steps:
    # First, we Zip up the files required in the Batch Account
    # For this instance, those are the ffmpeg files
    - task: ArchiveFiles@2
      displayName: 'Archive applications'
      inputs:
        rootFolderOrFile: hpc-application
        includeRootFolder: false
        archiveFile: '$(Build.ArtifactStagingDirectory)/package/$(Build.BuildId).zip'
    # Publish that zip file, so that we can use it as part
    # of our Release Pipeline later
    - task: PublishPipelineArtifact@0
      inputs:
        artifactName: 'hpc-application'
        targetPath: '$(Build.ArtifactStagingDirectory)/package'
    ```

1. Когда сборка будет настроена требуемым образом, выберите команду **Сохранить и поместить в очередь**. Если включена непрерывная интеграция (в разделе **Триггеры**), сборка будет автоматически запускаться при внесении в репозиторий новой фиксации, соответствующей условиям в сборке.

    ![Пример существующего конвейера сборки](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Чтобы наблюдать за процессом сборки в Azure DevOps, перейдите к разделу **Сборка** в Azure Pipelines. Выберите соответствующую сборку из определения сборки.

    ![Просмотр результатов сборки](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Если вы используете клиентское приложение для выполнения пакетного приложения HPC, необходимо создать отдельное определение сборки для него. Ряд руководств можно найти в документации по [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops).

## <a name="continuous-deployment"></a>Непрерывное развертывание

Azure Pipelines также используется для развертывания приложения и базовой инфраструктуры. [Конвейеры выпуска](https://docs.microsoft.com/azure/devops/pipelines/release) — это компонент, обеспечивающий непрерывное развертывание и автоматизирующий процесс выпуска.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Развертывание приложения и базовой инфраструктуры

Существует ряд действий, связанных с развертыванием инфраструктуры. Так как мы использовали [связанные шаблоны](../azure-resource-manager/templates/linked-templates.md), эти шаблоны должны быть доступны из общедоступной конечной точки (HTTP или HTTPS). Это может быть репозиторий в GitHub, учетная запись хранилища BLOB-объектов Azure или другое место хранения. Безопасность переданных артефактов шаблона может обеспечиваться за счет хранения в частном режиме и доступа к ним с помощью некоторого маркера подписанного URL-адрес (SAS). В приведенном ниже примере показано, как развернуть инфраструктуру с шаблонами из большого двоичного объекта службы хранилища Azure.

1. Создайте **определение выпуска** и выберите пустое определение. Затем необходимо переименовать созданную среду так, чтобы было понятно, что она связана с конвейером.

    ![Исходный конвейер выпуска](media/batch-ci-cd/Release-0.jpg)

1. Создайте зависимость от конвейера сборки, чтобы получить выходные данные для приложения HPC.

    > [!NOTE]
    > Обратите внимание на **псевдоним источника**, так как он потребуется при создании задач в определении выпуска.

    ![Создание ссылки артефакта на HPCApplicationPackage в соответствующем конвейере сборки](media/batch-ci-cd/Release-1.jpg)

1. Создайте ссылку на другой артефакт, на этот раз репозиторий Azure. Это необходимо для доступа к шаблонам Resource Manager, хранящимся в репозитории. Так как шаблоны Resource Manager не требуют компиляции, их не нужно отправлять через конвейер сборки.

    > [!NOTE]
    > Обратите внимание на **псевдоним источника**, так как он потребуется при создании задач в определении выпуска.

    ![Создание ссылки артефакта на Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Перейдите к разделу **variables**. Рекомендуется создать в конвейере несколько переменных, чтобы не вводить одну и ту же информацию для нескольких задач. Ниже представлены переменные, используемые в примере, и указано, как они влияют на развертывание.

    * **applicationStorageAccountName**: имя учетной записи хранения для хранения двоичных файлов приложения HPC.
    * **batchAccountApplicationName**: имя приложения в учетной записи пакетной службы Azure.
    * **batchAccountName**: имя учетной записи пакетной службы Azure.
    * **batchAccountPoolName**: имя пула виртуальных машин, выполняющих обработку.
    * **batchApplicationId**: уникальный идентификатор для приложения пакетной службы Azure.
    * **batchApplicationVersion**: семантическая версия приложения пакетной службы (то есть двоичных файлов ffmpeg).
    * **location**: расположение для развертываемых ресурсов Azure.
    * **resourceGroupName**: имя создаваемой группы ресурсов и место, где будут развернуты ресурсы.
    * **storageAccountName**: имя учетной записи хранения для хранения связанных шаблонов Resource Manager.

    ![Пример переменных, заданных для выпуска Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Перейдите к задачам среды разработки. На приведенном ниже снимке экрана представлено шесть задач. Эти задачи скачивают ZIP-файлы ffmpeg, развертывают учетную запись хранения для размещения вложенных шаблонов Resource Manager, копируют эти шаблоны Resource Manager в учетную запись хранения, развертывают учетную запись пакетной службы и необходимые зависимости, создают приложение в учетной записи пакетной службы Azure и передают пакет приложения в учетную запись пакетной службы Azure.

    ![Пример задач, используемых для выпуска приложения HPC в пакетной службе Azure](media/batch-ci-cd/Release-3.jpg)

1. Добавьте задачу **Скачать артефакт конвейера (предварительная версия)** и задайте указанные ниже свойства.
    * **Отображаемое имя**: скачивание ApplicationPackage в агент
    * **Имя скачиваемого артефакта**: hpc-application
    * **Путь для скачивания**: $(System.DefaultWorkingDirectory)

1. Создайте учетную запись хранения для хранения артефактов. Можно использовать существующую учетную запись хранения из решения, но для изоляции содержимого мы создадим выделенную учетную запись хранения для наших артефактов (в частности, шаблонов Resource Manager).

    Добавьте задачу **Развертывание группы ресурсов Azure** и задайте указанные ниже свойства.
    * **Отображаемое имя**: развертывание учетной записи хранения для шаблонов Resource Manager
    * **Подписка Azure**: выберите соответствующую подписку Azure
    * **Action** (Действие). Создание или изменение группы ресурсов
    * **Группа ресурсов**: $(resourceGroupName)
    * **Расположение**: $(location)
    * **Шаблон**: $(System.ArtifactsDirectory)/ **{псевдоним_источника_артефактов_репозитория_Azure}** /arm-templates/storageAccount.json
    * **Переопределение параметров шаблона**: -accountName $(storageAccountName)

1. Передайте артефакты из системы управления версиями в учетную запись хранения. Для выполнения этой задачи используется задача конвейера Azure. В рамках этой задачи URL-адрес контейнера учетной записи хранения и маркер SAS можно передать в переменную в Azure Pipelines. Это означает, что их можно повторно использовать в рамках этого этапа агента.

    Добавьте задачу **Копирование файлов Azure** и задайте указанные ниже свойства.
    * **Источник**: $(System.ArtifactsDirectory)/ **{псевдоним_источника_артефактов_репозитория_Azure}** /arm-templates/
    * **Тип подключения Azure**: Azure Resource Manager
    * **Подписка Azure**: выберите соответствующую подписку Azure
    * **Тип назначения**: Большой двоичный объект Azure
    * **Учетная запись хранения Диспетчера ресурсов**: $(storageAccountName)
    * **Имя контейнера**: templates
    * **Код URI контейнера хранилища**: templateContainerUri
    * **Токен SAS контейнера хранилища**: templateContainerSasToken

1. Разверните шаблон оркестратора. Если обратить внимание на шаблон оркестратора, приведенный ранее, можно заметить, что в дополнение к маркеру SAS в нем имелись параметры для URL-адреса контейнера учетной записи хранения. Следует заметить, что переменные, необходимые в шаблоне Resource Manager, хранятся в разделе variables определения выпуска или были заданы из другой задачи Azure Pipelines (например, в задаче копирования больших двоичных объектов Azure).

    Добавьте задачу **Развертывание группы ресурсов Azure** и задайте указанные ниже свойства.
    * **Отображаемое имя**: развертывание пакетной службы Azure
    * **Подписка Azure**: выберите соответствующую подписку Azure
    * **Action** (Действие). Создание или изменение группы ресурсов
    * **Группа ресурсов**: $(resourceGroupName)
    * **Расположение**: $(location)
    * **Шаблон**: $(System.ArtifactsDirectory)/ **{псевдоним_источника_артефактов_репозитория_Azure}** /arm-templates/deployment.json
    * **Переопределение параметров шаблона**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Распространенной практикой является использование задач Azure Key Vault. Если субъект-служба (подключение к подписке Azure) имеет соответствующие политики доступа, он может скачать секреты из Azure Key Vault и использовать их в качестве переменных в конвейере. Имя секрета будет задано со связанным значением. Например, в определении выпуска на секрет sshPassword можно ссылаться с помощью $(sshPassword).

1. На следующих шагах вызывается Azure CLI. Первый служит для создания приложения в пакетной службе Azure и отправки связанных пакетов.

    Добавьте задачу **Azure CLI** и задайте указанные ниже свойства.
    * **Отображаемое имя**: создание приложения в учетной записи пакетной службы Azure
    * **Подписка Azure**: выберите соответствующую подписку Azure
    * **Расположение скрипта**: встроенный скрипт
    * **Встроенный скрипт**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Второй шаг используется для передачи связанных пакетов в приложение. В нашем случае это файлы ffmpeg.

    Добавьте задачу **Azure CLI** и задайте указанные ниже свойства.
    * **Отображаемое имя**: отправка пакета в учетную запись пакетной службы Azure
    * **Подписка Azure**: выберите соответствующую подписку Azure
    * **Расположение скрипта**: встроенный скрипт
    * **Встроенный скрипт**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Номер версии пакета приложения присваивается переменной. Это удобно, если требуется перезаписывать предыдущие версии пакета и вручную управлять номером версии пакета, отправляемого в пакетную службу Azure.

1. Создайте выпуск, выбрав **Выпуск > Создать выпуск**. После активации выберите ссылку на новый выпуск, чтобы просмотреть его состояние.

1. Вы можете просмотреть выходные данные агента, нажав кнопку **Журналы** под средой.

    ![Просмотр состояния выпуска](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Тестирование среды

После настройки среды убедитесь в том, что приведенные ниже тесты выполняются успешно.

Подключитесь к новой учетной записи пакетной службы Azure с помощью Azure CLI из командной строки PowerShell.

* Войдите в свою учетную запись Azure с помощью команды `az login` и следуйте инструкциям по проверке подлинности.
* Теперь выполните проверку подлинности учетной записи пакетной службы: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Получение списка доступных приложений

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Проверка допустимости пула

```azurecli
az batch pool list
```

Обратите внимание на значение `currentDedicatedNodes` в выходных данных команды. Это значение будет скорректировано в следующем тесте.

#### <a name="resize-the-pool"></a>Изменение размера пула

Измените размер пула так, чтобы в нем было достаточно вычислительных узлов для тестирования заданий и задач. Выполните проверку текущего состояния до завершения изменения размера и получения нужного количества узлов с помощью команды pool list.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Дальнейшие действия

В дополнение к этой статье есть два учебника по использованию ffmpeg с .NET и Python. Дополнительные сведения о взаимодействии с учетной записью пакетной службы с помощью простого приложения см. в следующих учебниках:

* [Запуск параллельной рабочей нагрузки с помощью пакетной службы Azure с использованием API Python](tutorial-parallel-python.md)
* [Запуск параллельной рабочей нагрузки с помощью пакетной службы Azure с использованием .NET API](tutorial-parallel-dotnet.md)
