---
title: Миграция в менеджер ресурсов с PowerShell
description: В этой статье используется миграция ресурсов IAaS, поддерживаемых платформой, таких как виртуальные машины (Виртуальные машины), виртуальные сети и учетные записи хранения от классических до менеджера ресурсов Azure, используя команды Azure PowerShell
services: virtual-machines-windows
documentationcenter: ''
author: tanmaygore
manager: vashan
editor: ''
tags: azure-resource-manager
ms.assetid: 2b3dff9b-2e99-4556-acc5-d75ef234af9c
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: 12a77c4c21a26f1ec52bb3ffdc312df56d3c4537
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79249975"
---
# <a name="migrate-iaas-resources-from-classic-to-azure-resource-manager-by-using-powershell"></a>Переносите ресурсы IaaS из классического в менеджер ресурсов Azure с помощью PowerShell

> [!IMPORTANT]
> Сегодня около 90% IaaS VMs используют [azure Resource Manager.](https://azure.microsoft.com/features/resource-manager/) По состоянию на 28 февраля 2020 года классические ВМ были обесточены и будут полностью выведены из эксплуатации 1 марта 2023 года. [Узнайте больше]( https://aka.ms/classicvmretirement) об этом амортизме и [как он влияет на вас.](https://docs.microsoft.com/azure/virtual-machines/classic-vm-deprecation#how-does-this-affect-me)

Ниже последовательно описано, как использовать команды Azure PowerShell для переноса ресурсов IaaS из классической модели развертывания в модель развертывания с помощью Azure Resource Manager.

При желании можно также перенести ресурсы с помощью [Azure CLI.](../linux/migration-classic-resource-manager-cli.md)

* Общие сведения о поддерживаемых сценариях переноса см. в разделе [Поддерживаемый платформой перенос ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md).
* Подробное руководство и инструкции по переносу см. в разделе [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md).
* [Просмотрите наиболее распространенные ошибки миграции.](migration-classic-resource-manager-errors.md)

<br>
Вот диаграмма потока, чтобы определить порядок, в котором шаги должны быть выполнены во время процесса миграции.

![Screenshot that shows the migration steps](media/migration-classic-resource-manager/migration-flow.png)

 

## <a name="step-1-plan-for-migration"></a>Шаг 1. Планирование миграции
Вот несколько рекомендаций, которые мы рекомендуем при оценке того, следует ли переносить ресурсы IaaS из классических в ресурсный менеджер:

* Ознакомьтесь с [поддерживаемыми и неподдерживаемыми компонентами и конфигурациями](migration-classic-resource-manager-overview.md). Если у вас есть виртуальные машины, которые используют неподдерживаемые конфигурации или функции, подождите, пока будет объявлена поддержка конфигурации или функции. Также вы можете удалить такую функцию или вынести ее за пределы конфигурации, чтобы выполнить перенос.
* Если у вас есть текущие автоматизированные сценарии, которые развертывают инфраструктуру и приложения, попробуйте создать аналогичную программу установки для миграции с помощью этих сценариев. Вы можете также настроить примеры среды с помощью портала Azure.

> [!IMPORTANT]
> Шлюзы приложений в настоящее время не поддерживаются для миграции из классического в диспетчер ресурсов. Чтобы перенести виртуальную сеть с помощью шлюза приложения, удалите шлюз перед запуском операции "Подготовка" для перемещения сети. После завершения переноса повторно подключите шлюз в Azure Resource Manager.
>
> Шлюзы Azure ExpressRoute, которые подключаются к схемам ExpressRoute в другой подписке, не могут быть автоматически перенесены. В таких случаях удалите шлюз ExpressRoute, переоденьте виртуальную сеть и воссоздайте шлюз. Для получения дополнительной информации [см. Схемы Migrate ExpressRoute и связанные с ними виртуальные сети от классической модели развертывания диспетчера ресурсов.](../../expressroute/expressroute-migration-classic-resource-manager.md)

## <a name="step-2-install-the-latest-version-of-powershell"></a>Шаг 2: Установите последнюю версию PowerShell
Есть два основных способа установки Azure PowerShell — с помощью [коллекции PowerShell](https://www.powershellgallery.com/profiles/azure-sdk/) и [установщика веб-платформы (WebPI)](https://aka.ms/webpi-azps). Обновления для установщика веб-платформы выпускаются ежемесячно. Обновления для коллекции PowerShell выпускаются на постоянной основе. В этой статье используется Azure PowerShell 2.1.0.

Инструкции по установке см. в статье [Установка и настройка Azure PowerShell](/powershell/azure/overview).

## <a name="step-3-ensure-that-youre-an-administrator-for-the-subscription"></a>Шаг 3: Убедитесь, что вы администратор подписки
Для выполнения этой миграции необходимо добавить в качестве соадминистратора для подписки на [портале Azure.](https://portal.azure.com)

1. Войдите на [портал Azure](https://portal.azure.com).
2. В меню **концентратора** выберите **Подписку**. Если вы не видите этот пункт, щелкните **Все службы**.
3. Найдите соответствующую запись подписки, а затем посмотрите на поле **MY ROLE.** Для коуправляющего значение должно быть _администратором учетной записи._

Если вы не можете добавить коадминистратор, обратитесь к администратору службы или коадминистратору для получения подписки, чтобы получить себе добавил.

## <a name="step-4-set-your-subscription-and-sign-up-for-migration"></a>Шаг 4: Установите подписку и зарегистрируйтесь для миграции
Сначала запустите командную строку PowerShell. Для миграции навеяйте среду как для классических, так и для менеджера ресурсов.

Войдите в учетную запись для модели Resource Manager.

```powershell
    Connect-AzAccount
```

Получите доступные подписки с помощью следующей команды.

```powershell
    Get-AzSubscription | Sort Name | Select Name
```

Задайте подписку Azure для текущего сеанса. В этом примере задается имя подписки по умолчанию **My Azure Subscription**. Замените имя подписки в примере своим собственным значением.

```powershell
    Select-AzSubscription –SubscriptionName "My Azure Subscription"
```

> [!NOTE]
> Регистрация является одноразовым шагом, но вы должны сделать это один раз, прежде чем пытаться миграции. Если вы не зарегистрируетесь, отобразится такое сообщение об ошибке:
>
> *Неправильный запрос: Подписка не зарегистрирована для миграции.*

Выполните регистрацию в поставщике ресурсов миграции с помощью приведенной ниже команды.

```powershell
    Register-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Подождите пять минут, пока регистрация закончится. Проверьте состояние утверждения с помощью следующей команды:

```powershell
    Get-AzResourceProvider -ProviderNamespace Microsoft.ClassicInfrastructureMigrate
```

Убедитесь, что RegistrationState имеет значение `Registered` , прежде чем продолжить.

Прежде чем перейти на классическую модель развертывания, убедитесь, что у вас достаточно виртуальных машинных vCPUs Azure в регионе Azure текущего развертывания или виртуальной сети. Чтобы проверить текущее количество виртуальных ЦП в Azure Resource Manager, используйте приведенную ниже команду PowerShell. Чтобы узнать больше о квотах на виртуальные ЦП, см. соответствующий раздел статьи [Подписка Azure, границы, квоты и ограничения службы](../../azure-resource-manager/management/azure-subscription-service-limits.md#managing-limits).

В этом примере проверяется доступность в регионе **Западная часть США**. Замените регион в примере своим собственным значением.

```powershell
    Get-AzVMUsage -Location "West US"
```

Теперь вопийте на свой счет для классической модели развертывания.

```powershell
    Add-AzureAccount
```

Получите доступные подписки с помощью следующей команды.

```powershell
    Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName
```

Задайте подписку Azure для текущего сеанса. В этом примере задается имя подписки по умолчанию **My Azure Subscription**. Замените имя подписки в примере своим собственным значением.

```powershell
    Select-AzureSubscription –SubscriptionName "My Azure Subscription"
```


## <a name="step-5-run-commands-to-migrate-your-iaas-resources"></a>Шаг 5. Выполнение команд для переноса ресурсов IaaS
* [Мигрировать виртуальные в облачные службы (не в виртуальной сети)](#step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network)
* [Перенос виртуальных машин в виртуальную сеть](#step-51-option-2---migrate-virtual-machines-in-a-virtual-network).
* [Перенос учетной записи хранения](#step-52-migrate-a-storage-account)

> [!NOTE]
> Все операции, описанные здесь, являются идемпотентными. Если вы столкнетесь с какой-либо проблемой, не связанной с неподдерживаемой функцией или ошибкой конфигурации, мы рекомендуем повторить подготовку, прервать или зафиксировать текущую операцию. Платформа попытается повторить это действие.


### <a name="step-51-option-1---migrate-virtual-machines-in-a-cloud-service-not-in-a-virtual-network"></a>Шаг 5.1: Вариант 1 - Перенос виртуальных машин в облачный сервис (не в виртуальной сети)
Получите список облачных служб, используя следующую команду. Затем выберите облачный сервис, который необходимо перенести. Если виртуальные машины в облачной службе размещены в виртуальной сети или им назначены веб-роли или рабочие роли, то команда возвращает сообщение об ошибке.

```powershell
    Get-AzureService | ft Servicename
```

Получите имя развертывания для облачной службы. В этом примере имя службы — **My Service**. Замените имя службы в примере своим собственным значением.

```powershell
    $serviceName = "My Service"
    $deployment = Get-AzureDeployment -ServiceName $serviceName
    $deploymentName = $deployment.DeploymentName
```

Подготовьте к переносу виртуальные машины в облачной службе. Возможно два варианта.

* **Вариант 1: Перенос виртуальных виртуальных виз в созданную платформой виртуальную сеть.**

    Во-первых, проверить, что можно перенести облачный сервис, используя следующие команды:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    $validate.ValidationMessages
    ```

    Приведенная ниже команда позволяет отобразить все предупреждения и ошибки, которые мешают миграции. Если проверка будет успешной, можно перейти к этапу подготовки.

    ```powershell
    Move-AzureService -Prepare -ServiceName $serviceName `
        -DeploymentName $deploymentName -CreateNewVirtualNetwork
    ```
* **Вариант 2: Переход на существующую виртуальную сеть в модели развертывания ресурсного менеджера.**

    Этот пример устанавливает имя группы ресурсов для **myResourceGroup**, виртуальное название сети **myVirtualNetwork**, и название подсети **mySubNet**. Замените имена в примере именами своих ресурсов.

    ```powershell
    $existingVnetRGName = "myResourceGroup"
    $vnetName = "myVirtualNetwork"
    $subnetName = "mySubNet"
    ```

    Во-первых, проверить, что можно перенести виртуальную сеть, используя следующую команду:

    ```powershell
    $validate = Move-AzureService -Validate -ServiceName $serviceName `
        -DeploymentName $deploymentName -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName -VirtualNetworkName $vnetName -SubnetName $subnetName
    $validate.ValidationMessages
    ```

    Приведенная ниже команда позволяет отобразить все предупреждения и ошибки, которые мешают миграции. Если проверка прошла успешно, можно приступить к следующему этапу подготовки:

    ```powershell
        Move-AzureService -Prepare -ServiceName $serviceName -DeploymentName $deploymentName `
        -UseExistingVirtualNetwork -VirtualNetworkResourceGroupName $existingVnetRGName `
        -VirtualNetworkName $vnetName -SubnetName $subnetName
    ```

После успешного завершения операции подготовки для одного из перечисленных выше вариантов запросите состояние миграции виртуальных машин. Убедитесь, что они `Prepared` находятся в состоянии.

В этом примере виртуальной машине присваивается имя **myVM**. Замените имя в примере именем своей виртуальной машины.

```powershell
    $vmName = "myVM"
    $vm = Get-AzureVM -ServiceName $serviceName -Name $vmName
    $vm.VM.MigrationState
```

Проверьте конфигурацию для подготовленных ресурсов с помощью PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуться в старое состояние, используйте следующую команду:

```powershell
    Move-AzureService -Abort -ServiceName $serviceName -DeploymentName $deploymentName
```

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

```powershell
    Move-AzureService -Commit -ServiceName $serviceName -DeploymentName $deploymentName
```

### <a name="step-51-option-2---migrate-virtual-machines-in-a-virtual-network"></a>Шаг 5.1: Вариант 2 - Перенос виртуальных машин в виртуальную сеть

Для миграции виртуальных машин в виртуальной сети переносится сама виртуальная сеть. Виртуальные машины автоматически переносятся вместе с ней. Выберите виртуальную сеть, в которую будете переносить ресурсы.
> [!NOTE]
> [Имитируйте одну виртуальную машину, созданную](migrate-single-classic-to-resource-manager.md) с использованием классической модели развертывания, создав новую виртуальную машину Resource Manager с управляемыми дисками с помощью VHD (OS и данных) файлов виртуальной машины.
<br>

> [!NOTE]
> Виртуальное название сети может отличаться от того, что показано на новом портале. Новый портал Azure отображает `[vnet-name]`имя как, но фактическое `Group [resource-group-name] [vnet-name]`виртуальное название сети имеет тип. Перед тем, как начать миграцию, найди `Get-AzureVnetSite | Select -Property Name` фактическое имя виртуальной сети, используя команду, или просмотрите его на старом портале Azure. 

В этом примере виртуальной сети присваивается имя **myVnet**. Замените имя виртуальной сети в примере своим собственным значением.

```powershell
    $vnetName = "myVnet"
```

> [!NOTE]
> Если в виртуальной сети есть виртуальные машины, веб-роли или рабочие роли с неподдерживаемыми конфигурациями, то отображается сообщение об ошибке проверки.

Во-первых, проверить, что можно перенести виртуальную сеть, используя следующую команду:

```powershell
    Move-AzureVirtualNetwork -Validate -VirtualNetworkName $vnetName
```

Приведенная ниже команда позволяет отобразить все предупреждения и ошибки, которые мешают миграции. Если проверка прошла успешно, можно приступить к следующему этапу подготовки:

```powershell
    Move-AzureVirtualNetwork -Prepare -VirtualNetworkName $vnetName
```

Проверьте конфигурацию для подготовленных виртуальных машин с помощью Azure PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуться в старое состояние, используйте следующую команду:

```powershell
    Move-AzureVirtualNetwork -Abort -VirtualNetworkName $vnetName
```

Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

```powershell
    Move-AzureVirtualNetwork -Commit -VirtualNetworkName $vnetName
```

### <a name="step-52-migrate-a-storage-account"></a>Шаг 5.2: Миграция учетной записи хранилища
После того, как вы заработаете миграцию виртуальных машин, выполните следующие проверки предпосылок перед переносом учетных записей хранилища.

> [!NOTE]
> Если учетная запись хранилища не имеет связанных дисков или данных VM, вы можете перейти непосредственно в раздел "Проверка учетных записей хранения и начало миграции".

* Предпосылки проверки, если вы мигрировали какие-либо VMs или ваш счет хранения дисковых ресурсов:
    * Мигрируйте виртуальные машины, диски которых хранятся в учетной записи хранилища.

        Следующая команда возвращает свойства RoleName и DiskName всех дисков VM в учетной записи хранилища. RoleName — это имя виртуальной машины, к которой подключен диск. Если эта команда возвращает диски, убедитесь, что виртуальные машины, к которым подключены эти диски, будут перенесены до того, как вы переносите учетную запись хранилища.
        ```powershell
         $storageAccountName = 'yourStorageAccountName'
          Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Select-Object -ExpandProperty AttachedTo -Property `
          DiskName | Format-List -Property RoleName, DiskName

        ```
    * Удалите неприкрепленные VM-диски, хранящиеся в учетной записи хранилища.

        Найдите непривязанные VM-диски в учетной записи хранилища, используя следующую команду:

        ```powershell
            $storageAccountName = 'yourStorageAccountName'
            Get-AzureDisk | where-Object {$_.MediaLink.Host.Contains($storageAccountName)} | Where-Object -Property AttachedTo -EQ $null | Format-List -Property DiskName  

        ```
        Если предыдущая команда возвращает диски, удалите эти диски, используя следующую команду:

        ```powershell
           Remove-AzureDisk -DiskName 'yourDiskName'
        ```
    * Удалите изображения VM, хранящиеся в учетной записи хранилища.

        Следующая команда возвращает все изображения VM с дисками ОС, хранящимися в учетной записи хранилища.
         ```powershell
            Get-AzureVmImage | Where-Object { $_.OSDiskConfiguration.MediaLink -ne $null -and $_.OSDiskConfiguration.MediaLink.Host.Contains($storageAccountName)`
                                    } | Select-Object -Property ImageName, ImageLabel
         ```
         Приведенная ниже команда возвращает сведения об образах всех виртуальных машин, диски данных которых хранятся в учетной записи хранения.
         ```powershell

            Get-AzureVmImage | Where-Object {$_.DataDiskConfigurations -ne $null `
                                             -and ($_.DataDiskConfigurations | Where-Object {$_.MediaLink -ne $null -and $_.MediaLink.Host.Contains($storageAccountName)}).Count -gt 0 `
                                            } | Select-Object -Property ImageName, ImageLabel
         ```
        Удалите все изображения VM, возвращенные предыдущими командами, с помощью этой команды:
        ```powershell
        Remove-AzureVMImage -ImageName 'yourImageName'
        ```
* Проверка учетных записей хранения и начало миграции.

    Подготовьте каждую учетную запись хранения к переносу, используя следующую команду. В этом примере имя учетной записи хранения — **myStorageAccount**. Замените имя в примере именем своей учетной записи хранения.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Validate -StorageAccountName $storageAccountName
    ```

    Следующим шагом является подготовка учетной записи хранилища для миграции.

    ```powershell
        $storageAccountName = "myStorageAccount"
        Move-AzureStorageAccount -Prepare -StorageAccountName $storageAccountName
    ```

    Проверьте конфигурацию для подготовленной учетной записи хранения с помощью Azure PowerShell или портала Azure. Если вы не готовы к миграции и хотите вернуться в старое состояние, используйте следующую команду:

    ```powershell
        Move-AzureStorageAccount -Abort -StorageAccountName $storageAccountName
    ```

    Если подготовленная конфигурация вас устраивает, можете продолжить процесс. Выполните следующую команду, чтобы зафиксировать ресурсы.

    ```powershell
        Move-AzureStorageAccount -Commit -StorageAccountName $storageAccountName
    ```

## <a name="next-steps"></a>Дальнейшие действия
* [Обзор поддерживаемого платформой переноса ресурсов IaaS из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Техническое руководство по поддерживаемому платформой переносу из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-deep-dive.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Планирование переноса ресурсов IaaS из классической модели развертывания в модель Azure Resource Manager.](migration-classic-resource-manager-plan.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Перенос ресурсов IaaS из классической модели в модель Azure Resource Manager с помощью интерфейса командной строки Azure](../linux/migration-classic-resource-manager-cli.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Community tools to migrate IaaS resources from classic to Azure Resource Manager](migration-classic-resource-manager-community-tools.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (Инструменты сообщества для перемещения ресурсов IaaS из классической модели в модель Azure Resource Manager)
* [Распространенные ошибки миграции](migration-classic-resource-manager-errors.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
* [Часто задаваемые вопросы о миграции из классической модели в модель Azure Resource Manager](migration-classic-resource-manager-faq.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)
