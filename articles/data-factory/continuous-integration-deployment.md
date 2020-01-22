---
title: Непрерывная интеграция и доставка в фабрике данных Azure
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
ms.date: 08/14/2019
ms.openlocfilehash: 2e14b1bcc991a009ed9b3267477933706e1ec474
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 01/21/2020
ms.locfileid: "76289957"
---
# <a name="continuous-integration-and-delivery-in-azure-data-factory"></a>Непрерывная интеграция и доставка в фабрике данных Azure

## <a name="overview"></a>Обзор

Непрерывная интеграция — это методика автоматического тестирования каждого изменения, внесенного в базу кода, как можно раньше. Непрерывная поставка соответствует тестированию, которое происходит во время непрерывной интеграции и передает изменения в промежуточную или рабочую систему.

В фабрике данных Azure непрерывная интеграция и доставка (CI/CD) означает перемещение конвейеров фабрики данных из одной среды (разработки, тестирования, рабочей) в другую. Вы можете использовать интеграцию с интерфейсом UX фабрики данных с шаблонами Azure Resource Manager, чтобы выполнить CI/CD.

В интерфейсе фабрики данных можно создать шаблон диспетчер ресурсов из раскрывающегося меню **шаблон ARM** . При выборе параметра **Экспорт шаблона ARM**портал создает шаблон диспетчер ресурсов для фабрики данных и файл конфигурации, включающий все строки подключения и другие параметры. Затем создается один файл конфигурации для каждой среды (разработка, тестирование, Рабочая среда). Главный файл шаблона Resource Manager одинаков для всех сред.

Чтобы получить 9-минутное введение в эту функцию и демонстрацию, просмотрите это видео:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Continuous-integration-and-deployment-using-Azure-Data-Factory/player]

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="cicd-lifecycle"></a>Жизненный цикл CI/CD

Ниже приведен примерный обзор жизненного цикла CI/CD в фабрике данных Azure, настроенной с помощью Azure Repos Git. Дополнительные сведения о настройке репозитория Git см. в статье [Управление версиями в фабрике данных Azure](source-control.md).

1.  Фабрика данных разработки создается и настраивается с помощью Azure Repos Git. Все разработчики должны иметь разрешение на создание ресурсов фабрики данных, таких как конвейеры и DataSets.

1.  По мере внесения изменений в свои ветви их компонентов разработчики выполняют отладку своих конвейеров с самыми последними изменениями. Дополнительные сведения об отладке выполнения конвейера см. в статье [Итеративная разработка и отладка с помощью фабрики данных Azure](iterative-development-debugging.md).

1.  После того как разработчики будут удовлетворены изменениями, они создают запрос на вытягивание из ветви их компонентов в главную или коллективную ветвь для получения изменений, которые будут проверены одноранговыми узлами.

1.  После утверждения запроса на вытягивание и слияния изменений в главной ветви изменения могут быть опубликованы в фабрике разработки.

1.  Когда группа готова к развертыванию изменений в фабрике тестов, а затем в производственной фабрике, Группа экспортирует шаблон диспетчер ресурсов из главной ветви.

1.  Экспортированный шаблон диспетчер ресурсов развертывается с различными файлами параметров в фабрике тестирования и производственной фабрике.

## <a name="create-a-resource-manager-template-for-each-environment"></a>Создание шаблона Resource Manager для каждой среды

1. В списке **шаблон ARM** выберите **Экспорт шаблона ARM** , чтобы экспортировать шаблон диспетчер ресурсов для фабрики данных в среде разработки.

   ![Экспорт шаблона диспетчер ресурсов](media/continuous-integration-deployment/continuous-integration-image1.png)

1. В фабриках тестов и рабочих данных выберите **Импорт шаблона ARM**. После этого вы перейдете на портал Azure, где сможете импортировать экспортированный шаблон. Выберите **создать собственный шаблон в редакторе** , чтобы открыть редактор шаблонов диспетчер ресурсов.

   ![Создание собственного шаблона](media/continuous-integration-deployment/custom-deployment-build-your-own-template.png) 

1. Выберите **загрузить файл**и выберите созданный шаблон диспетчер ресурсов.

   ![Изменить шаблон](media/continuous-integration-deployment/custom-deployment-edit-template.png)

1. В разделе Параметры введите значения конфигурации, например учетные данные связанной службы. Когда все будет готово, выберите **приобрести** , чтобы развернуть шаблон диспетчер ресурсов.

   ![Раздел параметров](media/continuous-integration-deployment/continuous-integration-image5.png)

### <a name="connection-strings"></a>Строки подключения

Сведения о настройке строк подключения см. в статье о соединителе. Дополнительные сведения о базе данных SQL Azure см. в статье [Копирование данных в базу данных Azure SQL и из нее с помощью фабрики данных Azure](connector-azure-sql-database.md). Чтобы проверить строку подключения, можно открыть представление кода для ресурса в интерфейсе фабрики данных. В представлении кода часть пароля или ключа учетной записи в строке подключения удаляется. Чтобы открыть представление кода, выберите значок, выделенный здесь:

![Открыть представление кода для просмотра строки подключения](media/continuous-integration-deployment/continuous-integration-codeview.png)

## <a name="automate-continuous-integration-by-using-azure-pipelines-releases"></a>Автоматизация непрерывной интеграции с помощью Azure Pipelines выпусков

Ниже приведено пошаговое инструкции по настройке выпуска Azure Pipelines, который автоматизирует развертывание фабрики данных в нескольких средах.

![Схема непрерывной интеграции с помощью Azure Pipelines](media/continuous-integration-deployment/continuous-integration-image12.png)

### <a name="requirements"></a>Требования

-   Подписка Azure, связанная с Team Foundation Server или Azure Repos Visual Studio, использующая [конечную точку службы Azure Resource Manager](https://docs.microsoft.com/azure/devops/pipelines/library/service-endpoints#sep-azure-resource-manager).

-   Фабрика данных, настроенная с интеграцией Azure Repos Git.

-    [Хранилище ключей Azure](https://azure.microsoft.com/services/key-vault/) , содержащее секреты для каждой среды.

### <a name="set-up-an-azure-pipelines-release"></a>Настройка выпуска Azure Pipelines

1.  В [Azure DevOps](https://dev.azure.com/)откройте проект, настроенный для фабрики данных.

1.  В левой части страницы выберите **конвейеры**, а затем выберите **выпуски**.

    ![Выбор конвейеров, выпусков](media/continuous-integration-deployment/continuous-integration-image6.png)

1.  Выберите **Новый конвейер**или, если имеются существующие конвейеры, выберите **создать** , а затем — **Новый конвейер выпуска**.

1.  Выберите **пустой шаблон задания** .

    ![Выберите пустое задание](media/continuous-integration-deployment/continuous-integration-image13.png)

1.  В поле **имя этапа** введите имя среды.

1.  Выберите **Добавить артефакт**, а затем выберите репозиторий, настроенный для вашей фабрики данных. Выберите **adf_publish** для **ветви по умолчанию**. В качестве **версии по умолчанию**выберите **Последняя из ветви по умолчанию**.

    ![Добавление артефакта](media/continuous-integration-deployment/continuous-integration-image7.png)

1.  Добавьте задачу развертывания Azure Resource Manager.

    а.  В представлении этап выберите **просмотреть задачи этапа**.

    ![Представление "этап"](media/continuous-integration-deployment/continuous-integration-image14.png)

    b.  Создайте новое задание. Найдите **Развертывание группы ресурсов Azure**и нажмите кнопку **Добавить**.

    c.  В задаче развертывание выберите подписку, группу ресурсов и расположение для целевой фабрики данных. При необходимости укажите учетные данные.

    d.  В списке **действие** выберите **создать или обновить группу ресурсов**.

    д)  Нажмите кнопку с многоточием ( **...** ) рядом с полем **шаблон** . Найдите шаблон Azure Resource Manager, созданный с помощью **шаблона Импорт ARM** , в разделе [Создание диспетчер ресурсов шаблона для каждого окружения](continuous-integration-deployment.md#create-a-resource-manager-template-for-each-environment) этой статьи. Найдите этот файл в папке <FactoryName> ветви adf_publish.

    е)  Выберите **…** рядом с полем **Параметры шаблона** , чтобы выбрать файл параметров. Выбранный файл будет зависеть от того, была создана копия или используется файл по умолчанию Армтемплатепараметерсфорфактори. JSON.

    ж.  Выберите **…** рядом с полем **переопределить параметры шаблона** и введите сведения о целевой фабрике данных. Для учетных данных, полученных из Azure Key Vault, введите имя секрета в двойные кавычки. Например, если имя секрета — cred1, введите **"$ (cred1)"** для этого значения.

    h. Выберите **добавочный** для **режима развертывания**.

    > [!WARNING]
    > Если выбрать параметр **завершить** для **режима развертывания**, существующие ресурсы могут быть удалены, включая все ресурсы в Целевой группе ресурсов, которые не определены в шаблоне диспетчер ресурсов.

    ![Производственное развертывание фабрики данных](media/continuous-integration-deployment/continuous-integration-image9.png)

1.  Сохраните конвейер выпуска.

1. Чтобы запустить выпуск, выберите **создать выпуск**.

   ![Выбор создания выпуска](media/continuous-integration-deployment/continuous-integration-image10.png)

### <a name="get-secrets-from-azure-key-vault"></a>Получение секретов от Azure Key Vault

Если у вас есть секреты для передачи шаблона Azure Resource Manager, рекомендуется использовать Azure Key Vault с Azure Pipelinesным выпуском.

Существует два способа управления секретами:

1.  Добавьте секреты в файл параметров. Дополнительные сведения см. в статье [Использование Azure Key Vault для передачи защищенного значения параметра во время развертывания](../azure-resource-manager/templates/key-vault-parameter.md).

    Создайте копию файла параметров, который отправляется в ветвь Publish. Задайте значения параметров, которые требуется получить из Key Vault, в следующем формате:

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

-  Добавьте [задачу Azure Key Vault](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-key-vault) перед задачей «Azure Resource Manager развертывание», описанной в предыдущем разделе.

    1.  На вкладке **задачи** создайте новую задачу. Выполните поиск **Azure Key Vault** и добавьте его.

    1.  В задаче Key Vault выберите подписку, в которой было создано хранилище ключей. При необходимости введите учетные данные, а затем выберите хранилище ключей.

    ![Добавление задачи Key Vault](media/continuous-integration-deployment/continuous-integration-image8.png)

   #### <a name="grant-permissions-to-the-azure-pipelines-agent"></a>Предоставление разрешений для агента Azure Pipelines

   Azure Key Vaultная задача может завершиться ошибкой с ошибкой отказа в доступе, если не заданы правильные разрешения. Скачайте журналы для выпуска и выберите файл PS1, содержащий команду, чтобы предоставить разрешения агенту Azure Pipelines. Вы можете выполнить команду напрямую. Также можно скопировать идентификатор субъекта из файла и добавить политику доступа вручную в портал Azure. минимальные требуемые разрешения — `Get` и `List`.

### <a name="update-active-triggers"></a>Обновление активных триггеров

При попытке обновления активных триггеров развертывание может завершиться сбоем. Чтобы обновить активные триггеры, необходимо вручную закрыть их, а затем перезапустить после развертывания. Это можно сделать с помощью задачи Azure PowerShell:

1.  На вкладке **задачи** выпуска добавьте **Azure PowerShell** задачу.

1.  Выберите **Azure Resource Manager** в качестве типа подключения, а затем выберите свою подписку.

1.  Выберите **встроенный скрипт** в качестве типа скрипта, а затем укажите код. Следующий код останавливает триггеры:

    ```powershell
    $triggersADF = Get-AzDataFactoryV2Trigger -DataFactoryName $DataFactoryName -ResourceGroupName $ResourceGroupName

    $triggersADF | ForEach-Object { Stop-AzDataFactoryV2Trigger -ResourceGroupName $ResourceGroupName -DataFactoryName $DataFactoryName -Name $_.name -Force }
    ```

    ![Задача Azure PowerShell](media/continuous-integration-deployment/continuous-integration-image11.png)

Вы можете выполнить аналогичные действия (с помощью функции `Start-AzDataFactoryV2Trigger`), чтобы перезапустить триггеры после развертывания.

> [!IMPORTANT]
> В сценариях CI/CD тип среды выполнения интеграции (IR) в разных средах должен быть одинаковым. Например, если в среде разработки есть локальная среда IR, то такой же тип IR также должен быть автономным в других средах, таких как тестовая и Рабочая. Аналогично, при совместном использовании сред выполнения интеграции на нескольких этапах необходимо настроить среды выполнения интеграции как связанные автономные размещения во всех средах, таких как разработка, тестирование и производство.

#### <a name="sample-pre--and-post-deployment-script"></a>Пример скрипта, выполняемого до и после развертывания

В следующем примере скрипта показано, как отключить триггеры перед развертыванием и перезапустить их позже. Скрипт также содержит код для удаления ресурсов. Чтобы установить последнюю версию Azure PowerShell, ознакомьтесь со статьей [Установка Azure PowerShell в ОС Windows с помощью PowerShellGet](https://docs.microsoft.com/powershell/azure/install-az-ps).

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

    #Start active triggers - after cleanup efforts
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

В этих условиях для переопределения шаблона параметризации по умолчанию создайте файл с именем ARM-Template-Parameters-Definition. JSON в папке, указанной в качестве корневой папки для интеграции с Git фабрики данных. Необходимо использовать точное имя файла. Фабрика данных считывает этот файл из любой ветви, на которой Вы находитесь на портале фабрики данных Azure, а не только из ветви совместной работы. Вы можете создать или изменить файл из частной ветви, где можно протестировать изменения, выбрав **Экспорт шаблона ARM** в пользовательском интерфейсе. Затем можно выполнить слияние файла в ветвь совместной работы. Если файл не найден, используется шаблон по умолчанию.

### <a name="syntax-of-a-custom-parameters-file"></a>Синтаксис пользовательского файла параметров

Ниже приведены некоторые рекомендации, которые необходимо выполнить при создании файла пользовательских параметров. Файл состоит из раздела для каждого типа сущности: триггера, конвейера, связанной службы, набора данных, среды выполнения интеграции и т. д.
* Введите путь к свойству в соответствующем типе сущности.
* Присвоение имени свойства значения `*` указывает, что необходимо параметризовать все свойства под ним (только на первый уровень, но не рекурсивно). В эту конфигурацию можно также указать исключения.
* Установка значения свойства в виде строки указывает, что необходимо параметризовать свойство. Используйте формат `<action>:<name>:<stype>`.
   *  `<action>` может быть одним из следующих символов:
      * `=` означает, что текущее значение сохраняется как значение по умолчанию для параметра.
      * `-` означает, что значение по умолчанию для параметра не сохраняется.
      * `|` является особым случаем для секретов из Azure Key Vault для строк подключения или ключей.
   * `<name>` — имя параметра. Если оно пустое, оно принимает имя свойства. Если значение начинается с `-` символа, оно сокращается. Например, `AzureStorage1_properties_typeProperties_connectionString` будет сокращена до `AzureStorage1_connectionString`.
   * `<stype>` — это тип параметра. Если `<stype>` пуст, тип по умолчанию — `string`. Поддерживаемые значения: `string`, `bool`, `number`, `object`и `securestring`.
* Указание массива в файле определения указывает на то, что соответствующее свойство в шаблоне является массивом. Фабрика данных выполняет перебор всех объектов в массиве с помощью определения, указанного в объекте среды выполнения интеграции массива. Второй объект, строка, становится именем свойства, которое используется в качестве имени параметра для каждой итерации.
* Определение не может быть специфичным для экземпляра ресурса. Любое определение применяется ко всем ресурсам этого типа.
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
Ниже приведено объяснение того, как создается предыдущий шаблон, разбитый по типу ресурса.

#### <a name="pipelines"></a>Конвейеры
    
* Любое свойство в пути `activities/typeProperties/waitTimeInSeconds` параметризовано. Любое действие в конвейере, которое имеет свойство уровня кода с именем `waitTimeInSeconds` (например, действие `Wait`), параметризовано как число с именем по умолчанию. Но в шаблоне диспетчер ресурсов не будет значения по умолчанию. Он будет обязательным входом во время развертывания диспетчер ресурсов.
* Аналогично, свойство с именем `headers` (например, в `Web` действии) параметризовано с типом `object` (JObject). Он имеет значение по умолчанию, то есть то же значение, что и у исходной фабрики.

#### <a name="integrationruntimes"></a>интегратионрунтимес

* Все свойства в пути `typeProperties` параметризованы с соответствующими значениями по умолчанию. Например, в свойствах типа `IntegrationRuntimes` есть два свойства: `computeProperties` и `ssisProperties`. Оба типа свойств создаются с соответствующими значениями по умолчанию и типами (Object).

#### <a name="triggers"></a>Триггеры

* В разделе `typeProperties`два свойства являются параметризованными. Первый из них `maxConcurrency`, для которого задано значение по умолчанию и он имеет тип`string`. Имя параметра по умолчанию `<entityName>_properties_typeProperties_maxConcurrency`.
* Свойство `recurrence` также является параметризованным. В нем все свойства на этом уровне указываются для параметризации в виде строк, значения по умолчанию и имена параметров. Исключением является свойство `interval`, параметризованное как тип `number`. Имя параметра добавляется в суффикс с `<entityName>_properties_typeProperties_recurrence_triggerSuffix`. Аналогичным образом свойство `freq` — это строка, которая параметризована как строка. Однако свойство `freq` параметризовано без значения по умолчанию. Имя сокращено и суффиксы. Например, `<entityName>_freq`.

#### <a name="linkedservices"></a>LinkedServices

* Связанные службы являются уникальными. Так как связанные службы и наборы данных имеют широкий спектр типов, можно указать настройку для конкретного типа. В этом примере для всех связанных служб типа `AzureDataLakeStore`будет применен конкретный шаблон. Для всех остальных (через `*`) будет применен другой шаблон.
* Свойство `connectionString` будет параметризовано как значение `securestring`. Он не имеет значения по умолчанию. Имя параметра будет сокращено с суффиксом `connectionString`.
* `secretAccessKey` свойства — это `AzureKeyVaultSecret` (например, в связанной службе Amazon S3). Он автоматически параметризован как Azure Key Vault секрет и получен из настроенного хранилища ключей. Вы также можете параметризовать само хранилище ключей.

#### <a name="datasets"></a>Наборы данных

* Хотя настройка для наборов данных доступна для конкретного типа, можно предоставить конфигурацию без явной настройки уровня \*. В предыдущем примере все свойства набора данных в разделе `typeProperties` являются параметризованными.

### <a name="default-parameterization-template"></a>Шаблон параметризации по умолчанию

Ниже приведен текущий шаблон параметризации по умолчанию. Если необходимо добавить только несколько параметров, то изменение этого шаблона напрямую может оказаться хорошей идеей, так как вы не потеряли существующую структуру параметризации.

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

В следующем примере показано, как добавить одно значение в шаблон параметризации по умолчанию. Мы хотим добавить существующий Azure Databricks идентификатор интерактивного кластера для связанной службы "кирпичы" в файл параметров. Обратите внимание, что этот файл совпадает с предыдущим файлом, за исключением добавления `existingClusterId` в поле свойства `Microsoft.DataFactory/factories/linkedServices`.

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

Если вы настроили CI/CD для фабрик данных, вы можете превысить ограничения Azure Resource Manager шаблонов по мере роста фабрики. Например, одним из ограничений является максимальное количество ресурсов в шаблоне диспетчер ресурсов. Для размещения больших фабрик при формировании полного шаблона диспетчер ресурсов фабрика данных теперь создает связанные шаблоны диспетчер ресурсов. С помощью этой функции все полезные данные фабрики разбиваются на несколько файлов, чтобы не ограничиваться ограничениями.

Если вы настроили Git, связанные шаблоны создаются и сохраняются вместе с полными диспетчер ресурсов шаблонами в ветви adf_publish в новой папке с именем Линкедтемплатес:

![Папка связанных шаблонов Resource Manager](media/continuous-integration-deployment/linked-resource-manager-templates.png)

Связанные шаблоны диспетчер ресурсов обычно состоят из главного шаблона и набора дочерних шаблонов, связанных с главным. Родительский шаблон называется ArmTemplate_master. JSON, а дочерние шаблоны именуются шаблоном ArmTemplate_0. JSON, ArmTemplate_1. JSON и т. д. 

Чтобы использовать связанные шаблоны вместо полного шаблона диспетчер ресурсов, обновите задачу CI/CD, чтобы она указывала на ArmTemplate_master. JSON вместо Армтемплатефорфактори. JSON (полный шаблон диспетчер ресурсов). Для диспетчер ресурсов также требуется передать связанные шаблоны в учетную запись хранения, чтобы Azure мог получить к ним доступ во время развертывания. Дополнительные сведения см. в разделе [Развертывание связанных шаблонов диспетчер ресурсов с помощью VSTS](https://blogs.msdn.microsoft.com/najib/2018/04/22/deploying-linked-arm-templates-with-vsts/).

Не забудьте добавить сценарии фабрики данных в конвейер CI/CD до и после завершения развертывания.

Если вы не настроили Git, вы можете получить доступ к связанным шаблонам с помощью **шаблона Export ARM** в списке **шаблонов ARM** .

## <a name="hotfix-production-branch"></a>Рабочая ветвь исправления

Если вы развертываете фабрику в рабочей среде и понимаете, что есть ошибка, которую необходимо исправить немедленно, но вы не можете развернуть текущую ветвь совместной работы, может потребоваться развернуть исправление. Этот подход известен как быстрое устранение неполадок или исправление QFE.

1.  В Azure DevOps перейдите к выпуску, развернутому в рабочей среде. Поиск последней развернутой фиксации.

2.  В сообщении о фиксации получите идентификатор фиксации ветви совместной работы.

3.  Создайте новую ветвь исправления из этой фиксации.

4.  Перейдите в службу "Фабрика данных Azure" и переключитесь на ветвь исправлений.

5.  Исправьте ошибку с помощью UX фабрики данных Azure. Проверьте изменения.

6.  После проверки исправления выберите **Экспорт шаблона ARM** , чтобы получить диспетчер ресурсов шаблона исправления.

7.  Вручную проверить эту сборку в ветви adf_publish.

8.  Если конвейер выпуска настроен на автоматический запуск на основе adf_publish возврата, новый выпуск будет запущен автоматически. В противном случае выпуск выставится в очередь вручную.

9.  Разверните выпуск исправления на фабриках тестирования и производства. Этот выпуск содержит предыдущие рабочие нагрузки, а также исправление, выполненное на шаге 5.

10. Добавьте изменения из исправления в ветвь Development, чтобы в последующих выпусках не включалась та же ошибка.

## <a name="best-practices-for-cicd"></a>Рекомендации для CI/CD

Если вы используете интеграцию с Git с фабрикой данных и конвейер CI/CD, который перемещает изменения из разработки в тест, а затем в рабочую среду, мы рекомендуем следующие рекомендации:

-   **Интеграция с Git**. Необходимо настроить только фабрику данных разработки с помощью интеграции с Git. Изменения в тестовых и рабочих средах развертываются с помощью CI/CD и не требуют интеграции с Git.

-   **Сценарий CI/CD фабрики данных**. Перед шагом диспетчер ресурсов развертывания в CI/CD необходимо выполнить определенные задачи, такие как остановка и перезапуск триггеров и выполнение очистки. Рекомендуется использовать скрипты PowerShell до и после развертывания. Дополнительные сведения см. в разделе [Обновление активных триггеров](#update-active-triggers).

-   **Среды выполнения интеграции и совместное использование**. Среды выполнения интеграции не меняются часто и похожи на все этапы в CI/CD. Поэтому фабрика данных предполагает наличие одинакового имени и типа среды выполнения интеграции во всех стадиях НЕПРЕРЫВного развертывания и чтения. Если вы хотите совместно использовать среды выполнения интеграции на всех этапах, рассмотрите возможность использования фабрики ternary только для хранения общих сред выполнения интеграции. Эту общую фабрику можно использовать во всех средах в качестве связанного типа среды выполнения интеграции.

-   **Key Vault**. При использовании связанных служб, основанных на Azure Key Vault, их можно использовать еще больше за счет хранения отдельных хранилищ ключей для разных сред. Кроме того, можно настроить отдельные уровни разрешений для каждого хранилища ключей. Например, вы не хотите, чтобы члены команды имели разрешения для рабочих секретов. При использовании этого подхода рекомендуется иметь одинаковые имена секретов на всех этапах. Если вы сохраняете одни и те же имена, вам не нужно изменять шаблоны диспетчер ресурсов в средах CI/CD, так как единственное, что изменяется, — это имя хранилища ключей, которое является одним из параметров шаблона диспетчер ресурсов.

## <a name="unsupported-features"></a>Неподдерживаемые функции

- Фабрика данных не допускает отбора фиксаций или выборочной публикации ресурсов. Публикации будут включать все изменения, внесенные в фабрику данных.

    - Сущности фабрики данных зависят друг от друга. Например, триггеры зависят от конвейеров, а конвейеры зависят от наборов данных и других конвейеров. Выборочная публикация подмножества ресурсов может привести к непредвиденным поведению и ошибкам.
    - В редких случаях, когда требуется Выборочная публикация, рассмотрите возможность использования исправления. Дополнительные сведения см. в [статье исправление рабочей ветви](#hotfix-production-branch).

-   Вы не можете опубликовать из частных ветвей.

-   В настоящее время вы не можете размещать проекты в BitBucket.
