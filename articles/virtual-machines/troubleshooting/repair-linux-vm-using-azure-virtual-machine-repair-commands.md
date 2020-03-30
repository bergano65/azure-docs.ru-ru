---
title: Ремонт Linux VM с помощью команд по ремонту виртуальной машины Azure (ru) Документы Майкрософт
description: В этой статье подробно описано, как использовать команды по ремонту виртуальной машины Azure для подключения диска к другому Linux VM для исправления любых ошибок, а затем восстановить исходный VM.
services: virtual-machines-linux
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 49fdfde402938ce8d0ee1b141a47e68c99c502e7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "73796204"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Восстановление виртуальной машины Linux с помощью команд восстановления виртуальной машины Azure

Если виртуальная машина Linux (VM) в Azure сталкивается с ошибкой загрузки или диска, возможно, потребуется выполнить смягчение на самом диске. Например, такая ситуация может возникнуть из-за сбоя обновления приложения, который мешает успешно загрузить виртуальную машину. В этой статье подробно описано, как использовать команды по ремонту виртуальной машины Azure для подключения диска к другому Linux VM для исправления любых ошибок, а затем восстановить исходный VM.

> [!IMPORTANT]
> Скрипты в этой статье применяются только к mMs, которые используют [менеджер ресурсов Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="repair-process-overview"></a>Обзор процесса ремонта

Теперь вы можете использовать команды по ремонту виртуальной машины Azure для изменения диска ОС на VM, и вам больше не нужно удалять и воссоздавать VM.

Выполните следующие действия, чтобы устранить проблему VM:

1. Запуск Azure Cloud Shell
2. Выполнить добавление/обновление расширения az
3. Выполнить az vm ремонт создать
4. Выполнение мер по смягчению последствий
5. Выполнить az vm восстановление восстановления

Для получения дополнительной документации и инструкций, [см az vm ремонт](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Пример процесса ремонта

1. Запуск Azure Cloud Shell

   Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Он включает в себя общие инструменты Azure, предустановленные и настроенные для использования с вашей учетной записью.

   Чтобы открыть облачную оболочку, выберите **попытайтесь** из верхнего правого угла блока кода. Вы также можете открыть Cloud Shell в [https://shell.azure.com](https://shell.azure.com)отдельной вкладке браузера, перейдя к .

   Выберите **Copy,** чтобы скопировать блоки кода, затем вставьте код в облачную оболочку и выберите **Enter** для его запуска.

   Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду ``az --version``. Если вам необходимо установить или обновить ClI Azure, [см.](https://docs.microsoft.com/cli/azure/install-azure-cli)

2. Если это первый раз, `az vm repair` когда вы использовали команды, добавьте расширение vm-repair CLI.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Если вы ранее `az vm repair` использовали команды, примените все обновления к расширению Vm-ремонта.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Выполните `az vm repair create`. Эта команда создаст копию диска ОС для нефункционального VM, создаст ремонт VM и прикрепит диск.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Выполните все необходимые шаги по смягчению на созданном ремонте VM, а затем приступайте к шагу 5.

5. Выполните `az vm repair restore`. Эта команда будет поменять отремонтированный диск ОС с исходным диском ОС VM.

   ```azurecli-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Проверка и включение диагностики загрузки

В следующем примере на виртуальной машине ``myVMDeployed`` в группе ресурсов ``myResourceGroup`` включается расширение диагностики:

Azure CLI

```azurecli-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Дальнейшие действия

* Если у вас возникли проблемы с [Troubleshoot RDP connections to an Azure Virtual Machine](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection)подключением к VM, см.
* Для проблем с доступом к приложениям, работающим на VM, см. [проблемы подключения приложений Troubleshoot на виртуальных машинах в Azure.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)
* Дополнительные сведения об использовании Resource Manager вы найдете в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
