---
title: Использование Azure Pipelines для создания и развертывания решений HPC в пакетной службе Azure | Документация Майкрософт
description: Узнайте, как развернуть конвейер сборки или выпуска для приложения HPC, работающего в пакетной службе Azure.
author: christianreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 47665171ee5ae137e0503b3e5fa1d369aeabb356
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840057"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Использование Azure Pipelines для создания и развертывания решений HPC

Службы Azure DevOps Services предоставляют широкий спектр средств, используемых группами разработки при создании пользовательского приложения. Средства, предоставляемые Azure DevOps, могут преобразовываться в автоматизированное создание и тестирование решений для высокопроизводительных вычислений. В этой статье показано, как настроить непрерывную интеграцию (CI) и непрерывное развертывание (CD) с помощью Azure Pipelines для высокопроизводительного решения, развернутого в пакетной службе Azure.

Azure Pipelines предоставляет ряд современных процессов CI/CD для создания, развертывания, тестирования и мониторинга программного обеспечения. Эти процессы ускоряют доставку программного обеспечения, позволяя сосредоточиться на коде, а не на поддержке инфраструктуры и операций.

## <a name="create-an-azure-pipeline"></a>Создание конвейера Azure

В этом примере мы создадим конвейер сборки и выпуска для развертывания инфраструктуры пакетной службы Azure и выпуска пакета приложения. Если код разрабатывается локально, то это общий поток развертывания:

![Схема, показывающая поток развертывания в нашем конвейере](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Установка

Для выполнения действий, описанных в этой статье, требуется организация Azure DevOps и командный проект.

* [Создание Организации Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Создание проекта в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Система управления версиями для вашей среды

Система управления версиями позволяет командам отслеживать изменения, внесенные в базу кода, и проверять предыдущие версии кода.

Как правило, система управления версиями предполагает наличие программного кода вручную. Как насчет базовой инфраструктуры? Это приводит нас к инфраструктуре в виде кода, где мы будем использовать шаблоны Azure Resource Manager или другие альтернативы с открытым исходным кодом, чтобы декларативно определить нашу базовую инфраструктуру.

Этот пример во многом зависит от ряда шаблонов диспетчер ресурсов (документов JSON) и существующих двоичных файлов. Вы можете скопировать эти примеры в репозиторий и отправить их в Azure DevOps.

Структура базы кода, используемая в этом образце, похожа на следующую.

* Папка **ARM-Templates** , содержащая несколько шаблонов Azure Resource Manager. Шаблоны описаны в этой статье.
* Папка **Client-Application** , которая является копией [обработки файлов .NET пакетной службы Azure с помощью примера FFmpeg](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) . Это не требуется для этой статьи.
* Папка **HPC-Application** , которая является 64-разрядной версией [FFmpeg 3,4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip)для Windows.
* Папка **конвейеров** . Он содержит файл YAML, описывающий наш процесс сборки. Это описано в статье.

В этом разделе предполагается, что вы знакомы с управлением версиями и разрабатываете шаблоны диспетчер ресурсов. Если вы не знакомы с этими понятиями, дополнительные сведения см. на следующих страницах.

* [Что такое система управления версиями?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Описание структуры и синтаксиса шаблонов Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md)

#### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

В этом примере используется несколько шаблонов диспетчер ресурсов для развертывания решения. Для этого мы используем ряд шаблонов возможностей (аналогично единицам или модулям), которые реализуют определенный набор функций. Мы также используем шаблон комплексного решения, который отвечает за объединение этих базовых возможностей. Существует несколько преимуществ этого подхода:

* Базовые шаблоны возможностей можно тестировать отдельно.
* Базовые шаблоны возможностей могут быть определены как стандартные внутри организации и повторно использоваться в нескольких решениях.

В этом примере имеется шаблон комплексного решения (Deployment. JSON), который развертывает три шаблона. Базовые шаблоны — это шаблоны возможностей, отвечающие за развертывание определенного аспекта решения.

![Пример структуры связанного шаблона с помощью шаблонов Azure Resource Manager](media/batch-ci-cd/ARMTemplateHierarchy.png)

Первый шаблон, который мы будем просматривать, — для учетной записи хранения Azure. Нашему решению требуется учетная запись хранения для развертывания приложения в учетной записи пакетной службы. При создании шаблонов диспетчер ресурсов для учетных записей хранения следует помнить о справочнике по [шаблонам диспетчер ресурсов для типов ресурсов хранилища Microsoft. Storage](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) .

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

Далее будет рассмотрен шаблон учетной записи пакетной службы Azure. Учетная запись пакетной службы Azure выступает в качестве платформы для выполнения многочисленных приложений в пулах (группирование компьютеров). При создании шаблонов диспетчер ресурсов для учетных записей пакетной службы следует помнить о справочнике по [шаблонам диспетчер ресурсов для типов ресурсов Microsoft. Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) .

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

В следующем шаблоне показан пример создания пула пакетной службы Azure (серверные машины для обработки наших приложений). При создании шаблонов диспетчер ресурсов для пулов учетных записей пакетной службы следует помнить о справочнике по [шаблонам диспетчер ресурсов для типов ресурсов Microsoft. Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) .

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

Наконец, у нас есть шаблон, который действует аналогично Orchestrator. Этот шаблон отвечает за развертывание шаблонов возможностей.

Дополнительные сведения о [создании связанных шаблонов Azure Resource Manager](../azure-resource-manager/resource-manager-tutorial-create-linked-templates.md) можно найти в отдельной статье.

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

Инфраструктуру и программное обеспечение можно определить как код и соразмещать в одном репозитории.

Для этого решения в качестве пакета приложения используется FFmpeg. Пакет FFmpeg можно скачать [здесь](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Пример структуры репозитория Git](media/batch-ci-cd/git-repository.jpg)

Этот репозиторий состоит из четырех основных разделов:

* Папка **ARM-Templates** , в которой хранится Наша инфраструктура в виде кода
* Папка **HPC-Application** , содержащая двоичные файлы для FFmpeg
* Папка **конвейеров** , содержащая определение для нашего конвейера сборки.
* **Необязательно**: Папка **Client-Application** , в которой будет храниться код для приложения .NET. Мы не используем его в примере, но в собственном проекте вам может потребоваться выполнить запуск пакетного приложения HPC через клиентское приложение.

> [!NOTE]
> Это лишь один пример структуры для базы кода. Этот подход используется в целях демонстрации того, что приложение, инфраструктура и код конвейера хранятся в одном репозитории.

Теперь, когда исходный код настроен, можно начать первую сборку.

## <a name="continuous-integration"></a>Непрерывная интеграция

[Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops), в рамках Azure DevOps Services, помогает реализовать конвейер сборки, тестирования и развертывания для приложений.

На этом этапе конвейера тесты обычно выполняются для проверки кода и сборки соответствующих частей программного обеспечения. Число и типы тестов, а также любые дополнительные выполняемые задачи будут зависеть от вашей более широкой стратегии сборки и выпуска.

## <a name="preparing-the-hpc-application"></a>Подготовка приложения HPC

В этом примере мы рассмотрим папку **приложения HPC** . Папка **HPC-Application** — это программное обеспечение FFmpeg, которое будет запускаться в учетной записи пакетной службы Azure.

1. Перейдите к разделу сборки Azure Pipelines в вашей организации Azure DevOps. Создайте **Новый конвейер**.

    ![Создание нового конвейера сборки](media/batch-ci-cd/new-build-pipeline.jpg)

1. Создать конвейер сборки можно двумя способами.

    1\. [С помощью визуального конструктора](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Чтобы использовать его, нажмите кнопку "использовать визуальный конструктор" на **новой** странице конвейера.

    2\. [Использование сборок YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Вы можете создать новый конвейер YAML, щелкнув параметр Azure Repos или GitHub на новой странице конвейера. Кроме того, можно сохранить приведенный ниже пример в системе управления версиями и ссылаться на существующий файл YAML, щелкнув визуальный конструктор, а затем используя шаблон YAML.

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

1. Когда сборка будет настроена по мере необходимости, выберите **сохранить & очередь**. Если включена непрерывная интеграция (в разделе **Triggers** ), сборка автоматически активируется при создании новой фиксации в репозитории, предоставилой условия, заданные в сборке.

    ![Пример существующего конвейера сборки](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Просмотрите динамические обновления о ходе выполнения сборки в Azure DevOps, перейдя к разделу **build** Azure pipelines. Выберите соответствующую сборку из определения сборки.

    ![Просмотр динамических выходных данных из сборки](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Если вы используете клиентское приложение для выполнения пакетного приложения HPC, необходимо создать отдельное определение сборки для этого приложения. В документации по [Azure pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) можно найти ряд руководств.

## <a name="continuous-deployment"></a>Непрерывное развертывание

Azure Pipelines также используется для развертывания приложения и базовой инфраструктуры. [Конвейеры выпуска](https://docs.microsoft.com/azure/devops/pipelines/release) — это компонент, обеспечивающий непрерывное развертывание и автоматизирующий процесс выпуска.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Развертывание приложения и базовой инфраструктуры

Существует ряд действий, связанных с развертыванием инфраструктуры. Так как мы использовали [связанные шаблоны](../azure-resource-manager/resource-group-linked-templates.md), эти шаблоны должны быть доступны из общедоступной конечной точки (HTTP или HTTPS). Это может быть репозиторий в GitHub или учетная запись хранилища BLOB-объектов Azure или другое место хранения. Переданные артефакты шаблона могут оставаться безопасными, так как они могут храниться в частном режиме, но доступ к ним можно получить, используя определенную форму маркера подписанного URL-адрес (SAS). В следующем примере показано, как развернуть инфраструктуру с шаблонами из большого двоичного объекта службы хранилища Azure.

1. Создайте **новое определение выпуска**и выберите пустое определение. Затем необходимо переименовать созданную среду на что-то, что нужно для нашего конвейера.

    ![Начальный конвейер выпуска](media/batch-ci-cd/Release-0.jpg)

1. Создайте зависимость от конвейера сборки, чтобы получить выходные данные для нашего приложения HPC.

    > [!NOTE]
    > Опять же, обратите внимание на **Псевдоним источника**, так как это потребуется при создании задач в определении выпуска.

    ![Создание ссылки артефакта на Хпкаппликатионпаккаже в соответствующем конвейере сборки](media/batch-ci-cd/Release-1.jpg)

1. Создайте ссылку на другой артефакт, на этот раз репозиторий Azure. Это необходимо для доступа к шаблонам диспетчер ресурсов, хранящимся в репозитории. Так как шаблоны диспетчер ресурсов не требуют компиляции, их не нужно отправлять через конвейер сборки.

    > [!NOTE]
    > Опять же, обратите внимание на **Псевдоним источника**, так как это потребуется при создании задач в определении выпуска.

    ![Создание ссылки артефакта на Azure Repos](media/batch-ci-cd/Release-2.jpg)

1. Перейдите к разделу **Variables** . Рекомендуется создать несколько переменных в конвейере, чтобы не выводить одну и ту же информацию в несколько задач. Это переменные, используемые в этом примере, и то, как они влияют на развертывание.

    * **аппликатионсторажеаккаунтнаме**: Имя учетной записи хранения для хранения двоичных файлов приложения HPC
    * **батчаккаунтаппликатионнаме**: Имя приложения в учетной записи пакетной службы Azure
    * **батчаккаунтнаме**: Имя учетной записи пакетной службы Azure
    * **батчаккаунтпулнаме**: Имя пула виртуальных машин, выполняющих обработку
    * **батчаппликатионид**: Уникальный идентификатор для приложения пакетной службы Azure
    * **батчаппликатионверсион**: Семантическая версия приложения пакетной службы (то есть двоичные файлы FFmpeg)
    * **location**: Расположение для развертываемых ресурсов Azure
    * **resourceGroupName**: Имя создаваемой группы ресурсов и место, где будут развернуты ресурсы
    * **storageAccountName**: Имя учетной записи хранения для хранения связанных шаблонов диспетчер ресурсов

    ![Пример переменных, заданных для выпуска Azure Pipelines](media/batch-ci-cd/Release-4.jpg)

1. Перейдите к задачам среды разработки. В приведенном ниже снимке можно увидеть шесть задач. Эти задачи: Скачайте ZIP-файлы FFmpeg, разверните учетную запись хранения для размещения вложенных шаблонов диспетчер ресурсов, скопируйте эти шаблоны диспетчер ресурсов в учетную запись хранения, разверните учетную запись пакетной службы и необходимые зависимости, создайте приложение в Учетная запись пакетной службы Azure и передача пакета приложения в учетную запись пакетной службы Azure.

    ![Пример задач, используемых для освобождения приложения HPC в пакетной службе Azure](media/batch-ci-cd/Release-3.jpg)

1. Добавьте задачу **загрузить артефакт конвейера (Предварительная версия)** и задайте следующие свойства:
    * **Отображаемое имя:** Скачать ApplicationPackage в агент
    * **Имя загружаемого артефакта:** HPC-Application
    * **Путь для скачивания**: $ (System. DefaultWorkingDirectory)

1. Создайте учетную запись хранения для хранения артефактов. Можно использовать существующую учетную запись хранения из решения, но для автономного примера и изоляции содержимого мы создаем выделенную учетную запись хранения для наших артефактов (в частности, шаблоны диспетчер ресурсов).

    Добавьте задачу **развертывания группы ресурсов Azure** и задайте следующие свойства:
    * **Отображаемое имя:** Развертывание учетной записи хранения для шаблонов диспетчер ресурсов
    * **Подписка Azure:** Выберите подходящую подписку Azure
    * **Действие**: Создание или изменение группы ресурсов
    * **Группа ресурсов**: $ (resourceGroupName)
    * **Расположение**: $ (Location)
    * **Шаблон**: $ (System. артифактсдиректори)/ **{йоуразуререпоартифактсаурцеалиас}** /АРМ-темплатес/сторажеаккаунт.жсон
    * **Переопределить параметры шаблона**:-AccountName $ (storageAccountName)

1. Передайте артефакты из системы управления версиями в учетную запись хранения. Для выполнения этой задачи используется задача конвейера Azure. В рамках этой задачи URL-адрес контейнера учетной записи хранения и маркер SAS можно выдавать в переменную в Azure Pipelines. Это означает, что его можно повторно использовать в рамках этой фазы агента.

    Добавьте задачу **копирования файлов Azure** и задайте следующие свойства:
    * **Источник:** $ (System. артифактсдиректори)/ **{йоуразуререпоартифактсаурцеалиас}** /АРМ-темплатес/
    * **Тип подключения Azure**: Azure Resource Manager
    * **Подписка Azure:** Выберите подходящую подписку Azure
    * **Тип назначения**: BLOB-объект Azure
    * **Учетная запись хранения RM**: $ (storageAccountName)
    * **Имя контейнера**: шаблоны
    * **URI контейнера хранилища**: темплатеконтаинерури
    * **Маркер SAS контейнера хранилища**: темплатеконтаинерсастокен

1. Разверните шаблон Orchestrator. Взпомните шаблон Orchestrator, приведенный ранее, вы заметите, что в дополнение к маркеру SAS были указаны параметры для URL-адреса контейнера учетной записи хранения. Следует заметить, что переменные, необходимые в шаблоне диспетчер ресурсов, хранятся в разделе Variables определения выпуска или были заданы из другой Azure Pipelines задачи (например, в составе задачи копирования больших двоичных объектов Azure).

    Добавьте задачу **развертывания группы ресурсов Azure** и задайте следующие свойства:
    * **Отображаемое имя:** Развертывание пакетной службы Azure
    * **Подписка Azure:** Выберите подходящую подписку Azure
    * **Действие**: Создание или изменение группы ресурсов
    * **Группа ресурсов**: $ (resourceGroupName)
    * **Расположение**: $ (Location)
    * **Шаблон**: $ (System. артифактсдиректори)/ **{йоуразуререпоартифактсаурцеалиас}** /АРМ-темплатес/деплоймент.жсон
    * **Переопределить параметры шаблона**:```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Распространенной практикой является использование Azure Key Vaultных задач. Если субъект-служба (подключение к подписке Azure) имеет соответствующие политики доступа, он может скачать секреты из Azure Key Vault и использовать их в качестве переменных в конвейере. Имя секрета будет установлено со связанным значением. Например, в определении выпуска можно ссылаться на секрет sshPassword с $ (sshPassword).

1. Следующие шаги вызывают Azure CLI. Первый используется для создания приложения в пакетной службе Azure. и отправить связанные пакеты.

    Добавьте задачу **Azure CLI** и задайте следующие свойства:
    * **Отображаемое имя:** Создание приложения в учетной записи пакетной службы Azure
    * **Подписка Azure:** Выберите подходящую подписку Azure
    * **Расположение скрипта**: Встроенный скрипт
    * **Встроенный скрипт**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Второй шаг используется для передачи связанных пакетов в приложение. В нашем случае это файлы FFmpeg.

    Добавьте задачу **Azure CLI** и задайте следующие свойства:
    * **Отображаемое имя:** Отправка пакета в учетную запись пакетной службы Azure
    * **Подписка Azure:** Выберите подходящую подписку Azure
    * **Расположение скрипта**: Встроенный скрипт
    * **Встроенный скрипт**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Номер версии пакета приложения задается переменной. Это удобно, если перезапись предыдущих версий пакета выполняется автоматически, и если требуется вручную управлять номером версии пакета, отправленного в пакетную службу Azure.

1. Создайте новый выпуск, выбрав **выпуск > создать новый выпуск**. После активации выберите ссылку на новый выпуск, чтобы просмотреть его состояние.

1. Вы можете просмотреть выходные данные агента, нажав кнопку **журналы** под средой.

    ![Просмотр состояния выпуска](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Тестирование среды

После настройки среды Убедитесь, что следующие тесты могут быть успешно выполнены.

Подключитесь к новой учетной записи пакетной службы Azure с помощью Azure CLI из командной строки PowerShell.

* Войдите в свою учетную запись Azure `az login` с помощью и следуйте инструкциям по проверке подлинности.
* Теперь выполните аутентификацию учетной записи пакетной службы:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Список доступных приложений

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Убедитесь, что пул является допустимым

```azurecli
az batch pool list
```

Обратите внимание на `currentDedicatedNodes` значение из выходных данных этой команды. Это значение корректируется в следующем тесте.

#### <a name="resize-the-pool"></a>Изменение размера пула

Измените размер пула, чтобы в нем можно было выполнять тестирование заданий и задач, а также для проверки текущего состояния до завершения изменения размера и наличия доступных узлов.

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Следующие шаги

В дополнение к этой статье есть два учебника, в которых используется FFmpeg с использованием .NET и Python. Дополнительные сведения о взаимодействии с учетной записью пакетной службы с помощью простого приложения см. в этих руководствах.

* [Выполнение параллельной рабочей нагрузки с помощью пакетной службы Azure с использованием API Python](tutorial-parallel-python.md)
* [Выполнение параллельной рабочей нагрузки с пакетной службой Azure с помощью API .NET](tutorial-parallel-dotnet.md)
