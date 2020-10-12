---
title: Обновите расширение наблюдателя за сетями до последней версии.
description: Узнайте, как обновить расширение наблюдателя за сетями до последней версии.
services: virtual-machines-windows
documentationcenter: ''
author: damendo
manager: balar
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 09/23/2020
ms.author: damendo
ms.openlocfilehash: c386685d63894472623ffc4392a529541a91391c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91410362"
---
# <a name="how-to-update-the-network-watcher-extension-to-the-latest-the-version"></a>Обновление расширения наблюдателя за сетями до последней версии 

## <a name="overview"></a>Обзор

[Наблюдатель за сетями Azure](../../network-watcher/network-watcher-monitoring-overview.md) — это служба мониторинга производительности, диагностики и анализа сети, которая позволяет наблюдать за сетями Azure. Расширение виртуальной машины для агента службы "Наблюдатель за сетями" необходимо для записи трафика по требованию и других дополнительных функций виртуальных машин Azure. Расширение наблюдателя за сетями используется такими компонентами, как монитор подключения, монитор подключения (Предварительная версия), устранение неполадок подключения и запись пакетов.   

## <a name="prerequisites"></a>Предварительные требования
В этом документе предполагается, что на виртуальной машине установлено расширение наблюдателя за сетями, а также инструкции по его обновлению до последней версии. 

## <a name="latest-version"></a>Последняя версия
В настоящее время доступна последняя версия расширения наблюдателя за сетями `1.4.1654.1` .

## <a name="updating-your-extension"></a>Обновление расширения 

### <a name="check-your-extension-version"></a>Проверка версии расширения  

**Использование портал Azure**

1. Перейдите в колонку "расширения" виртуальной машины на портал Azure.   
2. Щелкните расширение "Азуренетворкватчер", чтобы открыть область сведений.  
3. Определение номера версии в поле "версия".  

**Использование Azure CLI** Выполните приведенную ниже команду из Azure CLI командной строки.   

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

Найдите расширение Азуренетворкватчер в выходных данных и выясните номер версии из поля "TypeHandlerVersion" в выходных данных.  


**Использование PowerShell** Выполните следующие команды в командной строке PowerShell:   

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

Найдите расширение Азуренетворкватчер в выходных данных и выясните номер версии из поля "TypeHandlerVersion" в выходных данных.   


### <a name="update-your-extension"></a>Обновление расширения

Если версия меньше `1.4.1654.1` (Текущая последняя версия), обновите расширение, используя любой из следующих вариантов. 

**Вариант 1. Использование PowerShell**

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```


**Вариант 2. Использование Azure CLI**  

Принудительное обновление 

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Если это не сработает. Удалите и снова установите расширение, выполнив приведенные ниже действия. При этом будет автоматически добавлена последняя версия. 

Удаление расширения 

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Повторная установка расширения

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

**Вариант 3. Перезагрузите виртуальные машины**

Если для расширения NetworkWatcher автоматическое обновление установлено в true. При перезагрузке виртуальной машины устанавливается Последнее расширение.


## <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в любой момент в этой статье, см. документацию по расширениям наблюдателя за сетями ([Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-linux), [Windows](https://docs.microsoft.com/azure/virtual-machines/extensions/network-watcher-windows)) или обратитесь к экспертам Azure на [форумах MSDN Azure и Stack overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зарегистрировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/) и щелкните "Получить поддержку". Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).
