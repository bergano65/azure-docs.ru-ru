---
title: Добавление runbooks Azure Automation в планы восстановления сайта
description: Узнайте, как расширить планы восстановления с Azure Automation для аварийного восстановления с помощью Azure Site Recovery.
author: rajani-janaki-ram
manager: gauravd
ms.service: site-recovery
ms.topic: conceptual
ms.date: 09/18/2019
ms.author: rajanaki
ms.openlocfilehash: ecfe993a137ca63c84438870ec54ac1e6d6707da
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257489"
---
# <a name="add-azure-automation-runbooks-to-recovery-plans"></a>Добавление модулей Runbook службы автоматизации Azure в планы восстановления

В этой статье описывается, как интегрировать runbooks Azure Automation для расширения планов [восстановления сайтов Azure.](site-recovery-overview.md) Мы покажем вам, как автоматизировать основные задачи, которые в противном случае нуждаются в ручном вмешательстве, и как преобразовать многоступенчатое восстановление в действие одним щелчком мыши.

## <a name="recovery-plans"></a>Планы восстановления 

Планы восстановления можно использовать при отказе от использования в непредподанных машинах или VMs Azure. Планы восстановления помогут вам определить систематический процесс восстановления, который определяет, как машины выводятся из строя, и как они начинаются и восстанавливаются после сбоя. 

Восстановление больших приложений может быть сложным. Планы восстановления помогают навести порядок, чтобы восстановление было последовательно точным, повторяемым и автоматизированным. Вы можете автоматизировать задачи в плане восстановления с помощью скриптов, а также runbooks Azure Automation. Типичными примерами могут быть настройка настроек на VM Azure после сбоя или перенастройка приложения, работающего на VM.

- [Узнайте подробнее](recovery-plan-overview.md) о планах восстановления.
- [Узнайте больше](../automation/automation-runbook-types.md) о runbooks Azure Automation.



## <a name="runbooks-in-recovery-plans"></a>Runbooks в планах восстановления

Вы добавляете учетную запись Azure Automation и runbooks в план восстановления. Запуск вызывается при запуске плана восстановления.

- Учетная запись Automation может быть в любом регионе Azure и должна находиться в той же подписке, что и хранилище восстановления сайта. 
- Запуск может работать в плане восстановления во время сбоя из основного места на вторичное или во время сбоя из вторичного места в основной.
- Runbooks в плане восстановления запускаться последовательно, один за другим, в установленном порядке.
- Если запуски в плане восстановления настраивают ВМ для запуска в разных группах, план восстановления будет продолжен только тогда, когда Azure сообщает о запуске всех вс-ам.
- Планы восстановления продолжают выполняться, даже если скрипт выходит из строя.

### <a name="recovery-plan-context"></a>Контекст плана восстановления

При запуске скрипта в сценарий контекст плана восстановления вводится в runbook. Контекст содержит переменные, обобщенные в таблице.

| **Имя переменной** | **Описание** |
| --- | --- |
| RecoveryPlanName |Имя плана восстановления. Используется в действиях, основанных на имени. |
| FailoverType |Уточняется, является ли это тестили или сбой производства. 
| FailoverDirection | Уточняется, является ли восстановление первичным или вторичным местом. |
| GroupID |Определяет номер группы в плане восстановления при выполнении плана. |
| VmMap |Массив всех виртуальных машин в группе. |
| Ключ VMMap |Уникальный ключ (GUID) для каждой виртуальной машины. |
| SubscriptionId |Идентификатор подписки Azure, в которой создана виртуальная машина. |
| ResourceGroupName | Название ресурсной группы, в которой находится VM.
| CloudServiceName |Имя облачной службы Azure, в которой создана виртуальная машина. |
| RoleName |Название Azure VM. |
| RecoveryPointId|Отметка времени для восстановления VM. |

Ниже приведен пример того, как выглядит переменная контекста.

```
{"RecoveryPlanName":"hrweb-recovery",
"FailoverType":"Test",
"FailoverDirection":"PrimaryToSecondary",
"GroupId":"1",
"VmMap":{"7a1069c6-c1d6-49c5-8c5d-33bfce8dd183":
    { "SubscriptionId":"7a1111111-c1d6-49c5-8c5d-111ce8dd183",
    "ResourceGroupName":"ContosoRG",
    "CloudServiceName":"pod02hrweb-Chicago-test",
    "RoleName":"Fabrikam-Hrweb-frontend-test",
    "RecoveryPointId":"TimeStamp"}
    }
}
```

Если вы хотите получить доступ ко всем вм-кодам в VMMap в цикле, вы можете использовать следующий код:

```
$VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
$vmMap = $RecoveryPlanContext.VmMap
    foreach($VMID in $VMinfo)
    {
        $VM = $vmMap.$VMID                
            if( !(($VM -eq $Null) -Or ($VM.ResourceGroupName -eq $Null) -Or ($VM.RoleName -eq $Null))) {
            #this check is to ensure that we skip when some data is not available else it will fail
    Write-output "Resource group name ", $VM.ResourceGroupName
    Write-output "Rolename " = $VM.RoleName
            }
        }
```


Блог Амана Шармы на [Harvesting Clouds](http://harvestingclouds.com) имеет полезный пример [сценария контекста плана восстановления.](http://harvestingclouds.com/post/script-sample-azure-automation-runbook-for-asr-recovery-plan/)



## <a name="before-you-start"></a>Перед началом работы

- Если вы новичок в Azure Automation, вы можете [зарегистрироваться](https://azure.microsoft.com/services/automation/) и [загрузить примеры скриптов.](https://azure.microsoft.com/documentation/scripts/)
- Убедитесь, что в учетную запись службы автоматизации добавлены следующие модули.
    - AzureRM.profile
    - AzureRM.Resources
    - AzureRM.Automation
    - AzureRM.Network
    - AzureRM.Compute

    Все эти модули должны иметь совместимые версии. Самый простой способ заключается в том, чтобы всегда использовать последние версии всех модулей.



## <a name="customize-the-recovery-plan"></a>Настройка плана восстановления

1. В хранилище выберите **планы восстановления (Восстановление сайта)**
2. Чтобы создать план восстановления, щелкните **«План восстановления**». Ознакомьтесь с [дополнительными сведениями](site-recovery-create-recovery-plans.md). Если у вас уже есть план восстановления, а затем выберите, чтобы открыть его.
3. На странице плана восстановления нажмите **Назаказ.**

    ![Нажмите кнопку "Настроить"](media/site-recovery-runbook-automation-new/custom-rp.png)

2. Нажмите на эллипсы (...) рядом с **группой 1: Начало** > **добавления действия поста.**
3. В **действии Insert,** проверить, что **сценарий** выбран, и указать имя для сценария (**Hello World**).
4. Укажите учетную запись автоматизации и выберите книгу. Нажмите кнопку **ОК**, чтобы сохранить этот сценарий. Сценарий будет добавлен в группу **Группа 1: последующие действия**.


## <a name="reuse-a-runbook-script"></a>Повторное использование сценария runbook

Можно использовать один сценарий runbook в нескольких планах восстановления, используя внешние переменные. 

- Для хранения параметров для выполнения плана восстановления используются [переменные Azure Automation.](../automation/automation-variables.md)
- Если перед именем переменной вы укажете имя плана восстановления, для каждого плана восстановления будут созданы собственные переменные, которые можно использовать в качестве параметров.
- Эти параметры можно изменять, чтобы управлять работой скрипта, не изменяя его содержимое, однако принцип работы сценария будет изменен.

### <a name="use-a-simple-string-variable-in-a-runbook-script"></a>Использование простых строковых переменных в сценарии Runbook

В этом примере скрипт использует входную группу сетевой безопасности (NSG) и применяет его к вводам в план восстановления. 

1. Чтобы скрипт мог определить, какой план восстановления запущен, используйте контекст этого плана восстановления:

    ```
    workflow AddPublicIPAndNSG {
        param (
              [parameter(Mandatory=$false)]
              [Object]$RecoveryPlanContext
        )

        $RPName = $RecoveryPlanContext.RecoveryPlanName
    ```

2. Обратите внимание на имя и группу ресурсов NSG. Эти переменные используются в качестве входных данных для скриптов плана восстановления. 
1. В активах счета Автоматизации. создать переменную для хранения имени NSG. Добавьте префикс к переменной имени с именем плана восстановления.

    ![Создание переменной для имени NSG](media/site-recovery-runbook-automation-new/var1.png)

2. Создайте переменную для хранения имени группы ресурсов для ресурса NSG. Добавьте префикс к переменной имени с именем плана восстановления.

    ![Создание имени группы ресурсов NSG](media/site-recovery-runbook-automation-new/var2.png)


3.  В скрипте используйте этот справочный код для получения переменных значений:

    ```
    $NSGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSG"
    $NSGRGValue = $RecoveryPlanContext.RecoveryPlanName + "-NSGRG"

    $NSGnameVar = Get-AutomationVariable -Name $NSGValue
    $RGnameVar = Get-AutomationVariable -Name $NSGRGValue
    ```

4.  Используйте переменные в модуле Runbook, что позволит применить NSG к сетевому интерфейсу виртуальной машины, для которой выполняется отработка отказа.

    ```
    InlineScript {
    if (($Using:NSGname -ne $Null) -And ($Using:NSGRGname -ne $Null)) {
            $NSG = Get-AzureRmNetworkSecurityGroup -Name $Using:NSGname -ResourceGroupName $Using:NSGRGname
            Write-output $NSG.Id
            #Apply the NSG to a network interface
            #$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
            #Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
            #  -AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $NSG
        }
    }
    ```


Для каждого плана восстановления создайте независимые переменные, чтобы скрипт можно было использовать повторно. Перед именами переменных добавьте имя плана восстановления. 

Для полного, сквозного сценария для этого сценария просмотрите [этот сценарий.](https://gallery.technet.microsoft.com/Add-Public-IP-and-NSG-to-a6bb8fee)


### <a name="use-a-complex-variable-to-store-more-information"></a>Использование сложной переменной для хранения дополнительных сведений

В некоторых сценариях возможно, что вы не сможете создавать отдельные переменные для каждого плана восстановления. Рассмотрим сценарий, в котором требуется один сценарий для присвоения общедоступного IP-адреса на определенных ВМ. Этот же подход будет полезен, если нужно применить разные группы безопасности сети для нескольких (но не всех) виртуальных машин. Обратите внимание на следующее.

- Можно сделать сценарий, который можно многоразового для любого плана восстановления.
- Каждый план восстановления может иметь разное число виртуальных машин.
- Например, точка восстановления SharePoint имеет два внешних интерфейса, а простые бизнес-приложения — только один.
- В этом сценарии нельзя создавать отдельные переменные для каждого плана восстановления.

В следующем примере мы создаем [сложную переменную](https://docs.microsoft.com/powershell/module/servicemanagement/azure/set-azureautomationvariable) в учетной записи Azure Automation.

Мы делаем это, указывая несколько значений, используя Azure PowerShell.

1. Используя PowerShell, войдите в свою подписку Azure.

    ```
    Connect-AzureRmAccount
    $sub = Get-AzureRmSubscription -Name <SubscriptionName>
    $sub | Select-AzureRmSubscription
    ```

2. Для хранения параметров создайте сложную переменную, используя название плана восстановления:

    ```
    $VMDetails = @{"VMGUID"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"};"VMGUID2"=@{"ResourceGroupName"="RGNameOfNSG";"NSGName"="NameOfNSG"}}
        New-AzureRmAutomationVariable -ResourceGroupName <RG of Automation Account> -AutomationAccountName <AA Name> -Name <RecoveryPlanName> -Value $VMDetails -Encrypted $false
    ```

3. В этой сложной переменной параметр **VMDetails** содержит идентификатор защищенной виртуальной машины. Этот идентификатор указан в свойствах виртуальной машины на портале Azure. На снимке экрана ниже показано, что мы создали переменную для хранения сведений о двух виртуальных машинах.

    ![Используйте идентификатор виртуальной машины в качестве идентификатора GUID](media/site-recovery-runbook-automation-new/vmguid.png)

4. Используйте эту переменную в модуле runbook. Если указанный GUID виртуальной машины находится в контексте плана восстановления, примените группу NSG к виртуальной машине.

    ```
    $VMDetailsObj = (Get-AutomationVariable -Name $RecoveryPlanContext.RecoveryPlanName).ToObject([hashtable])
    ```

4. В модуле Runbook выполните циклический перебор виртуальных машин, входящих в контекст плана восстановления, проверяя наличие этих виртуальных машин в **$VMDetailsObj**. Если встречается совпадение, примените группу NSG, используя свойства переменной.

    ```
        $VMinfo = $RecoveryPlanContext.VmMap | Get-Member | Where-Object MemberType -EQ NoteProperty | select -ExpandProperty Name
        $vmMap = $RecoveryPlanContext.VmMap

        foreach($VMID in $VMinfo) {
            $VMDetails = $VMDetailsObj[$VMID].ToObject([hashtable]);
            Write-output $VMDetails
            if ($VMDetails -ne $Null) { #If the VM exists in the context, this will not be Null
                $VM = $vmMap.$VMID
                # Access the properties of the variable
                $NSGname = $VMDetails.NSGName
                $NSGRGname = $VMDetails.NSGResourceGroupName

                # Add code to apply the NSG properties to the VM
            }
        }
    ```

Этот же сценарий можно использовать с разными планами восстановления, сохраняя в разных переменных значения параметров, применимых для соответствующих планов восстановления.

## <a name="sample-scripts"></a>Примеры сценариев

Разверните примеры сценариев в учетной записи службы автоматизации с помощью кнопки **Развертывание в Azure**.

[![Развертывание в Azure](https://azurecomcdn.azureedge.net/mediahandler/acomblog/media/Default/blog/c4803408-340e-49e3-9a1f-0ed3f689813d.png)](https://aka.ms/asr-automationrunbooks-deploy)

Это видео является еще одним примером. в котором показано, как выполнить восстановление двухуровневого приложения WordPress в Azure.


> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/One-click-failover-of-a-2-tier-WordPress-application-using-Azure-Site-Recovery/player]


## <a name="next-steps"></a>Дальнейшие действия

- Подробнее о [запуске автоматизации Azure как учетной записи](../automation/automation-create-runas-account.md)
- Просмотрите [примеры скриптов Azure Automation.](https://gallery.technet.microsoft.com/scriptcenter/site/search?f%5B0%5D.Type=User&f%5B0%5D.Value=SC%20Automation%20Product%20Team&f%5B0%5D.Text=SC%20Automation%20Product%20Team)
- [Подробнее](site-recovery-failover.md) о сбоях в беге.



