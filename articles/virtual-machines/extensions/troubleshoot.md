---
title: Устранение ошибок расширения виртуальной машины Windows
description: Узнайте об устранении неполадок в работе расширений виртуальной машины Windows в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: ad3197f20428ec751b4e3520af72dc5f8eb9ad28
ms.sourcegitcommit: 3fb5e772f8f4068cc6d91d9cde253065a7f265d6
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 08/31/2020
ms.locfileid: "89180361"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Troubleshooting Azure Windows VM extension failures
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Просмотр состояния расширения
Шаблоны Azure Resource Manager можно выполнять из Azure PowerShell. После выполнения шаблона состояние расширения можно узнать в обозревателе ресурсов Azure или с помощью программ командной строки.

Пример:

Azure PowerShell:

```azurepowershell
Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status
```

Пример выходных данных:

```output
Extensions:  {
  "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
  "Name": "myCustomScriptExtension",
  "SubStatuses": [
    {
      "Code": "ComponentStatus/StdOut/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
          \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
          test.txt                          \\n\\n",
                  "Time": null
      },
    {
      "Code": "ComponentStatus/StdErr/succeeded",
      "DisplayStatus": "Provisioning succeeded",
      "Level": "Info",
      "Message": "",
      "Time": null
    }
  ]
}
```

## <a name="troubleshooting-extension-failures"></a>Устранение неполадок расширений

### <a name="rerun-the-extension-on-the-vm"></a>Повторное выполнение расширения на виртуальной машине
При выполнении сценариев на виртуальной машине с помощью расширения пользовательских сценариев может возникать ошибка, указывающая на то, что виртуальная машина создана успешно, но сценарий не выполнен. В этом случае рекомендуется удалить соответствующее расширение и выполнить шаблон еще раз.
Примечание. В будущем эта функция будет усовершенствована, что позволит устранить необходимость в удалении расширения.

#### <a name="remove-the-extension-from-azure-powershell"></a>Удаление расширения с помощью Azure PowerShell
```azurepowershell
Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"
```

После удаления расширения шаблон можно выполнить повторно, чтобы запустить скрипты на виртуальной машине.

### <a name="trigger-a-new-goalstate-to-the-vm"></a>Активация нового Гоалстате для виртуальной машины
Возможно, вы заметили, что расширение не было выполнено или не удается выполнить из-за отсутствия "генератора сертификатов Windows Azure CRP" (этот сертификат используется для защиты транспорта защищенных параметров расширения).
Этот сертификат будет автоматически создан повторно при перезапуске гостевого агента Windows из виртуальной машины:
- Открытие диспетчера задач
- Перейдите на вкладку "сведения".
- Определение WindowsAzureGuestAgent.exe процесса
- Щелкните правой кнопкой мыши и выберите пункт "завершить задачу". Процесс будет автоматически перезапущен


Вы также можете активировать новый Гоалстате для виртуальной машины, выполнив "пустое обновление":

Azure PowerShell:

```azurepowershell
$vm = Get-AzureRMVM -ResourceGroupName <RGName> -Name <VMName>  
Update-AzureRmVM -ResourceGroupName <RGName> -VM $vm  
```

Azure CLI:

```azurecli
az vm update -g <rgname> -n <vmname>
```

Если "пустое обновление" не работает, можно добавить новый пустой диск данных в виртуальную машину из портал управления Azure, а затем удалить его позже после того, как сертификат будет добавлен обратно.
