---
title: Непрерывная интеграция и доставка на фабрике данных Azure
description: Сведения об использовании непрерывных интеграции и поставки для перемещения конвейеров Фабрики данных из одной среды (разработки, тестирования, рабочей) в другую.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: jroth
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: 6aad01808ad155b745b614d8de6009386f0d2914
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687966"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Непрерывная интеграция и доставка на фабрике данных Azure

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

## <a name="overview"></a>Обзор

Непрерывная интеграция — это практика тестирования каждого изменения, внесенного в вашу кодовую базу автоматически и как можно раньше.Непрерывная доставка следует за тестированием, которое происходит во время непрерывной интеграции и толкает изменения в постановку или производственную систему.

На фабрике данных Azure непрерывная интеграция и доставка (CI/CD) означает перемещение конвейеров Data Factory из одной среды (разработка, тестирование, производство) в другую. Для использования CI/CD можно использовать интеграцию Data Factory UX с шаблонами менеджера ресурсов Azure.

В ux Data Factory можно создать шаблон менеджера ресурсов из меню **выпадения шаблонов ARM** Template. При выборе **шаблона Export ARM Template**портал генерирует шаблон Resource Manager для фабрики данных и файл конфигурации, который включает в себя все строки подключения и другие параметры. Затем создается один файл конфигурации для каждой среды (разработка, тест, производство). Главный файл шаблона Resource Manager одинаков для всех сред.

Для девятиминутного знакомства с этой функцией и демонстрации, посмотрите это видео:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Жизненный цикл CI/CD

Ниже приведен пример жизненного цикла CI/CD на фабрике данных Azure, настроенной с помощью Azure Repos Git. Для получения дополнительной информации о том, как настроить репозиторий Git, [см.](source-control.md)

1.  Создается и настраивается фабрика данных разработки с помощью Azure Repos Git. Все разработчики должны иметь разрешение на авторресурсы Data Factory, такие как конвейеры и наборы данных.

1.  По мере внесения разработчиками изменений в ветви функций они оттаменяют свои конвейерные запуски с последними изменениями. Для получения дополнительной информации о том, [Iterative development and debugging with Azure Data Factory](iterative-development-debugging.md)как отладить запуск конвейера, см.

1.  После того, как разработчики удовлетворены изменениями, они создают запрос на вытягивание из ветви функций в ветку master или collaboration, чтобы их изменения были рассмотрены коллегами.

1.  После утверждения запроса на выдвижной и объединения изменений в основной ветке изменения изменения могут быть опубликованы на фабрике разработки.

1.  Когда группа готова развернуть изменения на испытательном заводе, а затем на производственном заводе, команда экспортирует шаблон Resource Manager из основной ветви.

1.  Экспортированный шаблон Resource Manager развертывается с различными параметрами на испытательный завод и производственную фабрику.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Создание шаблона Resource Manager для каждой среды

1. В списке **шаблонов ARM** выберите **шаблон Export ARM для** экспорта шаблона Resource Manager для вашей фабрики данных в среде разработки.

   ![Экспорт шаблона менеджера ресурсов](media/continuous-integration-deployment/continuous-integration-image1.png)

1. На фабриках по тестированию и производственным данным выберите **шаблон Import ARM.** После этого вы перейдете на портал Azure, где сможете импортировать экспортированный шаблон. Выберите **создать свой шаблон в редакторе,** чтобы открыть шаблонный редактор Resource Manager.

   ![Создайте свой собственный шаблон](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Выберите **файл Нагрузки,** а затем выберите шаблон сгенерированного менеджера ресурсов. Это файл **arm_template.json,** расположенный в файле .zip, экспортируемом в шаге 1.

   ![Изменить шаблон](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. В разделе настройки введите значения конфигурации, например связанные учетные данные службы. Когда вы закончите, выберите **«Покупка»** для развертывания шаблона «Менеджер ресурсов».

   ![Раздел настроек](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Строки подключения

Для получения информации о том, как настроить строки соединения, см. Дополнительные сведения о базе данных SQL Azure см. в статье [Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure](connector-azure-sql-database.md). Для проверки строки соединения можно открыть представление кода для ресурса в Data Factory UX. В представлении кода удаляется пароль или ключевая часть строки соединения. Чтобы открыть представление кода, выберите значок, выделенный здесь:

![Открытое представление кода, чтобы увидеть строку соединения](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Автоматизировать непрерывную интеграцию с помощью релизов Azure Pipelines

Ниже приводится руководство по настройке выпуска Azure Pipelines, которое автоматизирует развертывание фабрики данных в нескольких средах.

![Схема непрерывной интеграции с помощью Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Требования

-   Подписка Azure, связанная с Visual Studio Team Foundation Server или Azure Repos, [использующемконечную точку управления ресурсами Azure.](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager)

-   Фабрика данных, настроенная с интеграцией Azure Repos Git.

-    [Хранилище ключей Azure,](https://azure.microsoft.com/services/key-vault/) содержащее секреты для каждой среды.

### <a name="set-up-an-azure-pipelines-release"></a>Настройка выпуска Azure Pipelines

1.  В [Azure DevOps](https://dev.azure.com/)откройте проект, настроенный на фабрику данных.

1.  На левой стороне страницы выберите **Трубопроводы,** а затем выберите **релизы.**

    ![Выберите трубопроводы, релизы](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Выберите **новый конвейер**или, если у вас есть существующие конвейеры, выберите **новый,** а затем **новый конвейер выпуска.**

1.  Выберите шаблон **«Пустой» задания.**

    ![Выберите пустую работу](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  В поле **имен сцены** введите имя среды.

1.  Выберите **Добавить артефакт,** а затем выберите репозиторий, настроенный с фабрикой данных. Выберите **adf_publish** для **ветки по умолчанию.** Для **версии по умолчанию**выберите **Последний из ветки по умолчанию.**

    ![Добавление артефакта](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Добавьте задачу развертывания Azure Resource Manager.

    а.  В представлении этапа выберите **задачи этапа просмотра.**

    ![Представление этапа](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Создайте новое задание. Поиск для **развертывания группы ресурсов Azure,** а затем выберите **Добавить.**

    c.  В задаче развертывания выберите подписку, группу ресурсов и местоположение для фабрики целевых данных. При необходимости предоставьте учетные данные.

    d.  В списке **действий** выберите **Создать или обновить группу ресурсов.**

    д)  Выберите кнопку эллипсис **(...**) рядом с полем **шаблонов.** Просмотрите шаблон Azure Resource Manager, созданный с помощью **шаблона Import ARM в** [шаблоне «Создай диспетчер ресурсов» для каждого](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) раздела среды этой статьи. Ищите этот <FactoryName> файл в папке adf_publish ветви.

    е)  Выберите **...** рядом с полем **параметров шаблона** для выбора файла параметров. Файл, который вы выбираете, будет зависеть от того, создали ли вы копию или используете файл по умолчанию, ARMTemplateParametersForFactory.json.

    ж.  Выберите **...** рядом с **полем параметров шаблона Override** и введите информацию для фабрики целевых данных. Для учетных данных, полученных из Убежища ключей Azure, введите имя секрета между двойными кавычками. Например, если имя секрета cred1, введите **"$(cred1)"** для этого значения.

    h. Выберите **инкрементный** для **режима развертывания.**

    > [!WARNING]
    > При **выборе полного** **для режима развертывания**существующие ресурсы могут быть удалены, включая все ресурсы целевой группы ресурсов, которые не определены в шаблоне «Менеджер ресурсов».

    ![Развертывание фабрики данных Prod](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Сохраните конвейер выпуска.

1. Чтобы вызвать релиз, выберите **Создать релиз**.

   ![Выберите Создать выпуск](media/continuous-integration-deployment/continuous-integration-image10.png)

> [!IMPORTANT]
> В сценариях CI/CD тип времени выполнения интеграции (ИК) в различных средах должен быть одинаковым. Например, если у вас есть самостоятельно размещенный ИК в среде разработки, тот же ИК должен быть самостоятельно размещенным в других средах, таких как тест и производство. Аналогичным образом, если вы совместно работаете на нескольких этапах, необходимо настроить запуски интеграции как связанные самохуссованные во всех средах, таких как разработка, тестирование и производство.

### <a name="get-secrets-from-azure-key-vault"></a>Получите секреты из Убежища ключей Azure

Если у вас есть секреты для передачи в шаблоне менеджера ресурсов Azure, мы рекомендуем использовать Azure Key Vault с выпуском Azure Pipelines.

Существует два способа обработки секретов:

1.  Добавьте секреты в файл параметров. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](../azure-resource-manager/templates/key-vault-parameter.md).

    Создайте копию файла параметров, загруженного в ветку публикации. Установите значения параметров, которые вы хотите получить от Key Vault, используя этот формат:

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

    При использовании этого метода секрет автоматически извлекается из хранилища ключей.

    Файл параметров также должен находиться в ветви публикации.

1. Добавьте [задачу «Убежище ключей Azure»](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) перед задачей развертывания диспетчера ресурсов Azure, описанной в предыдущем разделе:

    1.  На вкладке **«Задачи»** создайте новую задачу. Поиск **Azure Key Vault** и добавьте его.

    1.  В задаче «Ключевое хранилище» выберите подписку, в которой создано хранилище ключей. Предоставьте учетные данные, если это необходимо, а затем выберите хранилище ключей.

    ![Добавление задачи Убежища ключей](media/continuous-integration-deployment/continuous-integration-image8.png)

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Предоставление разрешений для агента Azure Pipelines

Задача Azure Key Vault может выполнить ошибку с ошибкой, отданной доступу, если не установлены правильные разрешения. Загрузите журналы для выпуска и найдите файл .ps1, содержащий команду для предоставления разрешений агенту Azure Pipelines. Вы можете запустить команду напрямую. Или вы можете скопировать основной идентификатор из файла и добавить политику доступа вручную на портале Azure. `Get`и `List` являются минимальными разрешениями, необходимыми.

### <a name="update-active-triggers"></a>Обновление активных триггеров

При попытке обновления активных триггеров развертывание может завершиться сбоем. Чтобы обновить активные триггеры, необходимо вручную остановить их, а затем перезапустить их после развертывания. Это можно сделать с помощью задачи Azure PowerShell:

1.  На вкладке **«Задачи»** выпуска добавьте задачу **Azure PowerShell.** Выберите версию задачи 4.». 

1.  Выберите подписку, в ней находится ваша фабрика.

1.  Выберите **путь файла скрипта** в качестве типа скрипта. Это требует сохранения скрипта PowerShell в репозитории. Следующий скрипт PowerShell может быть использован для остановки триггеров:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

Можно выполнить аналогичные `Start-AzDataFactoryV2Trigger` шаги (с функцией) для перезапуска триггеров после развертывания.

### <a name="sample-pre--and-post-deployment-script"></a>Пример сценария до развертывания и после развертывания

Следующий пример скрипта можно использовать для остановки триггеров перед развертыванием и их перезапуска. Скрипт также содержит код для удаления ресурсов. Сохранить скрипт в репозитории Git Azure DevOps и ссылку на него с помощью задачи Azure PowerShell с помощью версии 4.

При запуске сценария предварительного развертывания необходимо указать вариацию следующих параметров в поле **Аргументов скрипта.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $true -deleteDeployment $false`


При запуске скрипа после развертывания необходимо указать вариацию следующих параметров в поле **Аргументов скрипта.**

`-armTemplate "$(System.DefaultWorkingDirectory)/<your-arm-template-location>" -ResourceGroupName <your-resource-group-name> -DataFactoryName <your-data-factory-name>  -predeployment $false -deleteDeployment $true`

![Задача Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Вот скрипт, который может быть использован для предварительного и пост-развертывания. Он учитывает удаленные ресурсы и ссылки на ресурсы.

```powershell
param
(
    [parameter(Mandatory = $false)] [String] $armTemplate,
    [parameter(Mandatory = $false)] [String] $ResourceGroupName,
    [parameter(Mandatory = $false)] [String] $DataFactoryName,
    [parameter(Mandatory = $false)] [Bool] $predeployment=$true,
    [parameter(Mandatory = $false)] [Bool] $deleteDeployment=$false
)

function getPipelineDependencies {
    param([System.Object] $activity)
    if ($activity.Pipeline) {
        return @($activity.Pipeline.ReferenceName)
    } elseif ($activity.Activities) {
        $result = @()
        $activity.Activities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.ifFalseActivities -or $activity.ifTrueActivities) {
        $result = @()
        $activity.ifFalseActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        $activity.ifTrueActivities | Where-Object {$_ -ne $null} | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        return $result
    } elseif ($activity.defaultActivities) {
        $result = @()
        $activity.defaultActivities | ForEach-Object{ $result += getPipelineDependencies -activity $_ }
        if ($activity.cases) {
            $activity.cases | ForEach-Object{ $_.activities } | ForEach-Object{$result += getPipelineDependencies -activity $_ }
        }
        return $result
    } else {
        return @()
    }
}

function pipelineSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]$pipeline,
    [Hashtable] $pipelineNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$pipeline.Name] -eq $true) {
        return;
    }
    $visited[$pipeline.Name] = $true;
    $pipeline.Activities | ForEach-Object{ getPipelineDependencies -activity $_ -pipelineNameResourceDict $pipelineNameResourceDict}  | ForEach-Object{
        pipelineSortUtil -pipeline $pipelineNameResourceDict[$_] -pipelineNameResourceDict $pipelineNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($pipeline)

}

function Get-SortedPipelines {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $pipelines = Get-AzDataFactoryV2Pipeline -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $ppDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $pipelines | ForEach-Object{ $ppDict[$_.Name] = $_ }
    $pipelines | ForEach-Object{ pipelineSortUtil -pipeline $_ -pipelineNameResourceDict $ppDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSPipeline]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function triggerSortUtil {
    param([Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]$trigger,
    [Hashtable] $triggerNameResourceDict,
    [Hashtable] $visited,
    [System.Collections.Stack] $sortedList)
    if ($visited[$trigger.Name] -eq $true) {
        return;
    }
    $visited[$trigger.Name] = $true;
    $trigger.Properties.DependsOn | Where-Object {$_ -and $_.ReferenceTrigger} | ForEach-Object{
        triggerSortUtil -trigger $triggerNameResourceDict[$_.ReferenceTrigger.ReferenceName] -triggerNameResourceDict $triggerNameResourceDict -visited $visited -sortedList $sortedList
    }
    $sortedList.Push($trigger)
}

function Get-SortedTriggers {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $triggers = Get-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $triggerDict = @{}
    $visited = @{}
    $stack = new-object System.Collections.Stack
    $triggers | ForEach-Object{ $triggerDict[$_.Name] = $_ }
    $triggers | ForEach-Object{ triggerSortUtil -trigger $_ -triggerNameResourceDict $triggerDict -visited $visited -sortedList $stack }
    $sortedList = new-object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSTrigger]
    
    while ($stack.Count -gt 0) {
        $sortedList.Add($stack.Pop())
    }
    $sortedList
}

function Get-SortedLinkedServices {
    param(
        [string] $DataFactoryName,
        [string] $ResourceGroupName
    )
    $linkedServices = Get-AzDataFactoryV2LinkedService -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName
    $LinkedServiceHasDependencies = @('HDInsightLinkedService', 'HDInsightOnDemandLinkedService', 'AzureBatchLinkedService')
    $Akv = 'AzureKeyVaultLinkedService'
    $HighOrderList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $RegularList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]
    $AkvList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]

    $linkedServices | ForEach-Object {
        if ($_.Properties.GetType().Name -in $LinkedServiceHasDependencies) {
            $HighOrderList.Add($_)
        }
        elseif ($_.Properties.GetType().Name -eq $Akv) {
            $AkvList.Add($_)
        }
        else {
            $RegularList.Add($_)
        }
    }

    $SortedList = New-Object Collections.Generic.List[Microsoft.Azure.Commands.DataFactoryV2.Models.PSLinkedService]($HighOrderList.Count + $RegularList.Count + $AkvList.Count)
    $SortedList.AddRange($HighOrderList)
    $SortedList.AddRange($RegularList)
    $SortedList.AddRange($AkvList)
    $SortedList
}

$templateJson = Get-Content $armTemplate | ConvertFrom-Json
$resources = $templateJson.resources

#Triggers 
Write-Host "Getting triggers"
$triggersADF = Get-SortedTriggers -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
        Remove-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Disabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
else {
    #Deleted resources
    #pipelines
    Write-Host "Getting pipelines"
    $pipelinesADF = Get-SortedPipelines -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $pipelinesTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/pipelines" }
    $pipelinesNames = $pipelinesTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40)}
    $deletedpipelines = $pipelinesADF | Where-Object { $pipelinesNames -notcontains $_.Name }
    #dataflows
    $dataflowsADF = Get-AzDataFactoryV2DataFlow -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $dataflowsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/dataflows" }
    $dataflowsNames = $dataflowsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataflow = $dataflowsADF | Where-Object { $dataflowsNames -notcontains $_.Name }
    #datasets
    Write-Host "Getting datasets"
    $datasetsADF = Get-AzDataFactoryV2Dataset -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
    $datasetsTemplate = $resources | Where-Object { $_.type -eq "Microsoft.DataFactory/factories/datasets" }
    $datasetsNames = $datasetsTemplate | ForEach-Object {$_.name.Substring(37, $_.name.Length-40) }
    $deleteddataset = $datasetsADF | Where-Object { $datasetsNames -notcontains $_.Name }
    #linkedservices
    Write-Host "Getting linked services"
    $linkedservicesADF = Get-SortedLinkedServices -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName
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
    Write-Host "Deleting dataflows"
    $deleteddataflow | ForEach-Object { 
        Write-Host "Deleting dataflow " $_.Name
        Remove-AzDataFactoryV2DataFlow -Name $_.Name -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Force 
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

    #Start active triggers - after cleanup efforts
    Write-Host "Starting active triggers"
    $activeTriggerNames | ForEach-Object { 
        Write-host "Enabling trigger " $_
        Add-AzDataFactoryV2TriggerSubscription -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force
    $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    while ($status.Status -ne "Enabled"){
            Start-Sleep -s 15
            $status = Get-AzDataFactoryV2TriggerSubscriptionStatus -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_
    }
    Start-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_ -Force 
    }
}
```

## <a name="use-custom-parameters-with-the-resource-manager-template"></a>Использование пользовательских параметров с шаблоном Resource Manager

Если вы находитесь в режиме GIT, можно переопределить свойства по умолчанию в шаблоне Resource Manager для установки свойств, которые параметры цвенриды в шаблоне, и свойствах, которые жестко кодируются. В следующих сценариях может потребоваться переопределить шаблон параметризации по умолчанию:

* Вы используете автоматизированный CI/CD и хотите изменить некоторые свойства во время развертывания диспетчера ресурсов, но свойства не параметрыруются по умолчанию.
* Ваша фабрика настолько велика, что шаблон диспетчера ресурсов по умолчанию является недействительным, поскольку он имеет больше, чем максимально допустимые параметры (256).

В этих условиях, чтобы переопределить шаблон параметризации по умолчанию, создайте файл под названием **arm-template-parameters-definition.json** в папке, указанной в качестве корневой папки для интеграции git фабрики данных. Вы должны использовать это точное имя файла. Data Factory считывает этот файл из той ветви, на которой вы в настоящее время находитесь на портале Azure Data Factory, а не только из отрасли совместной работы. Вы можете создать или отсвазать файл из частной ветви, где можно протестировать свои изменения, выбрав **шаблон Export ARM template** в uI. Затем можно объединить файл в ветвь совместной работы. Если файл не найден, используется шаблон по умолчанию.

> [!NOTE]
> Пользовательский шаблон параметризации не изменяет параметр параметра ARM 256. Это позволяет выбрать и уменьшить количество параметризированных свойств.

### <a name="syntax-of-a-custom-parameters-file"></a>Синтаксис пользовательского файла параметров

Ниже приведены некоторые рекомендации, чтобы следовать при создании пользовательского файла параметров. Файл состоит из раздела для каждого типа сущности: триггер, конвейер, связанная служба, набор данных, время выполнения интеграции и так далее.
* Введите путь свойства под соответствующим типом сущности.
* Установка имени свойства `*` для узначения того, что необходимо параметризировать все свойства под ним (только до первого уровня, а не рекурсивно). Вы также можете предоставить исключения из этой конфигурации.
* Установка значения свойства как строки указывает на то, что необходимо параметризировать свойство. Используйте `<action>:<name>:<stype>`формат .
   *  `<action>` может быть одним из этих символов:
      * `=` означает сохранение текущего значения в качестве значения по умолчанию для параметра.
      * `-` означает не держать значение по умолчанию для параметра.
      * `|` — это особый случай для секретов из Azure Key Vault для строк или ключей соединения.
   * `<name>` — это название параметра. Если он пуст, он берет название свойства. Если значение начинается `-` с символа, имя сокращается. Например, `AzureStorage1_properties_typeProperties_connectionString` будет сокращендо `AzureStorage1_connectionString`до .
   * `<stype>` является тип параметра. Если `<stype>` он пуст, тип `string`по умолчанию находится в. Поддерживаемые `string`значения: `bool` `number`, `object`, `securestring`, и .
* Указание массива в файле определения указывает на то, что соответствующее свойство в шаблоне является массивом. Data Factory итерирует все объекты в массиве, используя определение, указанное в объекте времени выполнения интеграции массива. Второй объект, строка, становится именем свойства, которое используется в качестве имени параметра для каждой итерации.
* Определение не может быть специфичным для экземпляра ресурсов. Любое определение применяется ко всем ресурсам такого типа.
* По умолчанию параметры параметрызаются все безопасные строки, такие как секреты Key Vault, а также безопасные строки, такие как строки соединения, клавиши и токены.
 
### <a name="sample-parameterization-template"></a>Шаблон параметризации образца

Вот пример того, как может выглядеть шаблон параметризации:

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
Вот объяснение того, как построен предыдущий шаблон, разбитый по типу ресурса.

#### <a name="pipelines"></a>Конвейеры
    
* Любое свойство `activities/typeProperties/waitTimeInSeconds` в пути параметры. Любое действие в конвейере с именем уровня кода (например, `waitTimeInSeconds` `Wait` действие) параметрырируется как число с именем по умолчанию. Но в шаблоне «Менеджер ресурсов» значение по умолчанию не будет иметь. Это будет обязательный вход ный вход во время развертывания диспетчера ресурсов.
* Аналогичным образом, `headers` свойство, называемое `Web` (например, в `object` действии) параметрыруется с типом (JObject). Он имеет значение по умолчанию, которое является тем же значением, что и у фабрики исходного кода.

#### <a name="integrationruntimes"></a>ИнтеграцияRuntimes

* Все свойства под `typeProperties` траекторией параметрысируются с соответствующими значениями по умолчанию. Например, есть два `IntegrationRuntimes` свойства под `computeProperties` `ssisProperties`свойствами типа: и . Оба типа свойств создаются с их соответствующими значениями и типами по умолчанию (Объект).

#### <a name="triggers"></a>Триггеры

* Под, `typeProperties`два свойства параметризированы. Первый из `maxConcurrency`них, который указан, чтобы иметь`string`значение по умолчанию и имеет тип. Он имеет имя `<entityName>_properties_typeProperties_maxConcurrency`параметра по умолчанию.
* Свойство `recurrence` также параметрызируется. В соответствии с ним все свойства на этом уровне определяются для параметризации в виде строк с значениями по умолчанию и именами параметров. Исключением является `interval` свойство, которое `number`параметрымируются как тип. Название параметра суффиксируется с `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Аналогичным образом, свойство `freq` является строкой и параметрыруется как строка. Однако `freq` свойство параметрызируется без значения по умолчанию. Название укорочено и суффиксировано. Например, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Связанные службы уникальны. Поскольку связанные службы и наборы данных имеют широкий спектр типов, можно предоставить настройку для конкретных типов. В этом примере для всех `AzureDataLakeStore`связанных служб типа будет применен определенный шаблон. Для всех остальных (через `*`), другой шаблон будет применяться.
* Свойство `connectionString` будет параметром `securestring` как значение. Он не будет иметь значение по умолчанию. Он будет иметь укороченный параметр имя, `connectionString`которое суффиксс с .
* Свойство `secretAccessKey` происходит `AzureKeyVaultSecret` (например, в Amazon S3 связаны службы). Он автоматически параметрыруется как секрет Убежища ключей Azure и извлекается из настроенного хранилища ключей. Можно также параметризировать сам свод ключей.

#### <a name="datasets"></a>Наборы данных

* Хотя настройка для типов доступна для наборов данных, можно \*предоставить конфигурацию без явной конфигурации уровня. В предыдущем примере параметры параметров `typeProperties` являются параметризированными.

### <a name="default-parameterization-template"></a>Шаблон параметризации по умолчанию

Ниже приводится текущий шаблон параметрыизации по умолчанию. Если вам нужно добавить только несколько параметров, редактирование этого шаблона непосредственно может быть хорошей идеей, потому что вы не потеряете существующую структуру параметризации.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
                    "poolName": "=",
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

В следующем примере показано, как добавить одно значение в шаблон параметризации по умолчанию. Мы только хотим добавить существующий интерактивный идентификатор кластера Azure Databricks для службы Databricks, связанной с файлом параметров. Обратите внимание, что этот файл такой же, `existingClusterId` как и предыдущий файл, за исключением добавления под полем свойств `Microsoft.DataFactory/factories/linkedServices`.

```json
{
    "Microsoft.DataFactory/factories/pipelines": {
    },
    "Microsoft.DataFactory/factories/dataflows": {
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
            "poolName": "=",
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

Если вы настроили CI/CD для фабрик данных, вы можете превысить пределы шаблона ресурсов Azure Manager по мере роста вашей фабрики. Например, одним ограничением является максимальное количество ресурсов в шаблоне «Менеджер ресурсов». Для размещения крупных фабрик при создании полного шаблона «Менеджер ресурсов» для фабрики фабрика теперь генерирует связанные шаблоны «Менеджер ресурсов». С помощью этой функции вся загрузка на фабрику разбита на несколько файлов, чтобы вы не были ограничены ограничениями.

Если вы настроили Git, связанные шаблоны генерируются и сохраняются вместе с полными шаблонами управления ресурсами в adf_publish в новой папке, называемой linkedTemplates:

![Папка связанных шаблонов Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Связанные шаблоны менеджера ресурсов обычно состоят из основного шаблона и набора шаблонов детей, которые связаны с мастером. Шаблон родительского шаблона называется ArmTemplate_master.json, а шаблоны «ребенок» — с шаблоном ArmTemplate_0.json, ArmTemplate_1.json и так далее. 

Чтобы использовать связанные шаблоны вместо полного шаблона Resource Manager, обновите задачу CI/CD, чтобы указать на ArmTemplate_master.json вместо ArmTemplateForFactory.json (полный шаблон менеджера ресурсов). Менеджер ресурсов также требует, чтобы вы загружали связанные шаблоны в учетную запись хранения, чтобы Azure мог получить к ним доступ во время развертывания. Для получения дополнительной информации см. [Развертывание связанных шаблонов управления ресурсами с VSTS.](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/)

Не забудьте добавить сценарии фабрики данных в конвейер CI/CD до и после завершения развертывания.

Если у вас нет Git настроен, вы можете получить доступ к связанным шаблонам через **шаблон Export ARM** в списке **шаблонов ARM.**

## <a name="hotfix-production-branch"></a>Производственная ветка Hotfix

Если вы развернете фабрику в производство и поймете, что есть ошибка, которую необходимо исправить сразу, но вы не можете развернуть текущую ветвь совместной работы, возможно, потребуется развернуть hotfix. Этот подход так же известен как проектирование быстрого исправления или «FE».

1.    В Azure DevOps перейдите к выпуску, который был развернут в производстве. Найдите последний развернутый коммит.

2.    Из сообщения о коммите получите идентификатор коммитов ветки совместной работы.

3.    Создайте новую ветку hotfix из этого коммита.

4.    Перейдите на UX-фабрику данных Azure и переключитесь на ветку hotfix.

5.    Используя Фабрику данных Azure UX, исправьте ошибку. Проверьте свои изменения.

6.    После проверки исправления выберите **шаблон Export ARM,** чтобы получить шаблон менеджера ресурсов hotfix.

7.    Вручную проверьте эту сборку в adf_publish ветку.

8.    Если вы настроили конвейер выпуска для автоматического триггера на основе adf_publish регистрации, новый релиз запускается автоматически. В противном случае вручную выстраивайся в очередь.

9.    Развертывание выпуска hotfix на испытательных и производственных заводах. Этот релиз содержит предыдущую рабочую нагрузку плюс исправление, которое вы сделали в шаге 5.

10.    Добавьте изменения из hotfix в ветку разработки, чтобы более поздние релизы не включали ту же ошибку.

## <a name="best-practices-for-cicd"></a>Рекомендации для CI/CD

Если вы используете интеграцию Git с фабрикой данных и имеете конвейер CI/CD, который перемещает ваши изменения от разработки к тестированию, а затем к производству, мы рекомендуем следующие рекомендации:

-   **Интеграция Git**. Вам нужно настроить только фабрику данных разработки с помощью интеграции Git. Изменения в тестировании и производстве развертываются через CI/CD и не нуждаются в интеграции Git.

-   **Сценарий CI/CD фабрики данных**. Перед этапом развертывания менеджера ресурсов в CI/CD необходимо выполнить определенные задачи, такие как остановка и перезапуск триггеров и выполнение очистки. Мы рекомендуем использовать скрипты PowerShell до и после развертывания. Для получения дополнительной [информации](#update-active-triggers)см.

-   **Интеграция runtimes и обмена**. Интеграция runtimes не часто меняются и похожи на всех этапах вашего CI / CD. Таким образом, Data Factory ожидает, что вы будете иметь одно и то же имя и тип времени выполнения интеграции на всех этапах CI/CD. Если вы хотите поделиться периодами выполнения интеграции на всех этапах, рассмотрите возможность использования ternary factory только для того, чтобы содержать общие сроки выполнения интеграции. Эту общую фабрику можно использовать во всех средах в качестве связанного типа выполнения интеграции.

-   **Ключевое Убежище**. При использовании связанных служб, основанных на Azure Key Vault, вы можете воспользоваться ими, сохраняя отдельные хранилища ключей для различных сред. Можно также настроить отдельные уровни разрешений для каждого хранилища ключей. Например, возможно, вы не хотите, чтобы члены вашей команды имели разрешения на секреты производства. Если вы придерживаетесь этого подхода, мы рекомендуем вам сохранять одни и те же секретные имена на всех этапах. Если вы сохраняете одни и те же имена, вам не нужно менять шаблоны «Менеджер ресурсов» в средах CI/CD, потому что единственное, что изменяется, это имя хранилища ключей, которое является одним из параметров шаблона ресурсного менеджера.

## <a name="unsupported-features"></a>Неподдерживаемые функции

- По замыслу Data Factory не разрешает вишневый выбор коммитов или выборочную публикацию ресурсов. Публикации будут включать все изменения, внесенные в фабрику данных.

    - Сущности фабрики данных зависят друг от друга. Например, триггеры зависят от конвейеров, а конвейеры — от наборов данных и других конвейеров. Выборочная публикация подмножества ресурсов может привести к неожиданному поведению и ошибкам.
    - В редких случаях, когда вам нужно селективное издание, рассмотреть вопрос об использовании hotfix. Для получения дополнительной [Hotfix production branch](#hotfix-production-branch)информации см.

-   Вы не можете опубликовать из частных ветвей.

-   В настоящее время вы не можете размещать проекты на Bitbucket.
