---
title: Расширение виртуальной машины Azure Key Vault для Windows | Документация Майкрософт
description: С помощью расширения виртуальной машины вы можете развернуть агент, выполняющий автоматическое обновление секретов Key Vault на виртуальных машинах.
services: virtual-machines-windows
author: msmbaldwin
ms.service: virtual-machines-windows
ms.topic: article
ms.date: 09/23/2018
ms.author: mbaldwin
ms.openlocfilehash: 43c4b363f223c61bac3d3f7dbd272519a0cd014d
ms.sourcegitcommit: 5acd8f33a5adce3f5ded20dff2a7a48a07be8672
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/24/2019
ms.locfileid: "72899046"
---
# <a name="key-vault-virtual-machine-extension-for-windows"></a>Расширение виртуальной машины Key Vault для Windows

Расширение Key Vault VM обеспечивает автоматическое обновление сертификатов, хранящихся в хранилище ключей Azure. В частности, расширение отслеживает список наблюдаемых сертификатов, хранящихся в хранилищах ключей, и при обнаружении изменения извлекает и устанавливает соответствующие сертификаты. В этом документе подробно описаны поддерживаемые платформы, конфигурации и параметры развертывания для расширения виртуальной машины Key Vault для Windows. 

### <a name="operating-system"></a>Операционная система

Расширение Key Vault VM поддерживает следующие версии Windows:

- Windows Server 2019
- Windows Server 2016
- Windows Server 2012

## <a name="extension-schema"></a>Схема расширения

В следующем JSON-файле показана схема для расширения виртуальной машины Key Vault. Для расширения не требуются защищенные параметры. все его параметры считаются общедоступными сведениями. Для расширения требуется список отслеживаемых сертификатов, периодичность опроса и хранилище сертификатов назначения. В частности:  

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "secretsManagementSettings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "linkOnRenewal": <Only Windows. This feature enables auto-rotation of SSL certificates, without necessitating a re-deployment or binding.  e.g.: false>,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "requireInitialSync": <initial synchronization of certificates e..g: true>,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```

> [!NOTE]
> Наблюдаемые URL-адреса сертификатов должны иметь форму `https://myVaultName.vault.azure.net/secrets/myCertName`.
> 
> Это связано с тем, что `/secrets` путь возвращает полный сертификат, включая закрытый ключ, а `/certificates` путь — нет. Дополнительные сведения о сертификатах можно найти здесь: [Key Vault Certificates](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates) .

### <a name="property-values"></a>Значения свойств

| Name | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2019-07-01 | date |
| publisher | Microsoft.Azure.KeyVault.Edp | string |
| Тип | KeyVaultForWindows | string |
| typeHandlerVersion | 1.0 | int |
| pollingIntervalInS | 3600 | int |
| certificateStoreName | MY | string |
| линконреневал | false | Логическое |
| certificateStoreLocation  | LocalMachine | string |
| рекуирединитиалсинк | true | Логическое |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | массив строк


## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих обновления сертификатов, выполняемого после развертывания. Расширение можно развернуть на отдельных виртуальных машинах или в масштабируемых наборах виртуальных машин. Для обоих типов шаблонов используются общие схема и конфигурация. 

Конфигурация JSON для расширения виртуальной машины должна быть вложена в фрагмент ресурса виртуальной машины шаблона, в частности `"resources": []` объект для шаблона виртуальной машины и в случае масштабируемого набора виртуальных машин в разделе `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` объект.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForWindows",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
            "publisher": "Microsoft.Azure.KeyVault.Edp",
            "type": "KeyVaultForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                    "pollingIntervalInS": <polling interval in seconds>,
                    "certificateStoreName": <certificate store name, e.g.: "MY">,
                    "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
                    "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
                }         
            }
      }
    }
```


## <a name="azure-powershell-deployment"></a>Развертывание с помощью Azure PowerShell

Azure PowerShell можно использовать для развертывания расширения виртуальной машины Key Vault на имеющейся виртуальной машине или в масштабируемых наборах виртуальных машин. 

* Развертывание расширения на виртуальной машине:
    
    ```powershell
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName =  "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Чтобы развернуть расширение в масштабируемом наборе виртуальных машин, выполните следующие действия.

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
            { "pollingIntervalInS": "' + <pollingInterval> + 
            '", "certificateStoreName": "' + <certStoreName> + 
            '", "certificateStoreLocation": "' + <certStoreLoc> + 
            '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForWindows"
        $extPublisher = "Microsoft.Azure.KeyVault.Edp"
        $extType = "KeyVaultForWindows"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

Azure CLI можно использовать для развертывания расширения виртуальной машины Key Vault в существующей виртуальной машине или в масштабируемом наборе виртуальных машин. 
 
* Развертывание расширения на виртуальной машине:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Чтобы развернуть расширение в масштабируемом наборе виртуальных машин, выполните следующие действия.

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForWindows" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vmss-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Ознакомьтесь со следующими ограничениями и требованиями.
- Ограничения, касающиеся решения Key Vault:
    - оно должно существовать во время развертывания; 
    - Политика доступа Key Vault настроена для удостоверения виртуальной машины или VMSS с помощью MSI


## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертываний расширения можно получить на портале Azure, а также с помощью Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

Выходные данные выполнения расширения регистрируются в следующем файле:

```
%windrive%\WindowsAzure\Logs\Plugins\Microsoft.Azure.KeyVault.Edp.KeyVaultForWindows\<version>\akvvm_service_<date>.log
```


### <a name="support"></a>Поддержка

Если в любой момент при изучении этой статьи вам потребуется дополнительная помощь, вы можете обратиться к экспертам по Azure на [форумах MSDN Azure и Stack Overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
