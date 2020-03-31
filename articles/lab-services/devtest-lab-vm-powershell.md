---
title: Создайте виртуальную машину в DevTest Labs с помощью Azure PowerShell
description: Узнайте, как использовать Лаборатории Azure DevTest для создания и управления виртуальными машинами с помощью Azure PowerShell.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: spelluru
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2020
ms.author: spelluru
ms.openlocfilehash: 13014c39641203bddadf858c34cff67462b3a4b3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76167106"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Создайте виртуальную машину с DevTest Labs с помощью Azure PowerShell
В этой статье показано, как создать виртуальную машину в Лабораториях Azure DevTest с помощью Azure PowerShell. Скрипты PowerShell можно использовать для автоматизации создания виртуальных машин в лаборатории В Azure DevTest Labs. 

## <a name="prerequisites"></a>Предварительные требования
Перед началом работы

- [Создайте лабораторию,](devtest-lab-create-lab.md) если вы не хотите использовать существующую лабораторию для тестирования скрипта или команд в этой статье. 
- [Установите Azure PowerShell](/powershell/azure/install-az-ps?view=azps-1.7.0) или используйте Azure Cloud Shell, интегрированную в портал Azure. 

## <a name="powershell-script"></a>Скрипт PowerShell
В примере скрипта в этом разделе используется cmdlet [Invoke-AzResourceAction.](/powershell/module/az.resources/invoke-azresourceaction?view=azps-1.7.0)  Это cmdlet принимает идентификатор ресурса лаборатории, название действия для выполнения (),`createEnvironment`и параметры, необходимые выполнить это действие. Параметры находятся в хэш-таблице, которая содержит все свойства описания виртуальной машины. 

```powershell
[CmdletBinding()]

Param(
[Parameter(Mandatory = $false)]  $SubscriptionId,
[Parameter(Mandatory = $true)]   $LabResourceGroup,
[Parameter(Mandatory = $true)]   $LabName,
[Parameter(Mandatory = $true)]   $NewVmName,
[Parameter(Mandatory = $true)]   $UserName,
[Parameter(Mandatory = $true)]   $Password
)
 
pushd $PSScriptRoot

try {
    if ($SubscriptionId -eq $null) {
        $SubscriptionId = (Get-AzContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

    $labSubnetName = $virtualNetwork.properties.allowedSubnets[0].labSubnetName

    #Prepare all the properties needed for the createEnvironment
    # call used to create the new VM.
    # The properties will be slightly different depending on the base of the vm
    # (a marketplace image, custom image or formula).
    # The setup of the virtual network to be used may also affect the properties.
    # This sample includes the properties to add an additional disk under dataDiskParameters
    
    $parameters = @{
       "name"      = $NewVmName;
       "location"  = $lab.Location;
       "properties" = @{
          "labVirtualNetworkId"     = $virtualNetwork.ResourceId;
          "labSubnetName"           = $labSubnetName;
          "notes"                   = "Windows Server 2016 Datacenter";
          "osType"                  = "windows"
          "expirationDate"          = "2019-12-01"
          "galleryImageReference"   = @{
             "offer"     = "WindowsServer";
             "publisher" = "MicrosoftWindowsServer";
             "sku"       = "2016-Datacenter";
             "osType"    = "Windows";
             "version"   = "latest"
          };
          "size"                    = "Standard_DS2_v2";
          "userName"                = $UserName;
          "password"                = $Password;
          "disallowPublicIpAddress" = $true;
          "dataDiskParameters" = @(@{
            "attachNewDataDiskOptions" = @{
                "diskName" = "adddatadisk"
                "diskSizeGiB" = "1023"
                "diskType" = "Standard"
                }
          "hostCaching" = "ReadWrite"
          })
       }
    }
    
    #The following line is the same as invoking
    # https://azure.github.io/projects/apis/#!/Labs/Labs_CreateEnvironment rest api

    Invoke-AzResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Свойства виртуальной машины в приведенном выше скрипте позволяют нам создать виртуальную машину с Windows Server 2016 DataCenter в качестве ОС. Для каждого типа виртуальной машины эти свойства будут немного отличаться. Раздел [«Определение виртуальной машины»](#define-virtual-machine) показывает, как определить, какие свойства использовать в этом скрипте.

Следующая команда приводит пример выполнения скрипта, сохраненного в имени файла: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Определите виртуальную машину
В этом разделе показано, как получить свойства, которые специфичны для типа виртуальной машины, которую вы хотите создать. 

### <a name="use-azure-portal"></a>Использование портала Azure
При создании VM-портала Azure можно создать шаблон менеджера ресурсов Azure. Вам не нужно завершать процесс создания VM. Вы выполняете только те шаги, пока не увидите шаблон. Это лучший способ получить необходимое описание JSON, если у вас еще нет лаборатории VM создан. 

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выберите **все службы** в левом навигационном меню.
3. Поиск и выбор **DevTest Labs** из списка услуг. 
4. На странице **DevTest Labs** выберите лабораторию в списке лабораторий.
5. На главной странице вашей лаборатории выберите **и добавьте** на панель инструментов. 
6. Выберите **базовое изображение** для VM. 
7. Выберите **параметры автоматизации** в нижней части страницы над кнопкой **«Отправить».** 
8. Для создания виртуальной машины можно увидеть **шаблон менеджера ресурсов Azure.** 
9. Сегмент JSON в разделе **ресурсов** имеет определение типа изображения, выбранного ранее. 

    ```json
    {
      "apiVersion": "2018-10-15-preview",
      "type": "Microsoft.DevTestLab/labs/virtualmachines",
      "name": "[variables('vmName')]",
      "location": "[resourceGroup().location]",
      "properties": {
        "labVirtualNetworkId": "[variables('labVirtualNetworkId')]",
        "notes": "Windows Server 2019 Datacenter",
        "galleryImageReference": {
          "offer": "WindowsServer",
          "publisher": "MicrosoftWindowsServer",
          "sku": "2019-Datacenter",
          "osType": "Windows",
          "version": "latest"
        },
        "size": "[parameters('size')]",
        "userName": "[parameters('userName')]",
        "password": "[parameters('password')]",
        "isAuthenticationWithSshKey": false,
        "labSubnetName": "[variables('labSubnetName')]",
        "disallowPublicIpAddress": true,
        "storageType": "Standard",
        "allowClaim": false,
        "networkInterface": {
          "sharedPublicIpAddressConfiguration": {
            "inboundNatRules": [
              {
                "transportProtocol": "tcp",
                "backendPort": 3389
              }
            ]
          }
        }
      }
    }
    ```

В этом примере вы увидите, как получить определение изображения Azure Market Place. Таким же образом можно получить определение пользовательского изображения, формулы или среды. Добавьте все артефакты, необходимые для виртуальной машины, и установите все необходимые настройки. После предоставления значений для требуемых полей и любых дополнительных полей, прежде чем выбрать кнопку **параметры автоматизации.**

### <a name="use-azure-rest-api"></a>Используйте API Azure REST
Следующая процедура дает вам шаги, чтобы получить свойства изображения с помощью REST API: Эти шаги работают только для существующего VM в лаборатории. 

1. Перейдите к [виртуальным машинам - список](/rest/api/dtl/virtualmachines/list) страницы, выберите **Попробуйте его** кнопку. 
2. Выберите **подписку На Azure.**
3. Введите **группу ресурсов для лаборатории.**
4. Введите **название лаборатории.** 
5. Выберите **Выполнить**.
6. Вы видите **свойства для изображения,** на основе которого был создан VM. 

## <a name="set-expiration-date"></a>Дата истечения срока действия
В таких сценариях, как обучение, демонстрации и испытания, может потребоваться создать виртуальные машины и удалить их автоматически после фиксированной продолжительности, чтобы не понести ненужных затрат. Можно установить дату истечения срока действия VM при его создании с помощью PowerShell, как показано в примере [раздела сценария PowerShell.](#powershell-script)

Вот пример сценария PowerShell, который устанавливает срок годности для всех существующих ВМ в лаборатории:

```powershell
# Values to change
$subscriptionId = '<Enter the subscription Id that contains lab>'
$labResourceGroup = '<Enter the lab resource group>'
$labName = '<Enter the lab name>'
$VmName = '<Enter the VmName>'
$expirationDate = '<Enter the expiration date e.g. 2019-12-16>'

# Log into your Azure account
Login-AzureRmAccount

Select-AzureRmSubscription -SubscriptionId $subscriptionId
$VmResourceId = "subscriptions/$subscriptionId/resourcegroups/$labResourceGroup/providers/microsoft.devtestlab/labs/$labName/virtualmachines/$VmName"

$vm = Get-AzureRmResource -ResourceId $VmResourceId -ExpandProperties

# Get all the Vm properties
$VmProperties = $vm.Properties

# Set the expirationDate property
If ($VmProperties.expirationDate -eq $null) {
    $VmProperties | Add-Member -MemberType NoteProperty -Name expirationDate -Value $expirationDate
} Else {
    $VmProperties.expirationDate = $expirationDate
}

Set-AzureRmResource -ResourceId $VmResourceId -Properties $VmProperties -Force
```


## <a name="next-steps"></a>Дальнейшие действия
Ниже приводится следующее содержание: [документация Azure PowerShell для лабораторий Azure DevTest](/powershell/module/az.devtestlabs/)
