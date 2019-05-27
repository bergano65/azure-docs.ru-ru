---
title: Непрерывные интеграция и поставка в Фабрике данных Azure | Документация Майкрософт
description: Сведения об использовании непрерывных интеграции и поставки для перемещения конвейеров Фабрики данных из одной среды (разработки, тестирования, рабочей) в другую.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/17/2019
author: gauravmalhot
ms.author: gamal
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: 76962975705ff53a292f41a0a54e42c5f2991a2c
ms.sourcegitcommit: 13cba995d4538e099f7e670ddbe1d8b3a64a36fb
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/22/2019
ms.locfileid: "66002582"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Непрерывные интеграция и поставка в Фабрике данных Azure

Непрерывная интеграция — это способ автоматического тестирования каждого изменения, внесенного в базу кода. Непрерывная поставка следует за проверкой, которая выполняется во время непрерывной интеграции. Она передает изменения в промежуточную или рабочую систему.

В Фабрике данных Azure под непрерывными интеграцией и поставкой подразумевается перемещение конвейеров Фабрики данных из одной среды (разработки, тестирования, рабочей) в другую. Чтобы выполнить непрерывные интеграцию и поставку, можно использовать интеграцию через пользовательский интерфейс Фабрики данных с шаблонами Azure Resource Manager. Если выбрать вариант **Шаблон ARM**, пользовательский интерфейс фабрики данных создаст шаблон Resource Manager. При выборе варианта **Export ARM template** (Экспорт шаблона ARM) портал создает шаблон Resource Manager для фабрики данных и файл конфигурации, включающий все строки подключения, а также другие параметры. Затем необходимо создать один файл конфигурации для каждой среды (разработки, тестирования, рабочей). Главный файл шаблона Resource Manager одинаков для всех сред.

Уделите 9 минут вашего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="create-a-resource-manager-template-for-each-environment"></a>Создание шаблона Resource Manager для каждой среды
Выберите **Export ARM template** (Экспорт шаблона ARM), чтобы экспортировать шаблон Resource Manager в фабрику данных в среде разработки.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

Затем перейдите к фабрикам данных в тестовой и рабочей средах и выберите **Import ARM template** (Импорт шаблона ARM).

![](media/continuous-integration-deployment/continuous-integration-image2.png)

После этого вы перейдете на портал Azure, где сможете импортировать экспортированный шаблон. Выберите **Build your own template in the editor** (Создать собственный шаблон в редакторе), а затем — **Загрузить файл** и выберите созданный шаблон Resource Manager. Укажите параметры, и фабрика данных и весь конвейер импортируются в вашу рабочую среду.

![](media/continuous-integration-deployment/continuous-integration-image3.png)

![](media/continuous-integration-deployment/continuous-integration-image4.png)

Нажмите кнопку **Загрузить файл**, выберите экспортированный шаблон Resource Manager и укажите все значения конфигурации (например, связанные службы).

![](media/continuous-integration-deployment/continuous-integration-image5.png)

**Строки подключения**. Можно найти сведения, необходимые для создания строки подключения, в статьях об отдельных соединителях. Дополнительные сведения о базе данных SQL Azure см. в статье [Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure](connector-azure-sql-database.md). Чтобы проверить правильную строку (например, для связанной службы), можно также открыть представление кода для ресурса в пользовательском интерфейсе Фабрики данных. Однако в представлении кода пароль или часть ключа учетной записи строки подключения удаляется. Чтобы открыть представление кода, щелкните значок, который выделен на следующем снимке экрана.

![Откройте представление кода, чтобы просмотреть строку подключения](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="continuous-integration-lifecycle"></a>Жизненный цикл непрерывной интеграции
Ниже приведен полный жизненный цикл для непрерывных интеграции и поставки, который можно использовать после включения интеграции Git Azure Repos в пользовательском интерфейсе Фабрики данных:

1.  Настройте фабрику данных в среде разработки с Azure Repos, в которой все разработчики могут создавать ресурсы Фабрики данных, такие как конвейеры, наборы данных и т. д.

1.  Затем разработчики могут изменить ресурсы (например, конвейеры). Во время этого процесса можно выбрать команду **Отладить**, чтобы увидеть, как работает конвейер с последними изменениями.

1.  После того как разработчики внесут изменения, они могут создать запрос Pull из своей ветви в главную ветвь (или ветвь совместной работы), чтобы их коллеги могли просмотреть изменения.

1.  Внесенные в главную ветвь изменения можно опубликовать в фабрике в среде разработки, выбрав **Опубликовать**.

1.  Если команда готова применить изменения к фабрикам в тестовой и рабочей средах, можно экспортировать шаблон Resource Manager из главной ветви или любой другой, если главная поддерживает активную фабрику данных в среде разработки.

1.  Экспортированный шаблон Resource Manager можно развернуть с файлами различных параметров в фабрики в тестовой и рабочей средах.

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Автоматизация непрерывной интеграции с помощью выпусков Azure Pipelines

Ниже приведены шаги настройки выпуска Azure Pipelines для автоматизации развертывания фабрики данных в различные среды.

![Схема непрерывной интеграции с помощью Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Требования

-   Подписка Azure, связанная с Team Foundation Server или Azure Repos и использующая  [*конечную точку службы Azure Resource Manager*](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Фабрика данных с интеграцией Git Azure Repos настроена.

-   Хранилище  [Azure Key Vault](https://azure.microsoft.com/services/key-vault/), содержащее секреты.

### <a name="set-up-an-azure-pipelines-release"></a>Настройка выпуска Azure Pipelines

1.  Перейдите на страницу Azure Repos в настроенный проект с Фабрикой данных.

1.  В верхнем меню щелкните **Azure Pipelines** &gt; **Выпуски** &gt; **Создать определение выпуска**.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Выберите шаблон **Пустой процесс**.

1.  Введите имя своей среды.

1.  Добавьте артефакт Git и выберите настроенный репозиторий с фабрикой данных. Выберите `adf_publish` в качестве стандартной ветви с последней версией по умолчанию.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Добавьте задачу развертывания Azure Resource Manager.

    a.  Создайте новую задачу, найдите и добавьте **Развертывание группы ресурсов Azure**.

    2.  В задаче "Развертывание" выберите подписку, группу ресурсов и расположение для целевой фабрики данных и при необходимости предоставьте учетные данные.

    c.  Выберите действие **Create or Update Resource Group** (Создание или изменение группы ресурсов).

    d.  Выберите **…** в поле **Шаблон**. Найдите шаблон Resource Manager (*ARMTemplateForFactory.json*), созданный с помощью действия публикации на портале. Найдите этот файл в папке `<FactoryName>` в ветви `adf_publish`.

    д.  Выполните те же действия для файла параметров. Выберите правильный файл. Выбор зависит от того, была ли создана копия или используется файл по умолчанию *ARMTemplateParametersForFactory.json*.

    Е.  Выберите **…** рядом с полем **Переопределить параметры шаблона** и заполните сведения для целевой фабрики данных. Для учетных данных, полученных из хранилища ключей, используйте одно и то же имя секрета в следующем формате: если имя секрета — `cred1`, введите `"$(cred1)"` (между кавычками).

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    ж. Выберите **добавочный** режим развертывания.

    > [!WARNING]
    > При выборе **полного** режима развертывания существующие ресурсы могут быть удалены, в том числе ресурсы в целевой группе ресурсов, которые не определены в шаблоне Resource Manager.

1.  Сохраните конвейер выпуска.

1.  Создайте выпуск из этого конвейера выпуска.

    ![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="optional---get-the-secrets-from-azure-key-vault"></a>Получение секретов из Azure Key Vault (необязательно)

Если вам нужно передать секреты в шаблоне Azure Resource Manager, рекомендуем использовать Azure Key Vault с выпуском Azure Pipelines.

Обработать секреты можно двумя способами.

1.  Добавьте секреты в файл параметров. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](../azure-resource-manager/resource-manager-keyvault-parameter.md).

    -   Создайте копию файла параметров, отправленного в ветвь публикации, и задайте значения параметров, которые необходимо получить из хранилища ключей в следующем формате:

    ```json
    {
        "parameters": {
            "azureSqlReportingDbPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/<subId>/resourceGroups/<resourcegroupId> /providers/Microsoft.KeyVault/vaults/<vault-name> "
                    },
                    "secretName": " < secret - name > "
                }
            }
        }
    }
    ```

    -   При использовании этого метода секрет автоматически извлекается из хранилища ключей.

    -   Файл параметров также должен находиться в ветви публикации.

1.  Перед развертыванием Azure Resource Manager, описанным в предыдущем разделе, добавьте [задачу Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault):

    -   Выберите вкладку **Задачи**, создайте новую задачу, найдите и добавьте **Azure Key Vault**.

    -   В задаче Key Vault выберите подписку, в которой было создано хранилище ключей, при необходимости введите учетные данные, а затем выберите хранилище ключей.

    ![](media/continuous-integration-deployment/continuous-integration-image8.png)

### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Предоставление разрешений для агента Azure Pipelines
Задача Azure Key Vault может завершиться ошибкой времени выполнения fIntegration с отказано в доступе. Загрузите журналы для выпуска и найдите файл `.ps1` с помощью команды для предоставления разрешений агенту Azure Pipelines. Вы можете выполнить команду напрямую или же скопировать идентификатор участника из файла и вручную добавить политику доступа на портале Azure. (*Get* и *List* — минимальные требуемые разрешения.)

### <a name="update-active-triggers"></a>Обновление активных триггеров
При попытке обновления активных триггеров развертывание может завершиться сбоем. Чтобы обновить активные триггеры, необходимо вручную остановить и запустить их после развертывания. Для этого можно добавить задачу Azure PowerShell, как показано в следующем примере.

1.  На вкладке задач выпуска найдите и добавьте **Azure PowerShell**.

1.  Выберите **Azure Resource Manager** в качестве типа подключения, а затем — свою подписку.

1.  Выберите **Встроенный скрипт** в качестве типа скрипта, а затем укажите код. Следующий пример останавливает триггеры.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Вы можете выполнить те же шаги и использовать аналогичный код (с функцией `Start-AzDataFactoryV2Trigger`), чтобы перезапустить триггеры после развертывания.

> [!IMPORTANT]
> В сценариях непрерывных интеграции и развертывания тип среды выполнения интеграции в разных средах должен совпадать. Например, если у вас есть *локальная* среда выполнения интеграции в среде разработки, ей необходим тип *Локальная* в других средах, таких как рабочая и тестовая. Аналогично, если вы совместно используете среды выполнения интеграции в нескольких средах, необходимо настроить их в качестве *связанных локальных сред* во всех средах (разработки, тестирования и рабочей).

## <a name="sample-deployment-template"></a>Пример шаблона развертывания

Ниже приведен пример шаблона развертывания, который можно импортировать в Azure Pipelines.

```json
{
    "source": 2,
    "id": 1,
    "revision": 51,
    "name": "Data Factory Prod Deployment",
    "description": null,
    "createdBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "createdOn": "2018-03-01T22:57:25.660Z",
    "modifiedBy": {
        "displayName": "Sample User",
        "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "uniqueName": "sampleuser@microsoft.com",
        "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
        "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
    },
    "modifiedOn": "2018-03-14T17:58:11.643Z",
    "isDeleted": false,
    "path": "\\",
    "variables": {},
    "variableGroups": [],
    "environments": [{
        "id": 1,
        "name": "Prod",
        "rank": 1,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserprod"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 1
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 2
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 3
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param\n(\n    [parameter(Mandatory = $false)] [String] $rootFolder=\"C:\\Users\\sampleuser\\Downloads\\arm_template\",\n    [parameter(Mandatory = $false)] [String] $armTemplate=\"$rootFolder\\arm_template.json\",\n    [parameter(Mandatory = $false)] [String] $armTemplateParameters=\"$rootFolder\\arm_template_parameters.json\",\n    [parameter(Mandatory = $false)] [String] $domain=\"microsoft.onmicrosoft.com\",\n    [parameter(Mandatory = $false)] [String] $TenantId=\"72f988bf-86f1-41af-91ab-2d7cd011db47\",\n    [parame",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": "5.*"
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "secret1",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/1"
    }, {
        "id": 2,
        "name": "Staging",
        "rank": 2,
        "owner": {
            "displayName": "Sample User",
            "url": "https://pde14b1dc-d2c9-49e5-88cb-45ccd58d0335.codex.ms/vssps/_apis/Identities/c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "id": "c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "uniqueName": "sampleuser@microsoft.com",
            "imageUrl": "https://sampleuser.visualstudio.com/_api/_common/identityImage?id=c9f828d1-2dbb-4e39-b096-f1c53d82bc2c",
            "descriptor": "aad.M2Y2N2JlZGUtMDViZC03ZWI3LTgxYWMtMDcwM2UyODMxNTBk"
        },
        "variables": {
            "factoryName": {
                "value": "sampleuserstaging"
            }
        },
        "variableGroups": [],
        "preDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 4
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 1
            }
        },
        "deployStep": {
            "id": 5
        },
        "postDeployApprovals": {
            "approvals": [{
                "rank": 1,
                "isAutomated": true,
                "isNotificationOn": false,
                "id": 6
            }],
            "approvalOptions": {
                "requiredApproverCount": null,
                "releaseCreatorCanBeApprover": false,
                "autoTriggeredAndPreviousEnvironmentApprovedCanBeSkipped": false,
                "enforceIdentityRevalidation": false,
                "timeoutInMinutes": 0,
                "executionOrder": 2
            }
        },
        "deployPhases": [{
            "deploymentInput": {
                "parallelExecution": {
                    "parallelExecutionType": "none"
                },
                "skipArtifactsDownload": false,
                "artifactsDownloadInput": {
                    "downloadInputs": []
                },
                "queueId": 19,
                "demands": [],
                "enableAccessToken": false,
                "timeoutInMinutes": 0,
                "jobCancelTimeoutInMinutes": 1,
                "condition": "succeeded()",
                "overrideInputs": {}
            },
            "rank": 1,
            "phaseType": 1,
            "name": "Run on agent",
            "workflowTasks": [{
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "# You can write your azure powershell scripts inline here. \n# You can also pass predefined and custom variables to this script using arguments",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $true",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }, {
                "taskId": "1e244d32-2dd4-4165-96fb-b7441ca9331e",
                "version": "1.*",
                "name": "Azure Key Vault: sampleuservault",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "KeyVaultName": "sampleuservault",
                    "SecretsFilter": "*"
                }
            }, {
                "taskId": "94a74903-f93f-4075-884f-dc11f34058b4",
                "version": "2.*",
                "name": "Azure Deployment:Create Or Update Resource Group action on sampleuser-datafactory",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceName": "e4e2ef4b-8289-41a6-ba7c-92ca469700aa",
                    "action": "Create Or Update Resource Group",
                    "resourceGroupName": "sampleuser-datafactory",
                    "location": "East US",
                    "templateLocation": "Linked artifact",
                    "csmFileLink": "",
                    "csmParametersFileLink": "",
                    "csmFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateForFactory.json",
                    "csmParametersFile": "$(System.DefaultWorkingDirectory)/Dev/ARMTemplateParametersForFactory.json",
                    "overrideParameters": "-factoryName \"$(factoryName)\" -linkedService1_connectionString \"$(linkedService1-connectionString)\" -linkedService2_connectionString \"$(linkedService2-connectionString)\"",
                    "deploymentMode": "Incremental",
                    "enableDeploymentPrerequisites": "None",
                    "deploymentGroupEndpoint": "",
                    "project": "",
                    "deploymentGroupName": "",
                    "copyAzureVMTags": "true",
                    "outputVariable": "",
                    "deploymentOutputs": ""
                }
            }, {
                "taskId": "72a1931b-effb-4d2e-8fd8-f8472a07cb62",
                "version": "2.*",
                "name": "Azure PowerShell script: FilePath",
                "refName": "",
                "enabled": true,
                "alwaysRun": false,
                "continueOnError": false,
                "timeoutInMinutes": 0,
                "definitionType": "task",
                "overrideInputs": {},
                "condition": "succeeded()",
                "inputs": {
                    "ConnectedServiceNameSelector": "ConnectedServiceNameARM",
                    "ConnectedServiceName": "",
                    "ConnectedServiceNameARM": "16a37943-8b58-4c2f-a3d6-052d6f032a07",
                    "ScriptType": "FilePath",
                    "ScriptPath": "$(System.DefaultWorkingDirectory)/Dev/deployment.ps1",
                    "Inline": "param(\n$x,\n$y,\n$z)\nwrite-host \"----------\"\nwrite-host $x\nwrite-host $y\nwrite-host $z | ConvertTo-SecureString\nwrite-host \"----------\"",
                    "ScriptArguments": "-rootFolder \"$(System.DefaultWorkingDirectory)/Dev/\" -DataFactoryName $(factoryname) -predeployment $false",
                    "TargetAzurePs": "LatestVersion",
                    "CustomTargetAzurePs": ""
                }
            }]
        }],
        "environmentOptions": {
            "emailNotificationType": "OnlyOnFailure",
            "emailRecipients": "release.environment.owner;release.creator",
            "skipArtifactsDownload": false,
            "timeoutInMinutes": 0,
            "enableAccessToken": false,
            "publishDeploymentStatus": true,
            "badgeEnabled": false,
            "autoLinkWorkItems": false
        },
        "demands": [],
        "conditions": [{
            "name": "ReleaseStarted",
            "conditionType": 1,
            "value": ""
        }],
        "executionPolicy": {
            "concurrencyCount": 1,
            "queueDepthCount": 0
        },
        "schedules": [],
        "retentionPolicy": {
            "daysToKeep": 30,
            "releasesToKeep": 3,
            "retainBuild": true
        },
        "processParameters": {
            "dataSourceBindings": [{
                "dataSourceName": "AzureRMWebAppNamesByType",
                "parameters": {
                    "WebAppKind": "$(WebAppKind)"
                },
                "endpointId": "$(ConnectedServiceName)",
                "target": "WebAppName"
            }]
        },
        "properties": {},
        "preDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "postDeploymentGates": {
            "id": 0,
            "gatesOptions": null,
            "gates": []
        },
        "badgeUrl": "https://sampleuser.vsrm.visualstudio.com/_apis/public/Release/badge/19749ef3-2f42-49b5-9696-f28b49faebcb/1/2"
    }],
    "artifacts": [{
        "sourceId": "19749ef3-2f42-49b5-9696-f28b49faebcb:a6c88f30-5e1f-4de8-b24d-279bb209d85f",
        "type": "Git",
        "alias": "Dev",
        "definitionReference": {
            "branches": {
                "id": "adf_publish",
                "name": "adf_publish"
            },
            "checkoutSubmodules": {
                "id": "",
                "name": ""
            },
            "defaultVersionSpecific": {
                "id": "",
                "name": ""
            },
            "defaultVersionType": {
                "id": "latestFromBranchType",
                "name": "Latest from default branch"
            },
            "definition": {
                "id": "a6c88f30-5e1f-4de8-b24d-279bb209d85f",
                "name": "Dev"
            },
            "fetchDepth": {
                "id": "",
                "name": ""
            },
            "gitLfsSupport": {
                "id": "",
                "name": ""
            },
            "project": {
                "id": "19749ef3-2f42-49b5-9696-f28b49faebcb",
                "name": "Prod"
            }
        },
        "isPrimary": true
    }],
    "triggers": [{
        "schedule": {
            "jobId": "b5ef09b6-8dfd-4b91-8b48-0709e3e67b2d",
            "timeZoneId": "UTC",
            "startHours": 3,
            "startMinutes": 0,
            "daysToRelease": 31
        },
        "triggerType": 2
    }],
    "releaseNameFormat": "Release-$(rev:r)",
    "url": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1",
    "_links": {
        "self": {
            "href": "https://sampleuser.vsrm.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_apis/Release/definitions/1"
        },
        "web": {
            "href": "https://sampleuser.visualstudio.com/19749ef3-2f42-49b5-9696-f28b49faebcb/_release?definitionId=1"
        }
    },
    "tags": [],
    "properties": {
        "DefinitionCreationSource": {
            "$type": "System.String",
            "$value": "ReleaseNew"
        }
    }
}
```

## <a name="sample-script-to-stop-and-restart-triggers-and-clean-up"></a>Пример сценария остановки и перезагрузки триггеров и очистки

Ниже приведен пример скрипта для остановки триггеров перед развертыванием и их последующего перезапуска. Скрипт также содержит код для удаления ресурсов. Чтобы установить последнюю версию Azure PowerShell, ознакомьтесь со статьей [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $rootFolder,
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
$triggersTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/triggers" }
$triggerNames = $triggersTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$activeTriggerNames = $triggersTemplate | Where-Object { $_.properties.runtimeState -eq "Started" -and ($_.properties.pipelines.Count -gt 0 -or $_.properties.pipeline.pipelineReference -ne $null)} | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
$deletedtriggers = $triggersADF | Where-Object { $triggerNames -notcontains $_.Name }
$triggerstostop = $triggerNames | where { ($triggersADF | Select-Object name).name -contains $_ }

if ($predeployment -eq $true) {
    #Stop all triggers
    Write-Host "Stopping deployed triggers"
    $triggerstostop | ForEach-Object { 
        Write-host "Disabling trigger " $_
        Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-AzDataFactoryV2LinkedService -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $linkedservicesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/linkedservices" }
    $linkedservicesNames = $linkedservicesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedlinkedservices = $linkedservicesADF | Where-Object { $linkedservicesNames -notcontains $_.Name }
    #Integrationruntimes
    Write-Host "Getting integration runtimes"
    $integrationruntimesADF = Get-AzDataFactoryV2IntegrationRuntime -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $integrationruntimesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/integrationruntimes" }
    $integrationruntimesNames = $integrationruntimesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedintegrationruntimes = $integrationruntimesADF | Where-Object { $integrationruntimesNames -notcontains $_.Name }

    #Delete resources
    Write-Host "Deleting triggers"
    $deletedtriggers | ForEach-Object { 
        Write-Host "Deleting trigger "  $_.Name
        $trig = Get-AzDataFactoryV2Trigger -name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
        if ($trig.RuntimeState -eq "Started") {
            Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.Name -Force 
        }
        Remove-AzDataFactoryV2Trigger -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting pipelines"
    $deletedpipelines | ForEach-Object { 
        Write-Host "Deleting pipeline " $_.Name
        Remove-AzDataFactoryV2Pipeline -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting datasets"
    $deleteddataset | ForEach-Object { 
        Write-Host "Deleting dataset " $_.Name
        Remove-AzDataFactoryV2Dataset -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting linked services"
    $deletedlinkedservices | ForEach-Object { 
        Write-Host "Deleting Linked Service " $_.Name
        Remove-AzDataFactoryV2LinkedService -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }
    Write-Host "Deleting integration runtimes"
    $deletedintegrationruntimes | ForEach-Object { 
        Write-Host "Deleting integration runtime " $_.Name
        Remove-AzDataFactoryV2IntegrationRuntime -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
    }

    if ($deleteDeployment -eq $true) {
        Write-Host "Deleting ARM deployment ... under resource group: " $ResourceGroupName
        $deployments = Get-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName
        $deploymentsToConsider = $deployments | Where { $_.DeploymentName -like "ArmTemplate_master*" -or $_.DeploymentName -like "ArmTemplateForFactory*" } | Sort-Object -Property Timestamp -Descending
        $deploymentName = $deploymentsToConsider[0].DeploymentName

       Write-Host "Deployment to be deleted: " $deploymentName
        $deploymentOperations = Get-AzResourceGroupDeploymentOperation -DeploymentName $deploymentName -ResourceGroupName $ResourceGroupName
        $deploymentsToDelete = $deploymentOperations | Where { $_.properties.targetResource.id -like "*Microsoft.Resources/deployments*" }

        $deploymentsToDelete | ForEach-Object { 
            Write-host "Deleting inner deployment: " $_.properties.targetResource.id
            Remove-AzResourceGroupDeployment -Id $_.properties.targetResource.id
        }
        Write-Host "Deleting deployment: " $deploymentName
        Remove-AzResourceGroupDeployment -ResourceGroupName $ResourceGroupName -Name $deploymentName
    }

    #Start Active triggers - After cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Использование пользовательских параметров с шаблоном Resource Manager

Если вы находитесь в режиме GIT, можно переопределить свойства по умолчанию в шаблоне Resource Manager для установки свойств, параметризуются в шаблоне и свойства, которые являются фиксированными. Может потребоваться переопределить шаблон параметризации по умолчанию в следующих сценариях:

* Использовать автоматизированные CI/CD и вы хотите изменить некоторые свойства во время развертывания Resource Manager, но не параметризованные свойства по умолчанию.
* Фабрикой настолько велико, что шаблон Resource Manager по умолчанию является недопустимым, так как она содержит более максимально допустимое параметров (256).

В этих условиях, чтобы переопределить параметризации шаблона по умолчанию, создайте файл с именем *arm шаблон параметров definition.json* в корневой папке репозитория. Имя файла должно совпадать. Фабрика данных пытается прочитать этот файл из любой ветви, в данный момент на портале фабрики данных Azure, а не только из ветви совместной работы. Можно создать или изменить его в закрытой ветке, где можно проверить изменения с помощью **шаблон экспорта ARM** в пользовательском Интерфейсе. Затем файл можно объединить в ветвь совместной работы. Если файл не найден, используется шаблон по умолчанию.


### <a name="syntax-of-a-custom-parameters-file"></a>Синтаксис пользовательского файла параметров

Ниже приведены некоторые рекомендации для использования при создании файла пользовательских параметров. Этот файл состоит из раздела для каждого типа сущности: триггера, конвейера, linkedservice, dataset, integrationruntime и т. д.
* Введите путь к свойству в типе соответствующие сущности.
* Если задано имя свойства "\*'', указывают, что требуется параметризовать соответствующие все свойства в нем (только до уровня первого уровня, не рекурсивно). Можно также предоставить все исключения.
* При задании значения свойства в виде строки вы указываете, что хотите параметризовать свойство. Используйте формат `<action>:<name>:<stype>`.
   *  `<action>` может принимать одно из следующих символов:
      * `=` означает, что сохранение текущего значения как значение по умолчанию для параметра.
      * `-` означает, что не следует хранить значение по умолчанию для параметра.
      * `|` является особым случаем для секретов из хранилища ключей Azure для строки подключения или разделов.
   * `<name>` — Имя параметра. Если поле пусто, он принимает имя свойства. Если значение начинается с `-` символа, имя сокращается. Например `AzureStorage1_properties_typeProperties_connectionString` бы быть сокращено до `AzureStorage1_connectionString`.
   * `<stype>` Представляет тип параметра. Если `<stype>` является пустым, по умолчанию используется тип `string`. Поддерживаемые значения: `string`, `bool`, `number`, `object`, и `securestring`.
* Когда вы можете указать массив в файле определения, вы можете указать, что соответствующее свойство в шаблоне является массивом. Фабрика данных выполняет итерацию всех объектов в массиве с помощью определения, указанного в объекте среды выполнения интеграции в массиве. Второй объект, строка, становится именем свойства, которое используется в качестве имени параметра для каждой итерации.
* Определение, является вспомогательным инструментом для экземпляра ресурса невозможна. Любое определение применяется ко всем ресурсам этого типа.
* По умолчанию все защищенные строки, такие как секретов Key Vault и защищенные строки, например строки подключения, ключи и маркеры, подлежащих параметризации.
 
## <a name="sample-parameterization-template"></a>Пример шаблона параметризации

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
        "properties": {
            "activities": [{
                "typeProperties": {
                    "waitTimeInSeconds": "-::number",
                    "headers": "=::object"
                }
            }]
        }
    },
    "Microsoft.DataFactory/factories/integrationRuntimes": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "typeProperties": {
                "recurrence": {
                    "*": "=",
                    "interval": "=:triggerSuffix:number",
                    "frequency": "=:-freq"
                },
                "maxConcurrency": "="
            }
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "secretAccessKey": "|"
                }
            }
        },
        "AzureDataLakeStore": {
            "properties": {
                "typeProperties": {
                    "dataLakeStoreUri": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "properties": {
            "typeProperties": {
                "*": "="
            }
        }
    }
}
```

### <a name="explanation"></a>Объяснение:

#### <a name="pipelines"></a>Конвейеры
    
* Любое свойство в пути действий/typeProperties/waitTimeInSeconds параметризован. Это означает, что любое действие в конвейере, имеющий свойство на уровне кода с именем `waitTimeInSeconds` (например, `Wait` действия) параметризован в виде числа, с именем по умолчанию. Но он не имеет значения по умолчанию в шаблоне Resource Manager. Он будет обязательный параметр во время развертывания Resource Manager.
* Аналогичным образом, свойство, называемую `headers` (например, в `Web` действия) является параметризованным с типом `object` (JObject). Он имеет значение по умолчанию, которое имеет то же значение, как и в фабрику источника.

#### <a name="integrationruntimes"></a>IntegrationRuntimes

* Только свойства, а все свойства, по пути `typeProperties` параметризуются вместе со значениями по умолчанию. Например, начиная с текущей схемы, имеется два свойства в разделе **IntegrationRuntimes** свойства типа: `computeProperties` и `ssisProperties`. Оба типа свойства создаются с помощью их значения по умолчанию и типы (объект).

#### <a name="triggers"></a>Триггеры

* В разделе `typeProperties`, параметризуются два свойства. Первый из них — `maxConcurrency`, которое указывается должен иметь значение по умолчанию и будет иметь тип `string`. Он имеет имя параметра по умолчанию `<entityName>_properties_typeProperties_maxConcurrency`.
* `recurrence` Свойство также является параметризованным. В нем параметризовать как строки со значениями по умолчанию и имена параметров указаны все свойства, на этом уровне. Исключением является `interval` свойство, которое является параметризованным в качестве числовой тип и с именем параметра суффиксом `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Аналогичным образом `freq` свойство представляет собой строку и параметризован в виде строки. Тем не менее `freq` свойство записывается в параметрической без значения по умолчанию. Имя является сокращено и суффикс. Например, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Связанные службы является уникальным. Так как связанные службы и наборы данных могут быть несколько типов, можно указать настройки определенного типа. Например, может сказать, что для всех связанных служб типа `AzureDataLakeStore`, определенного шаблона будет применен и для всех остальных (через \*) применяется другой шаблон.
* В приведенном выше примере `connectionString` свойство, будет параметризовано как `securestring` значение, он не имеет значения по умолчанию, и будет иметь параметр сокращенное имя, которое имеет суффикс `connectionString`.
* Свойство `secretAccessKey`, тем не менее, может быть `AzureKeyVaultSecret` (например, `AmazonS3` связанная служба). Таким образом он автоматически параметризован как секрет хранилища ключей Azure, и он извлекается из хранилища ключей, настроенные в фабрику источника. Также можно параметризовать хранилища ключей, сам.

#### <a name="datasets"></a>Наборы данных

* Несмотря на то, что настройка определенного типа недоступна для наборов данных, конфигурации можно указать без необходимости явно \*-настройки на уровне. В приведенном выше примере все свойства набора данных в группе `typeProperties` параметризуются.

Параметризация шаблона по умолчанию можно изменить, но это текущего шаблона. Это будет полезно в том случае, если необходимо добавить одно дополнительное свойство как параметр, а также если вы не хотите потерять существующие параметризации и необходимо повторно создать их.


```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }

        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```

**Пример**: Добавление интерактивных Databricks идентификатора кластера (из связанной службы Databricks) к файлу параметров:

```
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/integrationRuntimes":{
        "properties": {
            "typeProperties": {
                "ssisProperties": {
                    "catalogInfo": {
                        "catalogServerEndpoint": "=",
                        "catalogAdminUserName": "=",
                        "catalogAdminPassword": {
                            "value": "-::secureString"
                        }
                    },
                    "customSetupScriptProperties": {
                        "sasToken": {
                            "value": "-::secureString"
                        }
                    }
                },
                "linkedInfo": {
                    "key": {
                        "value": "-::secureString"
                    },
                    "resourceId": "="
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/triggers": {
        "properties": {
            "pipelines": [{
                    "parameters": {
                        "*": "="
                    }
                },  
                "pipelineReference.referenceName"
            ],
            "pipeline": {
                "parameters": {
                    "*": "="
                }
            },
            "typeProperties": {
                "scope": "="
            }
 
        }
    },
    "Microsoft.DataFactory/factories/linkedServices": {
        "*": {
            "properties": {
                "typeProperties": {
                    "accountName": "=",
                    "username": "=",
                    "userName": "=",
                    "accessKeyId": "=",
                    "servicePrincipalId": "=",
                    "userId": "=",
                    "clientId": "=",
                    "clusterUserName": "=",
                    "clusterSshUserName": "=",
                    "hostSubscriptionId": "=",
                    "clusterResourceGroup": "=",
                    "subscriptionId": "=",
                    "resourceGroupName": "=",
                    "tenant": "=",
                    "dataLakeStoreUri": "=",
                    "baseUrl": "=",
                    "database": "=",
                    "serviceEndpoint": "=",
                    "batchUri": "=",
                    "databaseName": "=",
                    "systemNumber": "=",
                    "server": "=",
                    "url":"=",
                    "aadResourceId": "=",
                    "connectionString": "|:-connectionString:secureString",
                    "existingClusterId": "-"
                }
            }
        },
        "Odbc": {
            "properties": {
                "typeProperties": {
                    "userName": "=",
                    "connectionString": {
                        "secretName": "="
                    }
                }
            }
        }
    },
    "Microsoft.DataFactory/factories/datasets": {
        "*": {
            "properties": {
                "typeProperties": {
                    "folderPath": "=",
                    "fileName": "="
                }
            }
        }}
}
```


## <a name="linked-resource-manager-templates"></a>Связанные шаблоны Resource Manager

Если вы настроили непрерывную интеграцию и развертывание (CI/CD) для своих фабрик данных, вы можете заметить, что при увеличении размера фабрики станут актуальны ограничения шаблона Resource Manager, такие как максимальное число ресурсов или максимальное количество полезных данных в ресурсе. Для таких случаев, а также создания полного шаблона Resource Manager для фабрики фабрика данных теперь также создает связанные шаблоны Resource Manager. Таким образом, полезные данные всей фабрики можно разбить на несколько файлов, чтобы не превысить упомянутые ограничения.

Если у вас настроен Git, связанные шаблоны создаются и сохраняются вместе с полными шаблонами Resource Manager в ветви `adf_publish` в новой папке с именем `linkedTemplates`.

![Папка связанных шаблонов Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Связанные шаблоны Resource Manager обычно имеют главный шаблон и набор дочерних шаблонов, связанных с главным. Родительский шаблон называется `ArmTemplate_master.json`, а дочерние шаблоны именуются по образцу: `ArmTemplate_0.json`, `ArmTemplate_1.json` и т. д. Для перехода с использования полного шаблона Resource Manager на использование связанных шаблонов обновите вашу задачу CI/CD, чтобы она указывала `ArmTemplate_master.json` вместо `ArmTemplateForFactory.json` (то есть полного шаблона Resource Manager). Resource Manager также требует отправлять связанные шаблоны в учетную запись хранения, чтобы они были доступны в Azure во время развертывания. Дополнительные сведения см. в разделе [Развертывание связанных шаблонов ARM с помощью VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Не забудьте добавить сценарии фабрики данных в конвейер CI/CD до и после завершения развертывания.

Если у вас не настроен Git, связанные шаблоны доступны с помощью жеста **экспорта шаблона ARM**.

## <a name="best-practices-for-cicd"></a>Рекомендации для CI/CD

Если вы используете интеграцию Git с фабрикой данных и у вас есть конвейер CI/CD, перемещающий изменения из рабочей среды в тестовую, а затем в рабочую, рекомендуем следующее:

-   **Интеграция Git**. Достаточно настроить интеграцию Git для своей фабрики данных для разработки. Изменения в тестовую и рабочую среду развертываются в рамках процесса CI/CD, и им не требуется интеграция Git.

-   **Сценарий CI/CD фабрики данных**. Перед шагом развертывания с помощью Resource Manager в CI/CD необходимо остановить триггеры и выполнить разные виды очистки фабрики. Мы советуем использовать [этот сценарий](#sample-script-to-stop-and-restart-triggers-and-clean-up), так как он выполняет все эти задачи. Запустите сценарий один раз перед развертыванием и один раз после, используя соответствующие флаги.

-   **Среды выполнения интеграции и общий доступ.** Среды выполнения интеграции — это один из инфраструктурных компонентов в фабрике данных, которые реже подвергаются изменениям и похожи на всех этапах процесса CI/CD. Поэтому Фабрика данных ожидает, что имена и типы сред выполнения интеграции одинаковы на всех этапах CI/CD. Если вам требуется совместно использовать среды выполнения интеграции на всех этапах, например, локальные среды выполнения интеграции, один из способов реализовать это — разместить локальную среду IR в тернарной фабрике (только для хранения общих сред выполнения интеграции). Затем можно использовать их в среде разработки, тестирования и рабочей среде как связанные среды выполнения интеграции.

-   **Key Vault**. При использовании рекомендуемых связанных служб на базе Azure Key Vault вы можете применять преимущества этого решения еще на одном уровне, потенциально используя отдельные хранилища ключей для рабочей среды, среды разработки и тестирования. Вы также можете настроить отдельные уровни разрешений для каждого из них. Возможно, вы не хотите, чтобы у участников вашей команды были разрешения на доступ к секретам рабочей среды. Мы также рекомендуем использовать одинаковые имена секретов на всех этапах. Если вы используете те же имена, вам не требуется менять свои шаблоны Resource Manager в рамках CI/CD, так как единственное, что необходимо изменить, — это имя хранилища ключей, которое является одним из параметров шаблона Resource Manager.

## <a name="unsupported-features"></a>Неподдерживаемые функции

-   Вы не можете публиковать отдельные ресурсы, так как сущности фабрики данных зависят друг от друга. Например, триггеры зависят от конвейеров, конвейеры зависят от наборов данных и других конвейеров и т. д. Отслеживать меняющиеся зависимости непросто. Если бы можно было выбрать ресурсы для публикации вручную, вы могли бы выбрать только подмножество всего набора изменений, что могло бы привести к непредвиденному поведению после публикации.

-   Вы не можете опубликовать из частных ветвей.

-   Вы не можете размещать проекты на Bitbucket.
