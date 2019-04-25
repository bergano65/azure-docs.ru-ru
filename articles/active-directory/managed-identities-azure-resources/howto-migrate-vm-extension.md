---
title: Прекратить использование расширения виртуальной Машины управляемого удостоверения и начать использовать конечную точку службы метаданных экземпляров Azure
description: Пошаговые инструкции по с помощью расширения виртуальной Машины с помощью службы метаданных экземпляров (IMDS) Azure для проверки подлинности.
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
ms.openlocfilehash: 5b3c6c99b05320ee53c3ff49f5c299650c32e939
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60440835"
---
# <a name="how-to-stop-using-the-virtual-machine-managed-identities-extension-and-start-using-the-azure-instance-metadata-service"></a>Прекращение использования виртуальной машины управляемого удостоверения расширения и начать использовать службу метаданных экземпляров Azure

## <a name="virtual-machine-extension-for-managed-identities"></a>Расширение виртуальной машины для управляемых удостоверений

Расширение виртуальной машины для управляемых удостоверений используется для запроса маркеров для управляемого удостоверения виртуальной машины. Рабочий процесс состоит из следующих действий:

1. Во-первых, рабочей нагрузки в пределах ресурсов вызывает локальную конечную точку `http://localhost/oauth2/token` для запроса маркера доступа.
2. Расширение виртуальной машины затем использует учетные данные для управляемого удостоверения для запроса маркера доступа из Azure AD... 
3. Маркер доступа возвращается вызывающему объекту и может использоваться для проверки подлинности в службах, поддерживающих аутентификацию Azure AD, например Azure Key Vault или хранилище Azure.

Из-за нескольких ограничений, описанных в следующем разделе расширения управляемого удостоверения виртуальной Машины является устаревшим с помощью конечной точки в Azure службы метаданных экземпляров (IMDS)

### <a name="provision-the-extension"></a>Подготовка расширения 

При настройке виртуальной машины или масштабируемого набора требуется управляемое удостоверение виртуальных машин, вы можете необязательно решили, при необходимости можно подготовить управляемого удостоверения для расширения виртуальной Машины ресурсов Azure с помощью `-Type` параметр на [ SET-AzVMExtension](https://docs.microsoft.com/powershell/module/az.compute/set-azvmextension) командлета. Можно передать или `ManagedIdentityExtensionForWindows` или `ManagedIdentityExtensionForLinux`, в зависимости от типа виртуальной машины и назовите его с помощью `-Name` параметра. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

```powershell
   $settings = @{ "port" = 50342 }
   Set-AzVMExtension -ResourceGroupName myResourceGroup -Location WestUS -VMName myVM -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Settings $settings 
```

Можно также использовать шаблон развертывания Azure Resource Manager для подготовки расширения виртуальной Машины, добавив следующий код JSON для `resources` раздел к шаблону (использовать `ManagedIdentityExtensionForLinux` для имени и типа элементов для версии Linux).

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
    
    
Если вы работаете с масштабируемыми наборами виртуальных машин, можно также подготовить управляемых удостоверений для ресурсов Azure масштабируемого набора виртуальных машин с помощью расширения [AzVmssExtension добавить](/powershell/module/az.compute/add-azvmssextension) командлета. Можно передать или `ManagedIdentityExtensionForWindows` или `ManagedIdentityExtensionForLinux`, в зависимости от типа масштабирования виртуальных машин задайте и назовите его с помощью `-Name` параметра. В параметре `-Settings` указан порт, используемый конечной точкой токена OAuth для получения токена:

   ```powershell
   $setting = @{ "port" = 50342 }
   $vmss = Get-AzVmss
   Add-AzVmssExtension -VirtualMachineScaleSet $vmss -Name "ManagedIdentityExtensionForWindows" -Type "ManagedIdentityExtensionForWindows" -Publisher "Microsoft.ManagedIdentity" -TypeHandlerVersion "1.0" -Setting $settings 
   ```
Для подготовки виртуальной машины масштабируемого задать расширение с помощью шаблона развертывания Azure Resource Manager, добавьте следующий код JSON для `extensionpProfile` раздел к шаблону (использовать `ManagedIdentityExtensionForLinux` для имени и типа элементов для версии Linux).

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

Подготовки расширения виртуальной машины может завершиться ошибкой из-за ошибок при поиске DNS. Если ошибка повторится, перезапустите виртуальную машину и повторите попытку. 

### <a name="remove-the-extension"></a>Удаление расширения 
Чтобы удалить расширение, используйте `-n ManagedIdentityExtensionForWindows` или `-n ManagedIdentityExtensionForLinux` (в зависимости от типа виртуальной машины) с параметром [удалить расширение виртуальной машины az](https://docs.microsoft.com/cli/azure/vm/), или [удаления расширения az vmss](https://docs.microsoft.com/cli/azure/vmss) для масштабирования виртуальных машин Задает, с помощью Azure CLI или `Remove-AzVMExtension` для Powershell:

```azurecli-interactive
az vm identity --resource-group myResourceGroup --vm-name myVm -n ManagedIdentityExtensionForWindows
```

```azurecli-interactive
az vmss extension delete -n ManagedIdentityExtensionForWindows -g myResourceGroup -vmss-name myVMSS
```

```powershell
Remove-AzVMExtension -ResourceGroupName myResourceGroup -Name "ManagedIdentityExtensionForWindows" -VMName myVM
```

### <a name="acquire-a-token-using-the-virtual-machine-extension"></a>Получение маркера с помощью расширения виртуальной машины

Ниже приведен пример запроса с помощью управляемых удостоверений для ресурсов Azure конечной точки расширения виртуальной Машины.

```
GET http://localhost:50342/oauth2/token?resource=https%3A%2F%2Fmanagement.azure.com%2F HTTP/1.1
Metadata: true
```

| Элемент | ОПИСАНИЕ |
| ------- | ----------- |
| `GET` | HTTP-команда, указывающая, что необходимо извлечь данные из конечной точки. В этом случае используется маркер доступа OAuth. | 
| `http://localhost:50342/oauth2/token` | Конечная точка управляемых удостоверений для ресурсов Azure, где 50342 — порт по умолчанию, который можно настроить. |
| `resource` | Параметр строки запроса, указывающий URI идентификатора приложения целевого ресурса. Он также отображается в утверждении (аудитории) `aud` выданного маркера. В этом примере запрашивается маркер для доступа к Azure Resource Manager, который имеет универсальный код ресурса (URI) идентификатора приложения https://management.azure.com/. |
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

| Элемент | ОПИСАНИЕ |
| ------- | ----------- |
| `access_token` | Запрашиваемый маркер доступа. При вызове защищенного REST API маркер внедряется в поле `Authorization` заголовка запроса в качестве маркера носителя, позволяя API выполнить проверку подлинности вызывающего объекта. | 
| `refresh_token` | Не используется управляемыми удостоверениями для ресурсов Azure. |
| `expires_in` | Количество секунд, в течение которых маркер доступа является действительным, прежде чем его срок действия истечет (с момента выдачи). Время выдачи можно найти в утверждении `iat` маркера. |
| `expires_on` | Период времени после истечения срока действия маркера доступа. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `exp` маркера). |
| `not_before` | Период времени, когда маркер доступа вступает в силу и может быть принят. Дата представляется как количество секунд с 1970-01-01T0:0:0Z в формате UTC (соответствует утверждению `nbf` маркера). |
| `resource` | Ресурс, для которого был запрошен маркер доступа, соответствует параметру строки запроса `resource`. |
| `token_type` | Тип маркера, который является маркером доступа носителя, что значит, что ресурс может предоставлять доступ носителю этого маркера. |


### <a name="troubleshoot-the-virtual-machine-extension"></a>Устранение неполадок расширения виртуальной машины 

#### <a name="restart-the-virtual-machine-extension-after-a-failure"></a>Перезапустите расширение виртуальной машины после сбоя

При завершении работы расширения в Windows и некоторых версиях Linux вы можете перезапустить его вручную с помощью следующего командлета:

```powershell
Set-AzVMExtension -Name <extension name>  -Type <extension Type>  -Location <location> -Publisher Microsoft.ManagedIdentity -VMName <vm name> -ResourceGroupName <resource group name> -ForceRerun <Any string different from any last value used>
```

Описание 
- Используется имя расширения и тип для Windows: `ManagedIdentityExtensionForWindows`
- Используется имя расширения и тип для Linux: `ManagedIdentityExtensionForLinux`

#### <a name="automation-script-fails-when-attempting-schema-export-for-managed-identities-for-azure-resources-extension"></a>Скрипт автоматизации завершается ошибкой при попытке экспорта схемы для расширения управляемых удостоверений для ресурсов Azure

При включении управляемого удостоверения для ресурсов Azure на виртуальной машине отображается следующая ошибка, при попытке использовать компонент «Скрипт автоматизации» для виртуальной машины или ее группы ресурсов:

![Ошибка экспорта скрипта автоматизации управляемых удостоверений для ресурсов Azure](./media/howto-migrate-vm-extension/automation-script-export-error.png)

Управляемые удостоверения для расширения виртуальной машины ресурсов Azure в настоящее время не поддерживает возможность экспорта его схемы в шаблон группы ресурсов. Поэтому созданный шаблон не содержит параметры конфигурации для включения управляемых удостоверений для ресурсов Azure в ресурсе. Эти разделы можно добавить вручную, следуя приведенным в [Настройка управляемых удостоверений для ресурсов Azure на виртуальной машине Azure с помощью шаблонов](qs-configure-template-windows-vm.md).

Если функция экспорта схемы станет доступной для управляемых удостоверений для расширения виртуальной машины ресурсов Azure (запланированное устареют в января 2019), оно будет отображаться на [Экспорт групп ресурсов, которые содержат расширения виртуальной Машины ](../../virtual-machines/extensions/export-templates.md#supported-virtual-machine-extensions).

## <a name="limitations-of-the-virtual-machine-extension"></a>Ограничения для расширения виртуальной машины 

Существует несколько основных ограничений с помощью расширения виртуальной машины. 

 * Самый серьезный ограничение заключается в том, что на виртуальной машине хранятся учетные данные, используемые для запроса маркеров. Злоумышленник, успешно нарушений виртуальной машины может вести эксфильтрацию учетные данные. 
 * Кроме того расширение виртуальной машины не по-прежнему поддерживается несколько дистрибутивов Linux, с огромным разработки, затрат на изменение, построение и тестирование расширения на каждом из этих дистрибутивов. В настоящее время поддерживаются только следующие дистрибутивы Linux: 
    * CoreOS Stable
    * CentOS 7.1; 
    * Red Hat 7.2 
    * Ubuntu 15.04. 
    * Ubuntu 16.04.
 * Нет повлиять на производительность для развертывания виртуальных машин с управляемыми удостоверениями как расширение виртуальной машины также должны быть подготовлены. 
 * Наконец расширение виртуальной машины может поддерживать только с 32 назначаемого пользователем управляемого удостоверения на каждую виртуальную машину. 

## <a name="azure-instance-metadata-service"></a>Служба метаданных экземпляров Azure

[Azure службы метаданных экземпляров (IMDS)](/azure/virtual-machines/windows/instance-metadata-service) является конечной точкой REST, который предоставляет сведения о выполнении экземпляров виртуальных машин, которые могут использоваться для управления и настройки виртуальных машин. Конечная точка доступна по хорошо известных немаршрутизируемый IP-адресу (`169.254.169.254`), которые могут быть доступны только из виртуальной машины.

Существует несколько преимуществ использования Azure IMDS для запроса маркеров. 

1. Служба является внешним по отношению к виртуальной машине, поэтому учетные данные, используемые управляемые удостоверения, больше не хранятся на виртуальной машине. Вместо этого они размещаются и защищенным на хост-компьютере Azure виртуальной машины.   
2. Все Windows и Linux операционные системы поддерживаются в Azure IaaS могут использовать управляемые удостоверения.
3. Развертывание осуществляется быстрее и проще, так как расширение виртуальной Машины больше не должно быть подготовлено.
4. С помощью IMDS конечную точку, вплоть до 1000 назначаемого пользователем управляемого удостоверения могут назначаться одной виртуальной машины.
5. Нет значительных изменений в запросы, используя IMDS в отличие от тех, с помощью расширения виртуальной машины, поэтому это довольно проста в порт через существующие развертывания, которые сейчас используют расширение виртуальной машины.

По этим причинам Azure IMDS служба не будет способа де-факто для запроса маркеров после расширения виртуальной машины является устаревшим. 


## <a name="next-steps"></a>Следующие шаги

* [Использование управляемых удостоверений для ресурсов Azure на виртуальной машине Azure для получения маркера доступа](how-to-use-vm-token.md)
* [Служба метаданных экземпляров Azure](https://docs.microsoft.com/azure/virtual-machines/windows/instance-metadata-service)
