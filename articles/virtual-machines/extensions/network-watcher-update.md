---
title: Обновление расширения наблюдателя за сетями до последней версии
description: Узнайте, как обновить расширение наблюдателя за сетями Azure до последней версии.
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
ms.openlocfilehash: e367c348364d03cec6914c99e7ff112803fc58f6
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132437"
---
# <a name="update-the-network-watcher-extension-to-the-latest-version"></a>Обновление расширения наблюдателя за сетями до последней версии

## <a name="overview"></a>Обзор

[Наблюдатель за сетями Azure](../../network-watcher/network-watcher-monitoring-overview.md) — это служба наблюдения за производительностью сети, диагностики и аналитики, которая наблюдает за сетями Azure. Расширение виртуальной машины агента наблюдателя за сетями — это требование для записи сетевого трафика по требованию и использования других расширенных функций на виртуальных машинах Azure. Расширение наблюдателя за сетями используется такими функциями, как монитор подключения, монитор подключения (Предварительная версия), устранение неполадок подключения и запись пакетов.

## <a name="prerequisites"></a>Предварительные требования

В этой статье предполагается, что на виртуальной машине установлено расширение наблюдателя за сетями.

## <a name="latest-version"></a>Последняя версия

В настоящее время доступна последняя версия расширения наблюдателя за сетями `1.4.1654.1` .

## <a name="update-your-extension"></a>Обновление расширения

Чтобы обновить расширение, необходимо знать версию расширения.

### <a name="check-your-extension-version"></a>Проверка версии расширения

Версию расширения можно проверить с помощью портал Azure, Azure CLI или PowerShell.

#### <a name="usetheazureportal"></a>Использование портал Azure

1. Перейдите в область **расширения** виртуальной машины на портал Azure.
1. Выберите расширение **азуренетворкватчер** , чтобы открыть область сведений.
1. В поле **Version (версия** ) щелкните номер версии.  

#### <a name="use-the-azure-cli"></a>Использование командной строки Azure CLI

Выполните следующую команду в командной строке Azure CLI:

```azurecli
az vm extension list --resource-group  <ResourceGroupName> --vm-name <VMName>
```

В выходных данных откройте расширение Азуренетворкватчер. Найдите номер версии в поле "TypeHandlerVersion" в выходных данных.  

#### <a name="usepowershell"></a>Использование PowerShell

Выполните следующие команды в командной строке PowerShell:

```powershell
Get-AzVMExtension -ResourceGroupName <ResourceGroupName> -VMName <VMName>  
```

В выходных данных откройте расширение Азуренетворкватчер. Найдите номер версии в поле "TypeHandlerVersion" в выходных данных.

### <a name="update-your-extension"></a>Обновление расширения

Если версия более ранняя, чем `1.4.1654.1` Текущая последняя версия, обновите расширение с помощью любого из следующих параметров.

#### <a name="option-1-use-powershell"></a>Вариант 1. Использование PowerShell

Выполните следующие команды:

```powershell
#Linux command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentLinux"   

#Windows command
Set-AzVMExtension `  -ResourceGroupName "myResourceGroup1" `  -Location "WestUS" `  -VMName "myVM1" `  -Name "AzureNetworkWatcherExtension" `  -Publisher "Microsoft.Azure.NetworkWatcher" -Type "NetworkWatcherAgentWindows"   
```

#### <a name="option-2-use-the-azure-cli"></a>Вариант 2. Использование Azure CLI

Принудительное обновление.

```azurecli
#Linux command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher" --force-update

#Windows command
az vm extension set --resource-group "myResourceGroup1" --vm-name "myVM1" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" --force-update
```

Если это не сработает, удалите и снова установите расширение и выполните следующие действия, чтобы автоматически добавить последнюю версию.

Удалите расширение.

```azurecli
#Same for Linux and Windows
az vm extension delete --resource-group "myResourceGroup1" --vm-name "myVM1" -n "AzureNetworkWatcherExtension"

```

Снова установите расширение.

```azurecli
#Linux command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentLinux" --publisher "Microsoft.Azure.NetworkWatcher"  

#Windows command
az vm extension set --resource-group "DALANDEMO" --vm-name "Linux-01" --name "NetworkWatcherAgentWindows" --publisher "Microsoft.Azure.NetworkWatcher" 

```

#### <a name="option-3-reboot-your-vms"></a>Вариант 3. Перезагрузите виртуальные машины

Если автоматическое обновление установлено в true для расширения наблюдателя за сетями, перезагрузите установку виртуальной машины на последнее расширение.

## <a name="support"></a>Поддержка

Если вам нужна дополнительная помощь в любой момент в этой статье, см. документацию по расширению наблюдателя за сетями для [Linux](./network-watcher-linux.md) или [Windows](./network-watcher-windows.md). Вы также можете обратиться к экспертам Azure на [форумах MSDN Azure и Stack overflow](https://azure.microsoft.com/support/forums/). Кроме того, можно зафайлировать обращение в службу поддержки Azure. Перейдите на [сайт поддержки Azure](https://azure.microsoft.com/support/options/)и выберите **получить поддержку**. Дополнительные сведения об использовании службы поддержки Azure см. в статье [Часто задаваемые вопросы о поддержке Microsoft Azure](https://azure.microsoft.com/support/faq/).