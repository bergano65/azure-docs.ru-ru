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
author: djpmsft
ms.author: daperlov
ms.reviewer: maghan
manager: craigg
ms.openlocfilehash: c090d9a864bfb5218836627a5579cd3089387af8
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/14/2019
ms.locfileid: "69013898"
---
# <a name="continuous-integration-and-delivery-cicd-in-azure-data-factory"></a>Непрерывные интеграция и поставка в Фабрике данных Azure

## <a name="overview"></a>Обзор

Непрерывная интеграция — это способ автоматического тестирования каждого изменения, внесенного в базу кода. Непрерывная поставка следует за проверкой, которая выполняется во время непрерывной интеграции. Она передает изменения в промежуточную или рабочую систему.

В фабрике данных Azure непрерывная интеграция & доставки означает перемещение конвейеров фабрики данных из одной среды (разработки, тестирования, рабочей) в другую. Чтобы выполнить непрерывную интеграцию & доставки, можно использовать интеграцию с интерфейсом UX фабрики данных с шаблонами Azure Resource Manager. РАЗРАБОТЧИК фабрики данных может создать шаблон диспетчер ресурсов из раскрывающегося списка **шаблонов ARM** . При выборе варианта **Export ARM template** (Экспорт шаблона ARM) портал создает шаблон Resource Manager для фабрики данных и файл конфигурации, включающий все строки подключения, а также другие параметры. Затем необходимо создать один файл конфигурации для каждой среды (разработка, тестирование, Рабочая среда). Главный файл шаблона Resource Manager одинаков для всех сред.

Уделите 9 минут вашего времени, чтобы просмотреть следующее видео с кратким обзором и демонстрацией этой функции:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="continuous-integration-lifecycle"></a>Жизненный цикл непрерывной интеграции

Ниже приведен пример обзора жизненного цикла непрерывной интеграции и доставки в фабрике данных Azure, настроенной с помощью Azure Repos Git. Дополнительные сведения о настройке репозитория Git см. в статье [Управление версиями в фабрике данных Azure](source-control.md).

1.  Фабрика данных разработки создается и настраивается с помощью Azure Repos Git, где все разработчики имеют разрешение на создание ресурсов фабрики данных, таких как конвейеры и DataSets.

1.  Когда разработчики делают изменения в своей ветви компонентов, они выполняют отладку своих конвейеров с самыми последними изменениями. Дополнительные сведения об отладке выполнения конвейера см. в статье [Итеративная разработка и отладка с помощью фабрики данных Azure](iterative-development-debugging.md).

1.  После того, как разработчики удовлетворяют своим изменениям, они создают запрос на вытягивание из ветви их компонентов в главную или коллективную ветвь для получения изменений, которые будут проверяться одноранговыми узлами.

1.  После утверждения запроса на вытягивание и слияния изменений в главной ветви они могут публиковаться в фабрике разработки.

1.  Когда команда готова к развертыванию изменений в фабрике тестов, а затем к производственной фабрике, они экспортируют шаблон диспетчер ресурсов из главной ветви.

1.  Экспортированный шаблон диспетчер ресурсов развертывается с различными файлами параметров в фабрике тестирования и производственной фабрике.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Создание шаблона Resource Manager для каждой среды

В раскрывающемся списке **шаблон ARM** выберите **Экспорт шаблона ARM** , чтобы экспортировать шаблон диспетчер ресурсов для фабрики данных в среде разработки.

![](media/continuous-integration-deployment/continuous-integration-image1.png)

В фабриках тестов и рабочих данных выберите **Импорт шаблона ARM**. После этого вы перейдете на портал Azure, где сможете импортировать экспортированный шаблон. Выберите **создать собственный шаблон в редакторе** , чтобы открыть редактор шаблонов диспетчер ресурсов.

![](media/continuous-integration-deployment/continuous-integration-image3.png) 

Щелкните **загрузить файл** и выберите созданный шаблон диспетчер ресурсов.

![](media/continuous-integration-deployment/continuous-integration-image4.png)

В области Параметры введите значения конфигурации, такие как учетные данные связанной службы. Когда все будет готово, щелкните **купить** , чтобы развернуть шаблон диспетчер ресурсов.

![](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Строки подключения

Сведения о настройке строк подключения можно найти в статье, посвященной каждому соединителю. Дополнительные сведения о базе данных SQL Azure см. в статье [Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure](connector-azure-sql-database.md). Чтобы проверить строку подключения, можно открыть представление кода для ресурса в интерфейсе фабрики данных. В представлении кода часть пароля или ключа учетной записи в строке подключения удаляется. Чтобы открыть представление кода, щелкните значок, который выделен на следующем снимке экрана.

![Откройте представление кода, чтобы просмотреть строку подключения](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-with-azure-pipelines-releases"></a>Автоматизация непрерывной интеграции с помощью выпусков Azure Pipelines

Ниже приведено пошаговое инструкции по настройке выпуска Azure Pipelines, который автоматизирует развертывание фабрики данных в нескольких средах.

![Схема непрерывной интеграции с помощью Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Требования

-   Подписка Azure, связанная с Team Foundation Server или Azure Repos с помощью [конечной точки службы Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-rm).

-   Фабрика данных, настроенная с интеграцией Azure Repos Git.

-    [Azure Key Vault](https://azure.microsoft.com/services/key-vault/) , содержащий секреты для каждой среды.

### <a name="set-up-an-azure-pipelines-release"></a>Настройка выпуска Azure Pipelines

1.  В [пользовательском интерфейсе Azure DevOps](https://dev.azure.com/)откройте проект, настроенный с помощью фабрики данных.

1.  В левой части страницы щелкните конвейеры, а затем выберите выпуски.

    ![](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Выберите **Новый конвейер** или если у вас есть конвейеры, **новые**и новые конвейеры **выпуска**.

1.  Выберите **пустой шаблон задания** .

    ![](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  В поле **имя этапа** введите имя среды.

1.  Выберите **Добавить артефакт**и выберите тот же репозиторий, настроенный для фабрики данных. Выберите `adf_publish` в качестве стандартной ветви с последней версией по умолчанию.

    ![](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Добавьте задачу развертывания Azure Resource Manager.

    1\.  В представлении "этап" щелкните ссылку **просмотреть задачи этапа** .

    ![](media/continuous-integration-deployment/continuous-integration-image14.png)

    2\.  Создайте новую задачу. Найдите **Развертывание группы ресурсов Azure**и нажмите кнопку **Добавить**.

    В.  В задаче "Развертывание" выберите подписку, группу ресурсов и расположение для целевой фабрики данных и при необходимости предоставьте учетные данные.

    Г.  В раскрывающемся списке Действие выберите **создать или обновить группу ресурсов**.

    Д.  Выберите **…** в поле **Шаблон**. Перейдите к шаблону Azure Resource Manager создать с помощью шага **Импорт шаблона ARM** в статье [Создание шаблона Resource Manager для каждой среды](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment). Найдите этот файл в папке `<FactoryName>` в ветви `adf_publish`.

    f.  Выберите **…** в **поле Параметры шаблона.** для выбора файла параметров. Выберите правильный файл. Выбор зависит от того, была ли создана копия или используется файл по умолчанию *ARMTemplateParametersForFactory.json*.

    ж.  Выберите **…** рядом с полем **Переопределить параметры шаблона** и заполните сведения для целевой фабрики данных. Для учетных данных, поступающих из хранилища ключей, введите имя секрета в двойные кавычки. Например, если имя секрета — `cred1`, введите `"$(cred1)"`для его значения.

    ![](media/continuous-integration-deployment/continuous-integration-image9.png)

    h. Выберите **добавочный** режим развертывания.

    > [!WARNING]
    > При выборе режима **завершить** развертывание существующие ресурсы могут быть удалены, включая все ресурсы в Целевой группе ресурсов, которые не определены в шаблоне диспетчер ресурсов.

1.  Сохраните конвейер выпуска.

1. Чтобы запустить выпуск, нажмите кнопку " **создать выпуск** "

![](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Получение секретов от Azure Key Vault

Если у вас есть секреты для передачи шаблона Azure Resource Manager, рекомендуется использовать Azure Key Vault с Azure Pipelinesным выпуском.

Существует два способа управления секретами:

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

#### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Предоставление разрешений для агента Azure Pipelines

Задача Azure Key Vault может завершиться с ошибкой отказа в доступе, если отсутствуют соответствующие разрешения. Загрузите журналы для выпуска и найдите файл `.ps1` с помощью команды для предоставления разрешений агенту Azure Pipelines. Вы можете выполнить команду напрямую или же скопировать идентификатор участника из файла и вручную добавить политику доступа на портале Azure. Для **получения** и **перечисления** требуются минимальные разрешения.

### <a name="update-active-triggers"></a>Обновление активных триггеров

При попытке обновления активных триггеров развертывание может завершиться сбоем. Чтобы обновить активные триггеры, необходимо вручную остановить и запустить их после развертывания. Это можно сделать с помощью задачи Azure PowerShell.

1.  На вкладке задачи в выпуске добавьте задачу **Azure PowerShell** .

1.  Выберите **Azure Resource Manager** в качестве типа подключения, а затем — свою подписку.

1.  Выберите **Встроенный скрипт** в качестве типа скрипта, а затем укажите код. Следующий пример останавливает триггеры.

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![](media/continuous-integration-deployment/continuous-integration-image11.png)

Вы можете выполнить аналогичные действия (с `Start-AzDataFactoryV2Trigger` функцией), чтобы перезапустить триггеры после развертывания.

> [!IMPORTANT]
> В сценариях непрерывных интеграции и развертывания тип среды выполнения интеграции в разных средах должен совпадать. Например, если у вас есть *локальная* среда выполнения интеграции в среде разработки, ей необходим тип *Локальная* в других средах, таких как рабочая и тестовая. Аналогично, если вы совместно используете среды выполнения интеграции в нескольких средах, необходимо настроить их в качестве *связанных локальных сред* во всех средах (разработки, тестирования и рабочей).

#### <a name="sample-prepostdeployment-script"></a>Пример скрипта, выполняемого перед развертыванием

Ниже приведен пример скрипта для завершения триггеров перед развертыванием и перезапуска триггеров. Скрипт также содержит код для удаления ресурсов. Чтобы установить последнюю версию Azure PowerShell, ознакомьтесь со статьей [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

Если вы находитесь в режиме GIT, можно переопределить свойства по умолчанию в шаблоне диспетчер ресурсов, чтобы задать свойства, параметризованные в шаблоне, и свойства, которые жестко запрограммированы. Может потребоваться переопределить шаблон параметризации по умолчанию в следующих сценариях:

* Вы используете автоматизированную CI/CD и хотите изменить некоторые свойства во время развертывания диспетчер ресурсов, но свойства не будут параметризованы по умолчанию.
* Ваша фабрика настолько велика, что шаблон диспетчер ресурсов по умолчанию является недопустимым, так как он содержит больше максимально допустимых параметров (256).

В этих условиях для переопределения шаблона параметризации по умолчанию создайте файл с именем *ARM-Template-Parameters-Definition. JSON* в корневой папке репозитория. Имя файла должно точно совпадать. Фабрика данных пытается прочитать этот файл из любой ветви, на которой Вы находитесь на портале фабрики данных Azure, а не только из ветви совместной работы. Вы можете создать или изменить файл из частной ветви, где можно протестировать изменения с помощью **шаблона Export ARM** в пользовательском интерфейсе. Затем можно выполнить слияние файла в ветвь совместной работы. Если файл не найден, используется шаблон по умолчанию.


### <a name="syntax-of-a-custom-parameters-file"></a>Синтаксис пользовательского файла параметров

Ниже приведены некоторые рекомендации, которые следует использовать при создании файла пользовательских параметров. Файл состоит из раздела для каждого типа сущности: триггера, конвейера, связанной службы, набора данных, среды выполнения интеграции и т. д.
* Введите путь к свойству в соответствующем типе сущности.
* Если задать для\*свойства имя "", вы указываете, что необходимо параметризовать все свойства под ним (только на первый уровень, но не рекурсивно). Кроме того, можно предоставить любые исключения.
* При задании значения свойства в виде строки вы указываете, что хотите параметризовать свойство. Используйте формат `<action>:<name>:<stype>`.
   *  `<action>` может иметь один из следующих символов:
      * `=` означает, что текущее значение сохраняется как значение по умолчанию для параметра.
      * `-` означает, что значение по умолчанию для параметра не сохраняется.
      * `|` является особым случаем для секретов из Azure Key Vault для строк подключения или ключей.
   * `<name>` имя параметра. Если оно пустое, оно принимает имя свойства. Если значение начинается с `-` символа, оно сокращается. Например, `AzureStorage1_properties_typeProperties_connectionString` сократится до `AzureStorage1_connectionString`.
   * `<stype>` тип параметра. Если `<stype>`параметр пуст ,используетсятип`string`по умолчанию. Поддерживаемые значения: `string`, `bool`, `number`, `object`и .`securestring`
* При указании массива в файле определения необходимо указать, что соответствующее свойство в шаблоне является массивом. Фабрика данных выполняет перебор всех объектов в массиве с помощью определения, указанного в объекте Integration Runtime массива. Второй объект, строка, становится именем свойства, которое используется в качестве имени параметра для каждой итерации.
* Невозможно определить конкретное определение для экземпляра ресурса. Любое определение применяется ко всем ресурсам этого типа.
* По умолчанию все защищенные строки, такие как Key Vault секреты и защищенные строки, такие как строки подключения, ключи и токены, являются параметризованными.
 
### <a name="sample-parameterization-template"></a>Пример шаблона параметризации

Ниже приведен пример того, как может выглядеть шаблон параметризации:

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
Ниже приведено объяснение того, как создается указанный шаблон, разбитый по типу ресурса.

#### <a name="pipelines"></a>Конвейеры
    
* Любое свойство в действиях Path/typeProperties/Ваиттимеинсекондс является параметризованным. Любое действие в конвейере, которое имеет свойство уровня кода с именем `waitTimeInSeconds` (например `Wait` , действие), параметризовано как число с именем по умолчанию. Но в шаблоне диспетчер ресурсов не будет значения по умолчанию. Он будет обязательным входом во время развертывания диспетчер ресурсов.
* Аналогично, свойство с `headers` именем (например, `Web` в действии) параметризовано с типом `object` (JObject). Он имеет значение по умолчанию, которое совпадает со значением в исходной фабрике.

#### <a name="integrationruntimes"></a>интегратионрунтимес

* Все свойства в пути `typeProperties` параметризованы с соответствующими значениями по умолчанию. Например, в разделе Свойства типа **интегратионрунтимес** есть два свойства: `computeProperties` и. `ssisProperties` Оба типа свойств создаются с соответствующими значениями по умолчанию и типами (Object).

#### <a name="triggers"></a>Триггеры

* В `typeProperties`разделе два свойства являются параметризованными. Первый из них — `maxConcurrency`, для которого задано значение по умолчанию и тип.`string` Он имеет имя `<entityName>_properties_typeProperties_maxConcurrency`параметра по умолчанию.
* `recurrence` Свойство также является параметризованным. В нем все свойства на этом уровне указываются для параметризации в виде строк, значения по умолчанию и имена параметров. Исключением является `interval` свойство, параметризованное как тип числа, с именем параметра с суффиксом `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Аналогично, `freq` свойство является строкой и параметризовано как строка. `freq` Однако свойство является параметризованным без значения по умолчанию. Имя сокращено и суффиксы. Например, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Связанные службы являются уникальными. Так как связанные службы и наборы данных имеют широкий спектр типов, можно указать настройку для конкретного типа. В этом примере будут применены все связанные службы `AzureDataLakeStore`типа, заданный шаблон, а для всех остальных (Via \*) будет применен другой шаблон.
* Свойство будет параметризовано `securestring` как значение, оно не будет иметь значения по умолчанию, и оно получит сокращенное имя параметра с `connectionString`суффиксом. `connectionString`
* Свойство является `secretAccessKey` свойством `AzureKeyVaultSecret` (например, в `AmazonS3` связанной службе). Он автоматически параметризован как Azure Key Vault секрет и получен из настроенного хранилища ключей. Вы также можете параметризовать само хранилище ключей.

#### <a name="datasets"></a>Наборы данных

* Хотя настройка для наборов данных доступна для конкретного типа, конфигурацию можно предоставить без явного указания конфигурации на \*уровне. В приведенном выше примере все свойства набора данных `typeProperties` в разделе являются параметризованными.

### <a name="default-parameterization-template"></a>Шаблон параметризации по умолчанию

Ниже приведен текущий шаблон параметризации по умолчанию. Если необходимо добавить только один или несколько параметров, это может оказаться полезным, так как в этом случае существующая структура параметризации не будет потеряна.

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

Ниже приведен пример добавления одного значения в шаблон параметризации по умолчанию. Мы хотим добавить в файл параметров только существующий идентификатор интерактивного кластера "кирпичные сведения" для связанной службы "кирпичы". Обратите внимание, что приведенный ниже файл совпадает с файлом, `existingClusterId` который указан выше, за исключением `Microsoft.DataFactory/factories/linkedServices`поля, включенного в поле свойства.

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

Если вы настроили непрерывную интеграцию и развертывание (CI/CD) для фабрик данных, вы можете столкнуться с ограничениями Azure Resource Manager шаблонов по мере роста фабрики. Пример ограничения — это максимальное число ресурсов в шаблоне диспетчер ресурсов. Для размещения больших фабрик, а также создания полного шаблона диспетчер ресурсов для фабрики, фабрика данных теперь создает связанные шаблоны диспетчер ресурсов. С помощью этой функции все полезные данные фабрики разбиваются на несколько файлов, чтобы не предельно использовать ограничения.

Если вы настроили Git, связанные шаблоны создаются и сохраняются вместе с полными диспетчер ресурсов шаблонами в `adf_publish` ветви в новой папке с `linkedTemplates`именем.

![Папка связанных шаблонов Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Связанные шаблоны Resource Manager обычно имеют главный шаблон и набор дочерних шаблонов, связанных с главным. Родительский шаблон называется `ArmTemplate_master.json`, а дочерние шаблоны именуются по образцу: `ArmTemplate_0.json`, `ArmTemplate_1.json` и т. д. Чтобы использовать связанные шаблоны вместо полного диспетчер ресурсов шаблона, обновите задачу CI/CD, чтобы она указывала `ArmTemplate_master.json` на `ArmTemplateForFactory.json` вместо (полный шаблон диспетчер ресурсов). Resource Manager также требует отправлять связанные шаблоны в учетную запись хранения, чтобы они были доступны в Azure во время развертывания. Дополнительные сведения см. в разделе [Развертывание связанных шаблонов ARM с помощью VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Не забудьте добавить сценарии фабрики данных в конвейер CI/CD до и после завершения развертывания.

Если у вас не настроен Git, связанные шаблоны доступны с помощью жеста **экспорта шаблона ARM**.

## <a name="hot-fix-production-branch"></a>Рабочая ветвь "горячее исправление"

Если вы развертываете фабрику в рабочей среде и понимаете, что есть ошибка, которую необходимо исправить немедленно, но вы не можете развернуть текущую ветвь совместной работы, может потребоваться развернуть горячее исправление.

1.  В Azure DevOps перейдите к выпуску, развернутому в рабочей среде, и найдите последнюю развернутую фиксацию.

2.  В сообщении о фиксации получите идентификатор фиксации ветви совместной работы.

3.  Создайте новую ветвь с горячим исправлением из этой фиксации.

4.  Перейдите в службу "Фабрика данных Azure" и переключитесь в эту ветвь.

5.  Исправьте ошибку с помощью UX фабрики данных Azure. Проверьте изменения.

6.  После проверки исправления щелкните **Экспорт шаблона ARM** , чтобы получить шаблон "горячее исправление" Диспетчер ресурсов.

7.  Вручную верните эту сборку в ветвь adf_publish.

8.  Если вы настроили конвейер выпуска для автоматического запуска на основе возвратов adf_publish, автоматически начнется новый выпуск. В противном случае выпуск выставится в очередь вручную.

9.  Разверните выпуск с горячим исправлением на фабриках тестирования и производства. Этот выпуск содержит предыдущие рабочие нагрузки, а также исправление, выполненное на шаге 5.

10. Добавьте изменения из ветви "горячее исправление" в "Разработка", чтобы последующие выпуски не запускались в одной и той же ошибке.

## <a name="best-practices-for-cicd"></a>Рекомендации для CI/CD

Если вы используете интеграцию Git с фабрикой данных и у вас есть конвейер CI/CD, перемещающий изменения из рабочей среды в тестовую, а затем в рабочую, рекомендуем следующее:

-   **Интеграция Git**. Настройка фабрики данных разработки необходима только для интеграции с Git. Изменения в тестовых и рабочих средах развертываются с помощью CI/CD и не требуют интеграции с Git.

-   **Сценарий CI/CD фабрики данных**. Перед этапом развертывания диспетчер ресурсов в CI/CD требуются некоторые задачи, такие как остановка и запуск триггеров и очистка. Рекомендуется использовать скрипты PowerShell до и после развертывания. Дополнительные сведения см. в разделе [Обновление активных триггеров](#update-active-triggers). 

-   **Среды выполнения интеграции и общий доступ.** Среды выполнения интеграции не меняются часто и похожи на все этапы в CI/CD. Поэтому Фабрика данных ожидает, что имена и типы сред выполнения интеграции одинаковы на всех этапах CI/CD. Если вы хотите совместно использовать среды выполнения интеграции на всех этапах, рассмотрите возможность использования фабрики ternary только для тех, которые содержат общие среды выполнения интеграции. Эту общую фабрику можно использовать во всех средах в качестве связанного типа среды выполнения интеграции.

-   **Key Vault**. При использовании связанных служб на основе Azure Key Vault можно использовать дополнительные преимущества, так как они позволяют хранить отдельные хранилища ключей для разных сред. Вы также можете настроить отдельные уровни разрешений для каждого из них. Например, вы не хотите, чтобы члены команды имели разрешения для рабочих секретов. При использовании этого подхода рекомендуется иметь одинаковые имена секретов на всех этапах. Если вы сохраняете одни и те же имена, вам не нужно изменять шаблоны диспетчер ресурсов в средах CI/CD, так как единственное, что изменяется, — это имя хранилища ключей, которое является одним из параметров шаблона диспетчер ресурсов.

## <a name="unsupported-features"></a>Неподдерживаемые функции

-   Вы не можете публиковать отдельные ресурсы. Сущности фабрики данных зависят друг от друга, и отслеживание изменяющихся зависимостей может быть трудной задачей и привести к непредвиденному поведению. Например, триггеры зависят от конвейеров, конвейеры зависят от наборов данных и других конвейеров, т. д. Если можно опубликовать только подмножество всего набора изменений, могут возникнуть некоторые непредвиденные ошибки.

-   Вы не можете опубликовать из частных ветвей.

-   Вы не можете размещать проекты на Bitbucket.
