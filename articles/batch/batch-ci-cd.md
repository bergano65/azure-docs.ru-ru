---
title: Используйте Azure конвейеры для создания и развертывания решений HPC - пакетной службы Azure | Документация Майкрософт
description: Узнайте, как развернуть конвейер сборки и выпуска приложения HPC, выполняется в пакетной службе Azure.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.openlocfilehash: 5b7c44d3ea3394ff728adfb9d9fd72293138fb2e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60880952"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Используйте Azure конвейеры для создания и развертывания решения HPC

Службы Azure DevOps предоставляют широкий спектр средств, используемых группами разработки при создании пользовательского приложения. Средства, предоставляемые DevOps в Azure можно легко преобразовать в автоматизированной сборки и тестирования решения для высокопроизводительных вычислений. В этой статье показано, как настроить непрерывную интеграцию (CI) и непрерывного развертывания (CD) с помощью Azure конвейеры для высокопроизводительных вычислений решения, которое развертывается в пакетной службе Azure.

Конвейеры Azure предоставляет широкий спектр современных процессов Непрерывной интеграции и для создания, развертывания, тестирования и мониторинга программного обеспечения. Эти процессы ускорение доставки программного обеспечения, позволяя пользователю сосредоточиться на коде, не поддерживают инфраструктуры и действий.

## <a name="create-an-azure-pipeline"></a>Создать конвейер Azure

В этом примере мы создадим сборки и выпуска конвейер, чтобы развернуть инфраструктуру, пакетной службы Azure и выпуска пакета приложения. При условии, что код разработан локально, это поток общие развертывания:

![Схема, показывающая поток развертывания в нашего конвейера](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Настройка

Чтобы выполнить действия, описанные в этой статье, требуется организации Azure DevOps и командного проекта.

* [Создать организацию Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Создание проекта в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Система управления версиями для среды

Системы управления версиями позволяет командам отслеживать изменения, внесенные в базу кода и проверьте предыдущие версии кода.

Как правило системы управления версиями будет рассматривать Рука в руку с кодом программного обеспечения. Как насчет базовой инфраструктуры? Это приводит нас к инфраструктуры как кода, где мы будем использовать шаблоны Azure Resource Manager или другие альтернативные элементы с открытым исходным кодом декларативно определять наших базовой инфраструктуры.

В этом примере во многом зависит от нескольких шаблонов Resource Manager (документов JSON) и существующие двоичные файлы. Можно скопировать эти примеры в репозиторий и отправить их DevOps в Azure.

Структура базы кода, используемый в этом примере похож на следующую процедуру.

* **Шаблоны arm** папка, содержащая ряд шаблонов Azure Resource Manager. В этой статье описаны шаблоны.
* Объект **клиентского приложения** папки, который является копией из [Azure пакетной службы .NET файл обработка с помощью ffmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) образца. Это не требуется для этой статьи.
* **Приложения hpc** папке, которая Windows 64-разрядная версия [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Объект **конвейеры** папки. Он содержит файл YAML, структурирование наш процесс сборки. Этот вопрос рассматривается в этой статье.

В этом разделе предполагается, что вы знакомы с шаблонами Resource Manager версии элемента управления и разработки. Если вы не знакомы с этими понятиями, см. Дополнительные сведения на следующих страницах.

* [Что такое системы управления версиями?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

В этом примере используется несколько шаблонов Resource Manager, чтобы развернуть наше решение. Чтобы сделать это, мы используем несколько возможностей шаблонов (аналогично единиц или модули), которые реализуют определенной части функциональных возможностей. Мы также используем шаблон решений end-to-end, который отвечает за перенос базовой возможности друг с другом. Существуют некоторые преимущества этого подхода:

* Шаблоны базовых возможностей по отдельности можно подвергать модульному тестированию.
* Шаблоны базовых возможностей могут быть определены как стандартный внутри организации и повторно использовать в нескольких решений.

В этом примере имеется шаблон решений end-to-end (deployment.json), выполняющий развертывание три шаблона. Базовые шаблоны, шаблоны возможностей, отвечающего за развертывание конкретные аспекты решения.

![Пример структуры связанного шаблона с помощью шаблонов Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Первый шаблон, который мы рассмотрим — для учетной записи хранения Azure. Наше решение требуется учетная запись хранения для развертывания приложения для нашей учетной записи пакетной службы. Стоит сведений о [шаблона Resource Manager: справочное руководство по типам ресурсов Microsoft.Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) при создании шаблонов Resource Manager для учетных записей хранения.

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

Далее мы рассмотрим шаблон учетной записи пакетной службы Azure. Учетной записи пакетной службы Azure выступает в качестве платформы для выполнения множества приложений в пулах (группами машин). Стоит сведений о [справочное руководство по шаблона Resource Manager для типов ресурсов Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) при создании шаблонов Resource Manager для учетных записей пакетной службы.

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

Следующий шаблон показан пример создания пула пакетной службы Azure (машин серверной части для обработки наших приложений). Стоит сведений о [справочное руководство по шаблона Resource Manager для типов ресурсов Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) при создании шаблонов Resource Manager для пулов учетной записи пакетной службы.

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

Наконец у нас есть шаблон, который действует аналогично оркестратора. Этот шаблон несет ответственность за развертывание шаблонов возможностей.

Вы можете также дополнительные сведения о [создания связанных шаблонов Azure Resource Manager](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) в отдельной статье.

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

#### <a name="the-hpc-solution"></a>Решения HPC

Инфраструктуру и программное обеспечение может быть определен как код и совместно размещенные в том же репозитории.

Для этого решения ffmpeg используется в качестве пакета приложения. Можно скачать пакет ffmpeg [здесь](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Пример структуры репозитория Git](media/batch-ci-cd/git-repository.jpg)

Существует четыре основных области для этого репозитория:

* **Шаблоны arm** папка, содержащая нашу инфраструктуру как код
* **Приложения hpc** папку, содержащую двоичные файлы для ffmpeg
* **Конвейеры** папку, которая содержит определение для построения конвейера.
* **Необязательно**: **Клиентское приложение** папку, которая будет храниться код для приложения .NET. Мы не используем это в примере, но в проекте, вы можете выполнить запусков приложения пакетной службы HPC с помощью клиентского приложения.

> [!NOTE]
> Это лишь один пример структуры для базы кода. Этот подход используется в целях демонстрации приложений, инфраструктуры и код конвейера, хранятся в том же репозитории.

Теперь, когда исходный код настроен, мы можем начать первая сборка.

## <a name="continuous-integration"></a>Непрерывная интеграция

[Azure конвейеры](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), в службах Azure DevOps, поможет вам реализовать конвейер сборки, тестирования и развертывания приложений.

На этом этапе конвейера обычно тесты для проверки кода и создавать соответствующие части программного обеспечения. Количество и типы тестов, и любые дополнительные задачи, которые запускаются будет зависеть шире сборки и выпуска стратегии.

## <a name="preparing-the-hpc-application"></a>Подготовка приложения HPC

В этом примере мы сосредоточимся на **приложения hpc** папки. **Приложения hpc** папка — программное обеспечение ffmpeg, который будет выполняться с в учетной записи пакетной службы Azure.

1. Перейдите к разделу построения конвейеров Azure в вашей организации DevOps в Azure. Создание **новый конвейер**.

    ![Создайте конвейер сборки](media/batch-ci-cd/new-build-pipeline.jpg)

1. У вас есть два варианта для создания конвейера сборки:

    a. [С помощью визуального конструктора](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Для этого щелкните «Использовать визуальный конструктор» **новый конвейер** страницы.

    2. [Использование YAML сборок](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Новый конвейер yaml-ФАЙЛ можно создать, щелкнув в репозитории Azure или параметр GitHub на новой странице конвейера. Кроме того можно хранить в приведенном ниже примере в системе управления версиями и ссылаться на существующий файл YAML, щелкнув визуальный конструктор и затем с помощью шаблона YAML.

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

1. После настройки сборки при необходимости выберите **сохранить и поместить в очередь**. При наличии включена непрерывная интеграция (в **триггеры** разделе), автоматически активирует сборку при новую фиксацию в репозиторий, отвечающих условиям задать в сборке.

    ![Примером существующего конвейера сборки](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Просмотреть обновления в реальном времени в ходе выполнения сборки в DevOps в Azure, перейдя к **построения** разделе конвейеров в Azure. Выберите подходящую сборку из определения сборки.

    ![Динамический просмотр выходных данных из сборки](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Если вы используете клиентское приложение для выполнения приложения пакетной службы HPC, необходимо создать отдельное определение сборки для этого приложения. Вы найдете ряд руководства в [конвейеры Azure](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) документации.

## <a name="continuous-deployment"></a>Непрерывное развертывание

Также используется для развертывания приложения и базовой инфраструктуры Azure конвейеров. [Конвейеры выпуска](https://docs.microsoft.com/azure/devops/pipelines/release/what-is-release-management?view=azure-devops) — компонент, который обеспечивает непрерывное развертывание и автоматизирует процесс выпуска.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Развертывание приложения и базовую инфраструктуру

Существует ряд действий, связанных с развертыванием инфраструктуры. Как мы использовали [связанные шаблоны](../azure-resource-manager/resource-group-linked-templates.md), эти шаблоны, необходимо будет доступен из общедоступной конечной точки (HTTP или HTTPS). Это может быть репозитория в GitHub, или учетную запись хранилища BLOB-объектов Azure или другого места. Артефакты загруженного шаблона можно защитить, так как они могут храниться в режиме защищенного просмотра, но доступ, с помощью некоторой формы токен общего доступа URL-адреса (SAS). Ниже приведен пример, как развернуть инфраструктуру с помощью шаблонов из большого двоичного объекта службы хранилища Azure.

1. Создание **новое определение выпуска**и выберите пустое определение. Далее нужно переименуйте только что созданный среды касается нашего конвейера.

    ![Первоначальный выпуск конвейера](media/batch-ci-cd/Release-0.jpg)

1. Создайте зависимость от конвейер сборки используются для получения выходных данных для нашего приложения HPC.

    > [!NOTE]
    > Опять же, обратите внимание, **псевдоним источника**, как оно понадобится при создании задачи в определении выпуска.

    ![Создает связь артефакта, чтобы HPCApplicationPackage в конвейере подходящую сборку](media/batch-ci-cd/Release-1.jpg)

1. Создайте ссылку на другой артефакт на этот раз репозиторий Azure. Это необходимо, чтобы открыть шаблоны Resource Manager, хранящиеся в репозитории. Как шаблоны Resource Manager не требуется компиляции, отправить их через конвейер сборки не нужно.

    > [!NOTE]
    > Опять же, обратите внимание, **псевдоним источника**, как оно понадобится при создании задачи в определении выпуска.

    ![Создание ссылки на артефакт в репозитории в Azure](media/batch-ci-cd/Release-2.jpg)

1. Перейдите к **переменных** раздел. Рекомендуется создать ряд переменных в конвейер, чтобы не введения те же сведения на несколько задач. Это переменные, используемые в этом примере, и их влияние на развертывание.

    * **applicationStorageAccountName**: Имя учетной записи хранения для хранения двоичных файлов приложения HPC
    * **batchAccountApplicationName**: Имя приложения в учетной записи пакетной службы Azure
    * **batchAccountName**: Имя учетной записи пакетной службы Azure
    * **batchAccountPoolName**: Имя пула виртуальных машин, выполняющий обработку
    * **batchApplicationId**: Уникальный идентификатор для приложения пакетной службы Azure
    * **batchApplicationVersion**: Семантическая версия приложения пакетной службы (то есть ffmpeg двоичные файлы)
    * **location**: Расположение для развертывания ресурсов Azure
    * **resourceGroupName**: Имя создаваемой группы ресурсов, и где будут развертываться ресурсы
    * **storageAccountName**: Имя учетной записи хранения для хранения связанных шаблонов Resource Manager

    ![Пример того, переменные, заданные для Azure конвейеры выпуска](media/batch-ci-cd/Release-4.jpg)

1. Перейдите к задачам для среды разработки. В ниже моментальных снимков, вы увидите шесть задач. Эти задачи будут: загрузить ffmpeg ZIP-файлы, разверните учетную запись хранилища размещена вложенные шаблоны Resource Manager, скопируйте эти шаблоны Resource Manager в учетную запись хранения, разверните учетную запись пакетной службы и необходимые зависимости, создайте приложение в учетной записи пакетной службы Azure и отправка пакета приложения для учетной записи пакетной службы Azure.

    ![Пример задачи, используемые для выпуска приложения HPC для пакетной службы Azure](media/batch-ci-cd/Release-3.jpg)

1. Добавить **скачать артефакт конвейера (Предварительная версия)** задач и задайте следующие свойства:
    * **Отображаемое имя:** Скачайте ApplicationPackage к агенту
    * **Имя скачиваемого артефакта:** hpc приложений
    * **Путь для загрузки**: $(System.DefaultWorkingDirectory)

1. Создайте учетную запись хранения для хранения артефактов. Можно использовать существующую учетную запись хранения из решения, но для изоляции содержимого и автономного пример, мы предоставляем выделенную учетную запись хранения для наших артефактов (в частности шаблоны Resource Manager).

    Добавить **развертывания группы ресурсов Azure** задач и задайте следующие свойства:
    * **Отображаемое имя:** Развертывание учетной записи хранения для шаблонов Resource Manager
    * **Подписка Azure:** Выберите соответствующую подписку Azure
    * **Действие**: Создание или изменение группы ресурсов
    * **Группа ресурсов**: $(resourceGroupName)
    * **Расположение**: $(location)
    * **Шаблон**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/storageAccount.json
    * **Переопределить параметры шаблона**: - accountName $(storageAccountName)

1. Отправьте артефакты из системы управления версиями в учетной записи хранения. Для этого выполняется Azure конвейера. В рамках этой задачи URL-адрес контейнера учетной записи хранения и маркер SAS можно выводимые переменной в конвейерах Azure. Это означает, что он может многократно использоваться на протяжении всего этого этапа агента.

    Добавить **копирование файлов Azure** задач и задайте следующие свойства:
    * **Источник:** $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates /
    * **Тип подключения к Azure**: Azure Resource Manager
    * **Подписка Azure:** Выберите соответствующую подписку Azure
    * **Тип назначения**: BLOB-объект Azure
    * **Учетная запись хранения диспетчера Ресурсов**: $(storageAccountName)
    * **Имя контейнера**: шаблоны
    * **URI контейнера хранилища**: templateContainerUri
    * **Маркер SAS контейнера хранилища**: templateContainerSasToken

1. Развертывание шаблона orchestrator. Отозвать шаблон orchestrator из ранее, вы заметите, что отсутствуют параметры для URL контейнер учетной записи хранения, наряду с токеном SAS. Следует заметить, что переменные, необходимые в шаблоне Resource Manager хранятся либо в разделе переменных определения выпуска, или были заданы из другой Azure конвейеров задач (например, часть задачи копирования BLOB-объектов Azure).

    Добавить **развертывания группы ресурсов Azure** задач и задайте следующие свойства:
    * **Отображаемое имя:** Развертывание пакетная служба Azure
    * **Подписка Azure:** Выберите соответствующую подписку Azure
    * **Действие**: Создание или изменение группы ресурсов
    * **Группа ресурсов**: $(resourceGroupName)
    * **Расположение**: $(location)
    * **Шаблон**: $(System.ArtifactsDirectory)/**{YourAzureRepoArtifactSourceAlias}**/arm-templates/deployment.json
    * **Переопределить параметры шаблона**: ```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Распространенной практикой является использование Azure Key Vault задач. Если субъект-служба (подключение к подписке Azure) имеет набор политик соответствующие права доступа, его можно загрузить секреты из хранилища ключей Azure и использовать в качестве переменных в конвейере. Имя секрета устанавливается с помощью связанного значения. Например может ссылаться на секрета sshPassword с $(sshPassword) в определении выпуска.

1. Дальнейшие действия вызовите Azure CLI. Первый используется для создания приложения в пакетной службе Azure. и загрузите соответствующие пакеты.

    Добавить **Azure CLI** задач и задайте следующие свойства:
    * **Отображаемое имя:** Создание приложения в учетной записи пакетной службы Azure
    * **Подписка Azure:** Выберите соответствующую подписку Azure
    * **Расположение скрипта**: Встроенный сценарий
    * **Встроенный сценарий**: ```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Второй шаг используется для отправки связанные пакеты в приложения. В нашем случае файлы ffmpeg.

    Добавить **Azure CLI** задач и задайте следующие свойства:
    * **Отображаемое имя:** Отправка пакета в учетной записи пакетной службы Azure
    * **Подписка Azure:** Выберите соответствующую подписку Azure
    * **Расположение скрипта**: Встроенный сценарий
    * **Встроенный сценарий**: ```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Номер версии пакета приложения будет присвоено переменной. Это удобно в том случае, если перезапись предыдущих версий пакета подходит вам, и в том случае, если вы хотите вручную управлять номер версии пакета в пакетной службе Azure.

1. Создание нового выпуска, выбрав **выпуска > Создайте новый выпуск**. После активации, щелкните ссылку, чтобы ваш новый выпуск, чтобы просмотреть состояние.

1. Динамические выходные данные от агента можно просмотреть, выбрав **журналы** кнопка под пунктом в вашей среде.

    ![Просмотр состояния выпуска](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Среда тестирования

После настройки среды, убедитесь, что следующие тесты могут быть успешно завершена.

Подключитесь к новой записью пакетной службы Azure, с помощью Azure CLI из командной строки PowerShell.

* Войдите в учетную запись Azure с помощью `az login` и следуйте инструкциям для проверки подлинности.
* Пройти проверку подлинности учетной записи пакетной службы: `az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Список доступных приложений

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Проверьте допустимость пула

```azurecli
az batch pool list
```

Обратите внимание на значение `currentDedicatedNodes` из выходных данных этой команды. Это значение настраивается в следующий тест.

#### <a name="resize-the-pool"></a>Изменяет размер пула

Изменяет размер пула, поэтому существуют вычислительных узлов, доступных для задания и задачи тестирования, проверьте с помощью команды список пула, чтобы увидеть текущее состояние до завершения изменения размера и нет доступных узлов

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Дальнейшие действия

В дополнение к этой статье есть два руководства, которые используют ffmpeg, с помощью .NET и Python. См. в статье эти учебники, Дополнительные сведения о том, как взаимодействовать с учетной записью пакетной службы с помощью простого приложения.

* [Запуск параллельной рабочей нагрузки с помощью пакета Azure, с помощью Python API](tutorial-parallel-python.md)
* [Запуск параллельной рабочей нагрузки с помощью пакета Azure, с помощью .NET API](tutorial-parallel-dotnet.md)
