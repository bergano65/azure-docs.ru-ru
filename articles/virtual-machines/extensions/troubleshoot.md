---
title: Устранение неполадок расширений виртуальной машины Windows | Документация Майкрософт
description: Узнайте об устранении неполадок в работе расширений виртуальной машины Windows в Azure.
services: virtual-machines-windows
documentationcenter: ''
author: kundanap
manager: gwallace
editor: ''
tags: top-support-issue,azure-resource-manager
ms.assetid: 878ab9b6-c3e6-40be-82d4-d77fecd5030f
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/29/2016
ms.author: kundanap
ms.openlocfilehash: f2b85e9a156d0e6264ec39282b803118963cbbbb
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67706660"
---
# <a name="troubleshooting-azure-windows-vm-extension-failures"></a>Troubleshooting Azure Windows VM extension failures
[!INCLUDE [virtual-machines-common-extensions-troubleshoot](../../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## <a name="viewing-extension-status"></a>Просмотр состояния расширения
Шаблоны Azure Resource Manager можно выполнять из Azure PowerShell. После выполнения шаблона состояние расширения можно узнать в обозревателе ресурсов Azure или с помощью программ командной строки.

Вот пример:

Azure PowerShell.

      Get-AzVM -ResourceGroupName $RGName -Name $vmName -Status

Пример выходных данных:

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
    }
  ]

## <a name="troubleshooting-extension-failures"></a>Устранение неполадок расширений
### <a name="rerun-the-extension-on-the-vm"></a>Повторное выполнение расширения на виртуальной машине
При выполнении сценариев на виртуальной машине с помощью расширения пользовательских сценариев может возникать ошибка, указывающая на то, что виртуальная машина создана успешно, но сценарий не выполнен. В этом случае рекомендуется удалить соответствующее расширение и выполнить шаблон еще раз.
Примечание. В будущем эта функция будет усовершенствована, что позволит устранить необходимость в удалении расширения.

#### <a name="remove-the-extension-from-azure-powershell"></a>Удаление расширения с помощью Azure PowerShell
    Remove-AzVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

После удаления расширения шаблон можно выполнить повторно, чтобы запустить скрипты на виртуальной машине.

