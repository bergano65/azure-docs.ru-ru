---
title: Расширение Azure Key Vault VM для Linux
description: Развертывание агента, выполняющего автоматическое обновление сертификатов Key Vault на виртуальных машинах, с помощью виртуального расширения машины.
services: virtual-machines-linux
author: msmbaldwin
tags: keyvault
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 12/02/2019
ms.author: mbaldwin
ms.openlocfilehash: a4fb3ad2ce6225528910bbda9d98a38001242710
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79298994"
---
# <a name="key-vault-virtual-machine-extension-for-linux"></a>Расширение виртуальной машины Key Vault для Linux

Расширение Key Vault VM обеспечивает автоматическое обновление сертификатов, хранящихся в хранилище ключей Azure. В частности, расширение отслеживает список наблюдаемых сертификатов, хранящихся в хранилищах ключей.  При обнаружении изменения расширение получает и устанавливает соответствующие сертификаты. Расширение Key Vault VM публикуется и поддерживается корпорацией Майкрософт, в настоящее время на Linux VMs. В этом документе подробно описаны поддерживаемые платформы, конфигурации и варианты развертывания для расширения Key Vault VM для Linux. 

### <a name="operating-system"></a>Операционная система

Расширение Key Vault VM поддерживает эти дистрибутивы Linux:

- Улунту-1604
- Улунту-1804
- Дебиан-9
- Suse-15 

### <a name="supported-certificate-content-types"></a>Поддерживаемые типы содержимого сертификата

- PKCS #12

## <a name="extension-schema"></a>Схема расширения

В следующем JSON-файле показана схема для расширения виртуальной машины Key Vault. Расширению не требуются защищенные параметры, так как все его параметры считаются информационными и не влияют на безопасность. Для расширения необходим список отслеживаемых секретов, указание частоты опроса и конечное хранилище сертификатов. В частности:  
```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KVVMExtensionForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
        "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
          "certificateStoreName": <certificate store name, e.g.: "MY">,
          "linkOnRenewal": <Not available on Linux e.g.: false>,
          "certificateStoreLocation": <certificate store location, currently it works locally only e.g.: "LocalMachine">,
          "requireInitialSync": <initial synchronization of certificates e..g: true>,
          "observedCertificates": <list of KeyVault URIs representing monitored certificates, e.g.: "https://myvault.vault.azure.net/secrets/mycertificate"
        }      
      }
      }
    }
```

> [!NOTE]
> Ваши наблюдаемые сертификаты URL-адреса должны быть формы. `https://myVaultName.vault.azure.net/secrets/myCertName`
> 
> Это происходит `/secrets` потому, что путь возвращает полный `/certificates` сертификат, включая частный ключ, в то время как путь не возвращается. Более подробную информацию о сертификатах можно найти здесь: [Key Vault Сертификаты](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-certificates)


### <a name="property-values"></a>Значения свойств

| name | Значение и пример | Тип данных |
| ---- | ---- | ---- |
| версия_API | 2019-07-01 | Дата |
| publisher | Microsoft.Azure.KeyVault | строка |
| type | KeyVaultForLinux | строка |
| typeHandlerVersion | 1.0 | INT |
| pollingIntervalInS | 3600 | строка |
| certificateStoreName | MY | строка |
| linkOnRenewal | false | Логическое |
| certificateStoreLocation  | LocalMachine | строка |
| требуетсяInitialSync | Да | Логическое |
| observedCertificates  | ["https://myvault.vault.azure.net/secrets/mycertificate"] | массив строк


## <a name="template-deployment"></a>Развертывание шаблона

Расширения виртуальной машины Azure можно развернуть с помощью шаблонов Azure Resource Manager. Шаблоны идеально подходят для развертывания одной или нескольких виртуальных машин, требующих обновления сертификатов, выполняемого после развертывания. Расширение может быть развернуто для отдельных виртуальных виртуальных или виртуальных наборов масштаба машины. Для обоих типов шаблонов используются общие схема и конфигурация. 

Конфигурация JSON для виртуального расширения машины должна быть вложена внутри `"resources": []` фрагмента ресурса виртуальной машины шаблона, `"virtualMachineProfile":"extensionProfile":{"extensions" :[]` специально для шаблона виртуальной машины и в случае виртуального масштаба машины, установленного под объектом.

```json
    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "KeyVaultForLinux",
      "apiVersion": "2019-07-01",
      "location": "<location>",
      "dependsOn": [
          "[concat('Microsoft.Compute/virtualMachines/', <vmName>)]"
      ],
      "properties": {
      "publisher": "Microsoft.Azure.KeyVault",
      "type": "KeyVaultForLinux",
      "typeHandlerVersion": "1.0",
      "autoUpgradeMinorVersion": true,
      "settings": {
          "secretsManagementSettings": {
          "pollingIntervalInS": <polling interval in seconds, e.g. "3600">,
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
        $extName =  "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
       
    
        # Start the deployment
        Set-AzVmExtension -TypeHandlerVersion "1.0" -ResourceGroupName <ResourceGroupName> -Location <Location> -VMName <VMName> -Name $extName -Publisher $extPublisher -Type $extType -SettingString $settings
    
    ```

* Развертывание расширения на виртуальном наборе масштабов машины:

    ```powershell
    
        # Build settings
        $settings = '{"secretsManagementSettings": 
        { "pollingIntervalInS": "' + <pollingInterval> + 
        '", "certificateStoreName": "' + <certStoreName> + 
        '", "certificateStoreLocation": "' + <certStoreLoc> + 
        '", "observedCertificates": ["' + <observedCerts> + '"] } }'
        $extName = "KeyVaultForLinux"
        $extPublisher = "Microsoft.Azure.KeyVault"
        $extType = "KeyVaultForLinux"
        
        # Add Extension to VMSS
        $vmss = Get-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName>
        Add-AzVmssExtension -VirtualMachineScaleSet $vmss  -Name $extName -Publisher $extPublisher -Type $extType -TypeHandlerVersion "1.0" -Setting $settings

        # Start the deployment
        Update-AzVmss -ResourceGroupName <ResourceGroupName> -VMScaleSetName <VmssName> -VirtualMachineScaleSet $vmss 
    
    ```

## <a name="azure-cli-deployment"></a>Развертывание с помощью Azure CLI

CLI Azure можно использовать для развертывания расширения Key Vault VM в существующую виртуальную машину или набор виртуальной машины. 
 
* Развертывание расширения на виртуальной машине:
    
    ```azurecli
       # Start the deployment
         az vm extension set -n "KeyVaultForLinux" `
         --publisher Microsoft.Azure.KeyVault `
         -g "<resourcegroup>" `
         --vm-name "<vmName>" `
         --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

* Развертывание расширения на виртуальном наборе масштабов машины:

   ```azurecli
        # Start the deployment
        az vmss extension set -n "KeyVaultForLinux" `
        --publisher Microsoft.Azure.KeyVault `
        -g "<resourcegroup>" `
        --vm-name "<vmName>" `
        --settings '{\"secretsManagementSettings\": { \"pollingIntervalInS\": \"<pollingInterval>\", \"certificateStoreName\": \"<certStoreName>\", \"certificateStoreLocation\": \"<certStoreLoc>\", \"observedCertificates\": [\ <observedCerts>\"] }}'
    ```

Ознакомьтесь со следующими ограничениями и требованиями.
- Ограничения, касающиеся решения Key Vault:
  - оно должно существовать во время развертывания; 
  - Политика доступа Key Vault установлена для идентификации VM/VMSS с помощью MSI


## <a name="troubleshoot-and-support"></a>Устранение неполадок и поддержка

### <a name="troubleshoot"></a>Устранение неполадок

Данные о состоянии развертываний расширения можно получить на портале Azure, а также с помощью Azure PowerShell. Чтобы просмотреть состояние развертывания расширений для определенной виртуальной машины, выполните следующую команду в Azure PowerShell.

## <a name="azure-powershell"></a>Azure PowerShell
```powershell
Get-AzVMExtension -VMName <vmName> -ResourceGroupname <resource group name>
```

## <a name="azure-cli"></a>Azure CLI
```azurecli
 az vm get-instance-view --resource-group <resource group name> --name  <vmName> --query "instanceView.extensions"
```

### <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в какой-либо момент этой статьи, вы можете связаться с экспертами Azure на [форумах MSDN Azure и Stack Overflow.](https://azure.microsoft.com/support/forums/) Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и выберите Получить поддержку. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
