---
title: Создание виртуальной машины в DevTest Labs с помощью Azure PowerShell | Документация Майкрософт
description: Узнайте, как использовать Azure DevTest Labs для создания и управления виртуальными машинами с помощью Azure PowerShell.
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
ms.date: 04/02/2019
ms.author: spelluru
ms.openlocfilehash: 0e68958070e9c35e12dd9446b351f880dfea6f69
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009348"
---
# <a name="create-a-virtual-machine-with-devtest-labs-using-azure-powershell"></a>Создание виртуальной машины с помощью DevTest Labs с помощью Azure PowerShell
В этой статье показано, как создать виртуальную машину в Azure DevTest Labs с помощью Azure PowerShell. Скрипты PowerShell можно использовать для автоматизации создания виртуальных машин в лаборатории в Azure DevTest Labs. 

## <a name="prerequisites"></a>Технические условия
Перед началом работы

- [Создание лаборатории](devtest-lab-create-lab.md) Если вы не хотите использовать имеющейся лаборатории для тестирования сценария или команды в этой статье. 
- [Установите Azure PowerShell](/powershell/azure/azurerm/other-install) или использовать Azure Cloud Shell, который интегрирован в портал Azure. 

## <a name="powershell-script"></a>Сценарий PowerShell
Пример сценария в этом разделе использует [Invoke-AzureRmResourceAction](/powershell/module/azurerm.resources/invoke-azurermresourceaction) командлета.  Этот командлет принимает идентификатор ресурса лаборатории, имя действия для выполнения (`createEnvironment`), и параметры, необходимые выполнение этого действия. Параметры находятся в хэш-таблицу, содержащую все свойства описание виртуальной машины. 

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
        $SubscriptionId = (Get-AzureRmContext).Subscription.SubscriptionId
    }

    $API_VERSION = '2016-05-15'
    $lab = Get-AzureRmResource -ResourceId "/subscriptions/$SubscriptionId/resourceGroups/$LabResourceGroup/providers/Microsoft.DevTestLab/labs/$LabName"

    if ($lab -eq $null) {
       throw "Unable to find lab $LabName resource group $LabResourceGroup in subscription $SubscriptionId."
    }

    #For this example, we are getting the first allowed subnet in the first virtual network
    #  for the lab.
    #If a specific virtual network is needed use | to find it. 
    #ie $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION) | Where-Object Name -EQ "SpecificVNetName"

    $virtualNetwork = @(Get-AzureRmResource -ResourceType  'Microsoft.DevTestLab/labs/virtualnetworks' -ResourceName $LabName -ResourceGroupName $lab.ResourceGroupName -ApiVersion $API_VERSION)[0]

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

    Invoke-AzureRmResourceAction -ResourceId $lab.ResourceId -Action 'createEnvironment' -Parameters $parameters -ApiVersion $API_VERSION -Force -Verbose
}
finally {
   popd
}
```

Свойства для виртуальной машины в приведенном выше сценарии позволяют создать виртуальную машину с Windows Server 2016 DataCenter, как операционная система. Для каждого типа виртуальной машины эти свойства будут немного отличаться. [Определение виртуальной машины](#define-virtual-machine) разделе показано, как определить, какие свойства будут использоваться в этом скрипте.

Следующая команда предоставляет пример выполнения сценария, сохраненного в имя файла: Create-LabVirtualMachine.ps1. 

```powershell
 PS> .\Create-LabVirtualMachine.ps1 -ResourceGroupName 'MyLabResourceGroup' -LabName 'MyLab' -userName 'AdminUser' -password 'Password1!' -VMName 'MyLabVM'
```

## <a name="define-virtual-machine"></a>Определение виртуальной машины
В этом разделе показано, как получить свойства, характерные для типа виртуальной машины, который вы хотите создать. 

### <a name="use-azure-portal"></a>Использование портала Azure
При создании виртуальной Машины на портале Azure, вы можете создать шаблон диспетчера ресурсов Azure. Не требуется для завершения процесса создания виртуальной Машины. Только вы выполните действия, пока не появится шаблон. Это лучший способ получить необходимые описание JSON, если у вас еще нет создания виртуальной Машины лаборатории. 

1. Перейдите на [портал Azure](https://portal.azure.com).
2. Выберите **все службы** в левом меню навигации.
3. Найдите и выберите **DevTest Labs** из списка служб. 
4. На **DevTest Labs** странице, выберите свою лабораторию в списке лабораторий.
5. На домашней странице для лаборатории, выберите **+ добавить** на панели инструментов. 
6. Выберите **базового образа** для виртуальной Машины. 
7. Выберите **параметры автоматизации** в нижней части страницы выше **отправить** кнопки. 
8. Вы увидите **шаблона Azure Resource Manager** для создания виртуальной машины. 
9. Сегмент JSON в **ресурсы** раздел имеет определение для типа изображения, выбранными ранее. 

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

В этом примере см., как получить определение образа Azure Marketplace. Определение пользовательского образа, формулы или среды можно получить таким же образом. Добавьте все артефакты, необходимые для виртуальной машины и задать дополнительные параметры, необходимые. После предоставления значения для обязательных полей и желаемые дополнительные поля, прежде чем выбрать **параметры автоматизации** кнопки.

### <a name="use-azure-rest-api"></a>С помощью Azure REST API
В следующей процедуре представлены действия, чтобы получить свойства изображения с помощью REST API: Эти действия подходят только для существующей виртуальной Машины в лаборатории. 

1. Перейдите к [список виртуальных машин -](/rest/api/dtl/virtualmachines/list) выберите **попробовать** кнопки. 
2. Выберите свою **подписку Azure**.
3. Введите **группу ресурсов для лаборатории**.
4. Введите **имя лаборатории**. 
5. Выберите **Запуск**.
6. Вы увидите **свойства изображения** зависимости, в которой была создана виртуальная машина. 



## <a name="next-steps"></a>Дальнейшие действия
См. в следующих материалах: [Документация по Azure PowerShell для Azure DevTest Labs](/powershell/module/az.devtestlabs/)
