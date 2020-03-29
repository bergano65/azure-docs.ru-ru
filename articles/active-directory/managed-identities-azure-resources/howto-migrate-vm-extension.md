---
title: Прекратите использовать расширение управляемой идентификации VM - Azure AD
description: Шаг за шагом инструкции прекратить использование расширения VM и начать использовать службу метаданных экземпляров Azure (IMDS) для проверки подлинности.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/25/2018
ms.author: markvi
ms.openlocfilehash: 01b8e1dbc290bed86ccfc3c7016e8bd9168e427a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80049062"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Как отказаться от использования расширения управляемых идентификаторов виртуальной машины и начать использовать службу метаданных экземпляров Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Виртуальное расширение машины для управляемых идентификационных данных

Виртуальное расширение машины для управляемых идентификационных данных используется для запроса токенов для управляемого идентификатора в виртуальной машине. Рабочий процесс состоит из следующих шагов:

1. Во-первых, рабочая нагрузка в `http://localhost/oauth2/token` ресурсе вызывает локальную конечную точку для запроса токена доступа.
2. Затем расширение виртуальной машины использует учетные данные для управляемого итогового устройства, чтобы запросить токен доступа от Azure AD. 
3. Конкен доступа возвращается вызывающему вызываемому вызываюшему абоненту и может использоваться для проверки подлинности служб, поддерживающих аутентификацию Azure AD, таких как Azure Key Vault или Azure Storage.

Из-за ряда ограничений, изложенных в следующем разделе, управляемое расширение VM-идентификации было урезано в пользу использования эквивалентной конечной точки в Службе метаданных экземпляров Azure (IMDS)

### <a name="provision-the-extension"></a>Обеспечение продления 

При настройке виртуальной машины или виртуальной шкалы машины для управляемого удостоверения личности можно по желанию `-Type` предоставить управляемые идентификаторы для расширения ресурсов Azure VM с помощью параметра на cmdlet [Set-AzVMExtension.](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) Вы можете `ManagedIdentityExtensionForWindows` пройти `ManagedIdentityExtensionForLinux`либо или , в зависимости от типа `-Name` виртуальной машины, и назвать его с помощью параметра. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Вы также можете использовать шаблон развертывания ресурсов Azure Manager для предоставления расширения `resources` VM, добавив `ManagedIdentityExtensionForLinux` следующий JSON в раздел к шаблону (использовать для имени и типа элементы для версии Linux).

    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
    
Если вы работаете с виртуальными наборами масштабов машин, можно также предоставить управляемые идентификаторы для ресурсов Azure виртуального расширения масштабов машин с помощью cmdlet [Add-AzVmssExtension.](/powershell/module/az.compute/add-azvmssextension) Вы можете `ManagedIdentityExtensionForWindows` пройти `ManagedIdentityExtensionForLinux`либо или , в зависимости от типа виртуального набора масштаба машины, и назвать его с помощью `-Name` параметра. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Чтобы обеспечить расширение виртуальной шкалы машин с шаблоном развертывания Azure `extensionpProfile` Resource Manager, `ManagedIdentityExtensionForLinux` добавьте следующий JSON в раздел к шаблону (использовать для элементов имени и типа для версии Linux).

    ```json
    "extensionProfile": {
        "extensions": [
            {
                "name": "ManagedIdentityWindowsExtension",
                "properties": {
                    "publisher": "Microsoft.ManagedIdentity",
                    "type": "ManagedIdentityExtensionForWindows",
                    "typeHandlerVersion": "1.0",
                    "autoUpgradeMinorVersion": true,
                    "settings": {
                        "port": 50342
                    },
                    "protectedSettings": {}
                }
            }
    ```

Подготовка расширения виртуальной машины может не состояться из-за сбоев в поиске DNS. Если это произойдет, перезапустите виртуальную машину и повторите попытку. 

### <a name="remove-the-extension"></a>Удаление расширения 
Чтобы удалить расширение, `-n ManagedIdentityExtensionForWindows` `-n ManagedIdentityExtensionForLinux` используйте или переключитесь (в зависимости от типа виртуальной машины) с [удалением расширения az vm](https://docs.microsoft.com/cli/azure/vm/) `Remove-AzVMExtension` или [расширением az vmss](https://docs.microsoft.com/cli/azure/vmss) для виртуальных наборов масштабов машины с помощью Azure CLI или для Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Приобретите токен с помощью расширения виртуальной машины

Ниже приводится пример запроса с использованием управляемых идентификаторов для ресурсов Azure VM Extension Endpoint:

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Элемент | Описание |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://localhost:50342/oauth2/token` | Конечная точка управляемых удостоверений для ресурсов Azure, где 50342 — порт по умолчанию, который можно настроить. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Он также отображается в утверждении (аудитории) `aud` выданного маркера. В этом примере запрашивается маркер для доступа к Azure Resource Manager, который имеет универсальный код ресурса (URI) идентификатора приложения `https://management.azure.com/`. |
| `Metadata` | Поле заголовка HTTP-запроса, необходимое управляемым удостоверениям для ресурсов Azure для устранения рисков от атак с подделкой запроса со стороны сервера (SSRF). Должно быть присвоено значение true (все в нижнем регистре).|
| `object_id` | (Необязательно.) Параметр строки запроса, указывающий object_id управляемого удостоверения, для которого требуется токен. Требуется, если виртуальная машина имеет несколько управляемых удостоверений, назначаемых пользователем.|
| `client_id` | (Необязательно.) Параметр строки запроса, указывающий client_id управляемого удостоверения, для которого требуется токен. Требуется, если виртуальная машина имеет несколько управляемых удостоверений, назначаемых пользователем.|


Пример ответа:

```
HTTP/1.1 200 OK
Content-Type: application/json
{
  "access_token": "eyJ0eXAi...",
  "refresh_token": "",
  "expires_in": "3599",
  "expires_on": "1506484173",
  "not_before": "1506480273",
  "resource": "https://management.azure.com/",
  "token_type": "Bearer"
}
```

| Элемент | Описание |
| ------- | ----------- |
| `access_token` | Запрашиваемый маркер доступа. При вызове защищенного REST API маркер внедряется в поле `Authorization` заголовка запроса в качестве маркера носителя, позволяя API выполнить проверку подлинности вызывающего объекта. | 
| `refresh_token` | Не используется управляемыми удостоверениями для ресурсов Azure. |
| `expires_in` | Количество секунд, в течение которых маркер доступа является действительным, прежде чем его срок действия истечет (с момента выдачи). Время выдачи можно найти в утверждении `iat` маркера. |
| `expires_on` | Период времени после истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `exp` маркера). |
| `not_before` | Период времени, когда маркер доступа вступает в силу и может быть принят. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `nbf` маркера). |
| `resource` | Ресурс, для которого был запрошен маркер доступа, соответствует параметру строки запроса `resource`. |
| `token_type` | Тип маркера, который является маркером доступа носителя, что значит, что ресурс может предоставлять доступ носителю этого маркера. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Устранение проблем с расширением виртуальной машины 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Перезапустить расширение виртуальной машины после сбоя

При завершении работы расширения в Windows и некоторых версиях Linux вы можете перезапустить его вручную с помощью следующего командлета:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Где: 
- Имя и тип расширения для Windows:`ManagedIdentityExtensionForWindows`
- Имя и тип расширения для Linux:`ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Скрипт автоматизации завершается ошибкой при попытке экспорта схемы для расширения управляемых удостоверений для ресурсов Azure

При включении управляемых идентификационных данных для ресурсов Azure на виртуальной машине при попытке использовать функцию «Автоматики» для виртуальной машины или ее группы ресурсов отображается следующая ошибка:

![Ошибка экспорта скрипта автоматизации управляемых удостоверений для ресурсов Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Управляемые идентификаторы для виртуальных ресурсов Azure в настоящее время не поддерживают возможность экспорта своей схемы в шаблон группы ресурсов. Поэтому созданный шаблон не содержит параметры конфигурации для включения управляемых удостоверений для ресурсов Azure в ресурсе. Эти разделы можно добавлять вручную, следуя примерам в [настройке управляемых идентификаторов для ресурсов Azure на виртуальной машине Azure с помощью шаблонов.](qs-configure-template-windows-vm.md)

Когда функциональность экспорта схемы станет доступна для управляемых идентификаторов для расширения виртуальных машин Ресурсов Azure (планируется к амортизацию в январе 2019 года), она будет указана в [группах экспорта ресурсов, содержащих расширения VM.](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions)

## <a name="limitations-of-the-virtual-machine-extension"></a>Ограничения расширения виртуальной машины 

Есть несколько основных ограничений на использование виртуального расширения машины. 

 * Наиболее серьезным ограничением является тот факт, что учетные данные, используемые для запроса токенов, хранятся на виртуальной машине. Злоумышленник, успешно нарушавший виртуальную машину, может выявлять учетные данные. 
 * Кроме того, расширение виртуальной машины по-прежнему не поддерживается несколькими дистрибутивами Linux, с огромными затратами на разработку, чтобы изменить, построить и протестировать расширение на каждом из этих дистрибутивов. В настоящее время поддерживаются только следующие дистрибутивы Linux: 
    * CoreOS Stable
    * CentOS 7.1; 
    * Red Hat 7.2 
    * Ubuntu 15.04. 
    * Ubuntu 16.04
 * Развертывание виртуальных машин с управляемыми удостоверениями влияет на производительность, так как необходимо также обеспечить расширение виртуальной машины. 
 * Наконец, расширение виртуальной машины может поддерживать только наличие 32 назначенных пользователем управляемых идентификационных данных на виртуальную машину. 

## <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure

[Служба метаданных экземпляров Azure Instance (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) — это конечная точка REST, которая предоставляет информацию о запуске виртуальных экземпляров машин, которые могут быть использованы для управления и настройки виртуальных машин. Конечная точка доступна на известном IP-адресе,`169.254.169.254`который может быть доступен только внутри виртуальной машины.

Использование IMDS Azure IMDS имеет несколько преимуществ для запроса токенов. 

1. Услуга является внешней для виртуальной машины, поэтому учетные данные, используемые управляемыми идентификаторами, больше не присутствуют на виртуальной машине. Вместо этого они размещаются и закрепляются на хост-машине виртуальной машины Azure.   
2. Все операционные системы Windows и Linux, поддерживаемые Azure IaaS, могут использовать управляемые идентификаторы.
3. Развертывание происходит быстрее и проще, так как расширение VM больше не требуется.
4. С точки зрения IMDS до 1000 управляемых идентификаторов, назначенных пользователем, могут быть назначены одной виртуальной машине.
5. Нет никаких существенных изменений в запросах с использованием IMDS, в отличие от тех, которые используют расширение виртуальной машины, поэтому портировать существующие развертывания, которые в настоящее время используют расширение виртуальной машины, довольно просто портировать.

По этим причинам служба Azure IMDS будет дефакто-способом запроса токенов после того, как расширение виртуальной машины будет утихано. 


## <a name="next-steps"></a>Next Steps

* [Как использовать управляемые идентификаторы для ресурсов Azure в виртуальной машине Azure для получения токена доступа](how-to-use-vm-token.md)
* [Служба метаданных экземпляров Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
