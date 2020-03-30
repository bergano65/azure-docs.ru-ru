---
title: Используйте конвейеры Azure для создания & развертывать решения HPC - Azure Batch
description: Узнайте, как развернуть конвейер сборки/релиза для приложения HPC, работая в Azure Batch.
author: chrisreddington
ms.author: chredd
ms.date: 03/28/2019
ms.topic: conceptual
ms.custom: fasttrack-new
services: batch
ms.service: batch
ms.openlocfilehash: 50cb711dfd16c2a8718d13ba9255ace1e7e3e26d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79533136"
---
# <a name="use-azure-pipelines-to-build-and-deploy-hpc-solutions"></a>Используйте трубопроводы Azure для создания и развертывания решений HPC

Службы Azure DevOps предоставляют ряд инструментов, используемых командами разработчиков при создании пользовательского приложения. Инструменты, предоставляемые Azure DevOps, могут трансформироваться в автоматизированное построение и тестирование высокопроизводительных вычислительных решений. В этой статье показано, как настроить непрерывную интеграцию (CI) и непрерывное развертывание (CD) с использованием конвейеров Azure для высокопроизводительного вычислительного решения, развернутого в Azure Batch.

Azure Pipelines предоставляет широкий спектр современных процессов CI/CD для создания, развертывания, тестирования и мониторинга программного обеспечения. Эти процессы ускоряют доставку программного обеспечения, позволяя вам сосредоточиться на коде, а не поддерживать инфраструктуру и операции.

## <a name="create-an-azure-pipeline"></a>Создание трубопровода Azure

В этом примере мы создадим конвейер сборки и выпуска для развертывания инфраструктуры Azure Batch и выпуска пакета приложений. Если предположить, что код разработан локально, это общий поток развертывания:

![Диаграмма, показывающая поток развертывания в нашем трубопроводе](media/batch-ci-cd/DeploymentFlow.png)

### <a name="setup"></a>Настройка

Чтобы следовать шагам в этой статье, вам нужна организация Azure DevOps и командный проект.

* [Создание организации Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/accounts/create-organization?view=azure-devops)
* [Создание проекта в Azure DevOps](https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops)

### <a name="source-control-for-your-environment"></a>Управление исходом для окружающей среды

Управление исходным кодом позволяет группам отслеживать изменения, внесенные в кодовую базу, и проверять предыдущие версии кода.

Как правило, исходный контроль рассматривается в руках с программным кодом. Как насчет базовой инфраструктуры? Это подводит нас к инфраструктуре как кодексу, где мы будем использовать шаблоны Azure Resource Manager или другие альтернативы с открытым исходным кодом для декларативного определения нашей базовой инфраструктуры.

Этот пример в значительной степени опирается на ряд шаблонов менеджера ресурсов (Документы JSON) и существующих биений. Вы можете скопировать эти примеры в свой репозиторий и отодавить их в Azure DevOps.

Структура кодовой базы, используемая в этом примере, напоминает следующую структуру;

* Папка **для размытков,** содержащая ряд шаблонов управления ресурсами Azure. Шаблоны объясняются в этой статье.
* Папка **клиента-приложения,** которая является копией [обработки файлов Azure .NET с образцом ffmpeg.](https://github.com/Azure-Samples/batch-dotnet-ffmpeg-tutorial) Это не требуется для этой статьи.
* **Папка hpc-приложения,** которая является Windows 64-битная версия [ffmpeg 3.4](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).
* Папка **трубопроводов.** В нем содержится файл YAML с описанием процесса сборки. Об этом говорится в статье.

В этом разделе предполагается, что вы знакомы с управлением версиями и разработкой шаблонов управления ресурсами. Если вы не знакомы с этими понятиями, смотрите следующие страницы для получения дополнительной информации.

* [Что такое система управления версиями?](https://docs.microsoft.com/azure/devops/user-guide/source-control?view=azure-devops)
* [Понимание структуры и синтаксиса шаблонов менеджера ресурсов Azure](../azure-resource-manager/templates/template-syntax.md)

#### <a name="azure-resource-manager-templates"></a>Шаблоны Azure Resource Manager

Этот пример использует несколько шаблонов управления ресурсами для развертывания нашего решения. Для этого мы используем ряд шаблонов возможностей (по аналогии с единицами или модулями), которые реализуют определенную функциональность. Мы также используем шаблон сквозного решения, который отвечает за объединение этих базовых возможностей. Этот подход имеет несколько преимуществ:

* Базовые шаблоны возможностей могут быть индивидуально протестированы.
* Базовые шаблоны возможностей могут быть определены как стандарт внутри организации и повторно использованы в нескольких решениях.

В этом примере имеется шаблон сквозного решения (deployment.json), который развертывает три шаблона. Основными шаблонами являются шаблоны возможностей, ответственные за развертывание определенного аспекта решения.

![Пример структуры связанных шаблонов с использованием шаблонов управления ресурсами Azure](media/batch-ci-cd/ARMTemplateHierarchy.png)

Первый шаблон, который мы рассмотрим, предназначен для учетной записи хранения Azure. Наше решение требует учетной записи для хранения для развертывания приложения в нашем пакетном аккаунте. При создании шаблонов управления ресурсами для учетных записей для учетных записей хранилища ресурсов стоит знать о [руководстве](https://docs.microsoft.com/azure/templates/microsoft.storage/allversions) по шаблонам ресурсов Resource Manager для типов ресурсов.

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

Далее мы рассмотрим шаблон учетной записи пакетов Azure. Учетная запись Azure Batch действует как платформа для запуска многочисленных приложений в пулах (группирования машин). При создании шаблонов «Менеджер ресурсов» следует знать [о справочном руководстве по шаблонам ресурсов «Менеджер ресурсов ресурсов ресурсов ресурса» для Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) при создании шаблонов «Менеджер ресурсов» для учетных записей пакетов.

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

В следующем шаблоне показан пример создания пакета пакетов Azure (бэкэнд-машины для обработки наших приложений). При создании шаблонов «Менеджер ресурсов» следует знать [о справочном руководстве по шаблонам ресурсов «Менеджер ресурсов ресурсов ресурсов ресурса» для Microsoft.Batch](https://docs.microsoft.com/azure/templates/microsoft.batch/allversions) при создании шаблонов «Менеджер ресурсов» для пулов пакетных учетных записей.

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

Наконец, у нас есть шаблон, который действует подобно оркестратору. Этот шаблон отвечает за развертывание шаблонов возможностей.

Вы также можете узнать больше о [создании связанных шаблонов управления ресурсами Azure](../azure-resource-manager/templates/template-tutorial-create-linked-templates.md) в отдельной статье.

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

Инфраструктура и программное обеспечение могут быть определены как код и размещены в одном репозитории.

Для этого решения ffmpeg используется в качестве пакета приложений. Пакет ffmpeg можно скачать [здесь](https://ffmpeg.zeranoe.com/builds/win64/static/ffmpeg-3.4-win64-static.zip).

![Пример структуры репозитория Git](media/batch-ci-cd/git-repository.jpg)

В этом репозитории есть четыре основных раздела:

* Папка **для рукоятки,** которая хранит нашу инфраструктуру как код
* Папка **hpc-приложения,** содержащая файлы для ffmpeg
* Папка конвейеров, содержащая определение для нашего конвейера **сборки.**
* **Дополнительно**: Папка **клиента-приложения,** которая будет хранить код для приложения .NET. Мы не используем это в примере, но в вашем собственном проекте вы можете выполнить запускприложения HPC Batch Application через клиентское приложение.

> [!NOTE]
> Это лишь один из примеров структуры к кодовой базе. Этот подход используется для демонстрации того, что приложения, инфраструктура и код конвейера хранятся в одном репозитории.

Теперь, когда исходный код настроен, мы можем начать первую сборку.

## <a name="continuous-integration"></a>Непрерывная интеграция

[Конвейеры Azure,](https://docs.microsoft.com/azure/devops/pipelines/get-started/?view=azure-devops)в составе служб Azure DevOps, помогают реализовать конвейер сборки, тестирования и развертывания для приложений.

На этом этапе конвейера тесты обычно запускаются для проверки кода и создания соответствующих элементов программного обеспечения. Количество и типы тестов, а также любые дополнительные задачи, которые вы запустите, будут зависеть от вашей более широкой стратегии сборки и выпуска.

## <a name="preparing-the-hpc-application"></a>Подготовка приложения HPC

В этом примере мы сосредоточимся на папке **hpc-приложений.** Папка **hpc-приложения** — это программное обеспечение ffmpeg, которое будет работать в учетной записи Azure Batch.

1. Перейдите в раздел Сборки трубопроводов Azure в организации Azure DevOps. Создайте **новый конвейер.**

    ![Создание нового трубопровода сборки](media/batch-ci-cd/new-build-pipeline.jpg)

1. У вас есть два варианта создания конвейера сборки:

    а. [Использование визуального дизайнера](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=azure-devops&tabs=new-nav). Чтобы использовать это, нажмите кнопку "Использовать визуальный дизайнер" на странице **Нового конвейера.**

    b. [Использование сборки YAML](https://docs.microsoft.com/azure/devops/pipelines/get-started-yaml?view=azure-devops). Создать новый конвейер YAML можно, нажав на опцию Azure Repos или GitHub на новой странице конвейера. Кроме того, вы можете сохранить приведенный ниже пример в элементе управления исходным источником и ссылку на существующий файл YAML, нажав на Visual Designer, а затем используя шаблон YAML.

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

1. После настройки сборки выберите **«Сохранить & очередь.** Если у вас есть непрерывная интеграция включена (в разделе **Триггеры),** сборка автоматически срабатывает при создании нового коммита в репозиторий, что соответствует условиям, установленным в сборке.

    ![Пример существующего конвейера сборки](media/batch-ci-cd/existing-build-pipeline.jpg)

1. Просматривайте живые обновления о ходе сборки в Azure DevOps, перемещаясь по разделу **сборки** трубопроводов Azure. Выберите соответствующую сборку из определения сборки.

    ![Просмотр живых выходов из сборки](media/batch-ci-cd/Build-1.jpg)

> [!NOTE]
> Если вы используете клиентское приложение для выполнения приложения HPC Batch, необходимо создать отдельное определение сборки для этого приложения. В документации [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/index?view=azure-devops) можно найти несколько руководств.

## <a name="continuous-deployment"></a>Непрерывное развертывание

Трубопроводы Azure также используются для развертывания приложения и базовой инфраструктуры. [Конвейеры выпуска](https://docs.microsoft.com/azure/devops/pipelines/release) — это компонент, который позволяет непрерывно развертывать и автоматизирует процесс выпуска.

### <a name="deploying-your-application-and-underlying-infrastructure"></a>Развертывание приложения и базовой инфраструктуры

Развертывание инфраструктуры включает ряд шагов. Поскольку мы использовали [связанные шаблоны,](../azure-resource-manager/templates/linked-templates.md)эти шаблоны должны быть доступны с общедоступной точки (HTTP или HTTPS). Это может быть репозиторий на GitHub, или учетная запись хранения Azure Blob или другое место хранения. Загруженные артефакты шаблонов могут оставаться безопасными, так как они могут храниться в закрытом режиме, но доступны с помощью той или иной формы маркера общей подписи доступа (SAS). В следующем примере показано, как развертывать инфраструктуру с шаблонами из капли хранения Azure.

1. Создайте **новое определение выпуска**и выберите пустое определение. Затем нам нужно переименовать вновь созданную среду в нечто, имевшем отношение к нашему трубопроводу.

    ![Первоначальный выпуск трубопровода](media/batch-ci-cd/Release-0.jpg)

1. Создайте зависимость от конвейера сборки, чтобы получить выход для нашего приложения HPC.

    > [!NOTE]
    > Еще раз обратите внимание на **Источник Alias**, так как это будет необходимо, когда задачи создаются внутри определения выпуска.

    ![Создание ссылки артефакта на HPCApplicationPackage в соответствующем конвейере сборки](media/batch-ci-cd/Release-1.jpg)

1. Создайте ссылку на другой артефакт, на этот раз, На разрепо Azure. Это необходимо для доступа к шаблонам менеджера ресурсов, хранящимся в репозитории. Поскольку шаблоны Resource Manager не требуют компиляции, вам не нужно проталкивать их через конвейер сборки.

    > [!NOTE]
    > Еще раз обратите внимание на **Источник Alias**, так как это будет необходимо, когда задачи создаются внутри определения выпуска.

    ![Создание ссылки артефакта на Репо Azure](media/batch-ci-cd/Release-2.jpg)

1. Перейдите к разделу **переменных.** Рекомендуется создать ряд переменных в конвейере, чтобы вы не ввода одной и той же информации в несколько задач. Это переменные, используемые в этом примере, и как они влияют на развертывание.

    * **applicationStorageAccountName**: Имя учетной записи хранилища для хранения связных приложений HPC
    * **batchAccountApplicationName**: Имя приложения в учетной записи пакета Azure
    * **batchAccountName**: Имя учетной записи пакета Azure
    * **batchAccountPoolName**: Название пула ВМ, выполняющих обработку
    * **batchApplicationId**: Уникальный идентификатор для приложения Azure Batch
    * **batchApplicationVersion**: Семантическая версия вашего пакетного приложения (т.е. бинарные файлы ffmpeg)
    * **местоположение**: Местоположение ресурсов Azure, которые будут развернуты
    * **resourceGroupName**: Название группы ресурсов, которая будет создана, и где будут развернуты ваши ресурсы
    * **storageAccountName**: Название учетной записи хранилища для хранения связанных шаблонов менеджера ресурсов

    ![Пример переменных, установленных для выпуска трубопроводов Azure](media/batch-ci-cd/Release-4.jpg)

1. Перейдите к задачам для среды Dev. В приведенном ниже снимке можно увидеть шесть задач. Эти задачи будут: загрузить застегнутые файлы ffmpeg, развернуть учетную запись хранения для размещения вложенных шаблонов менеджера ресурсов, скопировать эти шаблоны менеджера ресурсов в учетную запись хранения, развернуть пакетную учетную запись и требуемые зависимости, создать приложение в учетная запись пакета Azure и загружать пакет приложений в учетную запись Azure Batch.

    ![Пример задач, используемых для выпуска приложения HPC в Azure Batch](media/batch-ci-cd/Release-3.jpg)

1. Добавьте задачу **Скачать трубопроводный артефакт (Preview)** и установите следующие свойства:
    * **Имя дисплея:** Скачать ApplicationPackage к агенту
    * **Название артефакта для загрузки:** hpc-приложение
    * **Путь к загрузке:**$(System.DefaultWorkingDirectory)

1. Создайте учетную запись хранения артефактов. Существующая учетная запись хранилища из решения может быть использована, но для автономного образца и изоляции содержимого мы делаем специальную учетную запись хранения для наших артефактов (в частности, шаблоны менеджера ресурсов).

    Добавьте задачу **развертывания группы ресурсов Azure** и установите следующие свойства:
    * **Имя дисплея:** Развертывание учетной записи хранения для шаблонов диспетчера ресурсов
    * **Подписка на Azure:** Выберите соответствующую подписку Azure
    * **Действие**: Создание или обновление группы ресурсов
    * **Ресурсная группа**: $(ресурсGroupName)
    * **Расположение**: $(местоположение)
    * **Шаблон**: $(System.ArtifactsDirectory)/**«YourAzureReRepoArtifactSourceAlias»/arm-templates/storageAccount.json**
    * **Переопределение параметров шаблона**: -accountName $(storageAccountName)

1. Загрузите артефакты из управления источниками в учетную запись хранения. Для выполнения этой задачи выполняется задача Azure Pipeline. В рамках этой задачи URL-адрес контейнера для хранения данных и токен SAS Token могут быть отнесены к переменной в конвейерах Azure. Это означает, что его можно использовать повторно на протяжении всей фазы агента.

    Добавьте задачу **копирования файлов Azure** и установите следующие свойства:
    * **Источник:** $(System.ArtifactsDirectory)/**«YourAzureReRepoArtifactSourceAlias»/arm-templates/**
    * **Тип подключения Azure**: Менеджер ресурсов Azure
    * **Подписка на Azure:** Выберите соответствующую подписку Azure
    * **Тип назначения**: Azure Blob
    * **RM Хранение счета**: $(хранениеAccountName)
    * **Название контейнера**: шаблоны
    * **Контейнер для хранения URI**: templateContainerUri
    * **Контейнер хранения SAS Token**: templateContainerSasToken

1. Развертывание шаблона оркестратора. Напомним, шаблон оркестратора из ранее, вы заметите, что были параметры для хранения счета контейнера URL, в дополнение к маркеру SAS. Следует заметить, что переменные, необходимые в шаблоне «Менеджер ресурсов», либо хранятся в разделе переменных определения выпуска, либо были установлены из другой задачи Azure Pipelines (например, часть задачи Azure Blob Copy).

    Добавьте задачу **развертывания группы ресурсов Azure** и установите следующие свойства:
    * **Имя дисплея:** Развертывание пакета Azure
    * **Подписка на Azure:** Выберите соответствующую подписку Azure
    * **Действие**: Создание или обновление группы ресурсов
    * **Ресурсная группа**: $(ресурсGroupName)
    * **Расположение**: $(местоположение)
    * **Шаблон**: $(System.ArtifactsDirectory)/**«YourAzureReRepoArtifactSourceAlias»/arm-templates/deployment.json**
    * **Параметры шаблона переопределения:**```-templateContainerUri $(templateContainerUri) -templateContainerSasToken $(templateContainerSasToken) -batchAccountName $(batchAccountName) -batchAccountPoolName $(batchAccountPoolName) -applicationStorageAccountName $(applicationStorageAccountName)```

Распространенной практикой является использование задач Azure Key Vault. Если у директора службы (подключение к вашей подписке Azure) есть соответствующий набор политик доступа, он может загружать секреты из Хранилища ключей Azure и использоваться в качестве переменных в конвейере. Название секрета будет установлено с соответствующим значением. Например, секрет sshPassword может быть ссылкой с $ (sshPassword) в определении релиза.

1. Следующие шаги называются Azure CLI. Первый используется для создания приложения в Azure Batch. и загружать связанные пакеты.

    Добавьте задачу **Azure CLI** и установите следующие свойства:
    * **Имя дисплея:** Создание приложения в учетной записи пакета Azure
    * **Подписка на Azure:** Выберите соответствующую подписку Azure
    * **Местоположение сценария**: Встроконный сценарий
    * **Сценарий в составе**:```az batch application create --application-id $(batchApplicationId) --name $(batchAccountName) --resource-group $(resourceGroupName)```

1. Второй шаг используется для загрузки связанных пакетов в приложение. В нашем случае, ffmpeg файлы.

    Добавьте задачу **Azure CLI** и установите следующие свойства:
    * **Имя дисплея:** Загрузить пакет в учетную запись пакета Azure
    * **Подписка на Azure:** Выберите соответствующую подписку Azure
    * **Местоположение сценария**: Встроконный сценарий
    * **Сценарий в составе**:```az batch application package create --application-id $(batchApplicationId)  --name $(batchAccountName)  --resource-group $(resourceGroupName) --version $(batchApplicationVersion) --package-file=$(System.DefaultWorkingDirectory)/$(Release.Artifacts.{YourBuildArtifactSourceAlias}.BuildId).zip```

    > [!NOTE]
    > Номер версии пакета приложений настроен на переменную. Это удобно, если перезапись предыдущих версий пакета работает для вас, и если вы хотите вручную управлять номером версии пакета, отодвинутого в Azure Batch.

1. Создайте новый релиз, выбрав **релиз > Создать новый релиз.** После срабатывания выберите ссылку на новый релиз для просмотра статуса.

1. Вы можете просмотреть живой выход от агента, выбрав кнопку **Журналы** под вашей средой.

    ![Просмотр состояния релиза](media/batch-ci-cd/Release-5.jpg)

### <a name="testing-the-environment"></a>Тестирование среды

После настройки среды подтвердите успешное выполнение следующих тестов.

Подключитесь к новой учетной записи Azure Batch, используя CLI Azure из запроса команды PowerShell.

* Войдите в свою учетную запись Azure `az login` и следуйте инструкциям по аутентификации.
* Теперь проверить подлинность учетной записи пакета:`az batch account login -g <resourceGroup> -n <batchAccount>`

#### <a name="list-the-available-applications"></a>Перечислите доступные приложения

```azurecli
az batch application list -g <resourcegroup> -n <batchaccountname>
```

#### <a name="check-the-pool-is-valid"></a>Проверка того, что пул действителен

```azurecli
az batch pool list
```

Обратите внимание `currentDedicatedNodes` на значение вывода этой команды. Это значение корректируется в следующем тесте.

#### <a name="resize-the-pool"></a>Изменить размер бассейна

Изменяйте пул так, что есть вычислительные узлы, доступные для тестирования задания и задачи, проверьте с командой списка пулов, чтобы увидеть текущее состояние до завершения изрядного размера и доступных узлов

```azurecli
az batch pool resize --pool-id <poolname> --target-dedicated-nodes 4
```

## <a name="next-steps"></a>Дальнейшие действия

В дополнение к этой статье, Есть два учебника, которые используют ffmpeg, используя .NET и Python. Дополнительные сведения о том, как взаимодействовать с учетной записью Batch через простое приложение, смотрите эти учебники.

* [Запуск параллельной рабочей нагрузки с помощью пакета Azure с использованием Python API](tutorial-parallel-python.md)
* [Запуск параллельной рабочей нагрузки с помощью пакетной службы Azure с использованием .NET API](tutorial-parallel-dotnet.md)
