---
title: Ремонт Windows VM с помощью команд по ремонту виртуальной машины Azure (ru) Документы Майкрософт
description: В этой статье подробно описано, как использовать команды по ремонту Azure VM для подключения диска к другому Windows VM для исправления любых ошибок, а затем восстановить исходный VM.
services: virtual-machines-windows
documentationcenter: ''
author: v-miegge
manager: dcscontentpm
editor: ''
tags: virtual-machines
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 09/10/2019
ms.author: v-miegge
ms.openlocfilehash: 2055558ef80a641084a7cf9d299281497d282936
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060675"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Восстановление виртуальной машины Windows с помощью команд восстановления виртуальной машины Azure

Если виртуальная машина Windows (VM) в Azure сталкивается с ошибкой загрузки или диска, возможно, потребуется выполнить смягчение на самом диске. Например, такая ситуация может возникнуть из-за сбоя обновления приложения, который мешает успешно загрузить виртуальную машину. В этой статье подробно описано, как использовать команды по ремонту Azure VM для подключения диска к другому Windows VM для исправления любых ошибок, а затем восстановить исходный VM.

> [!IMPORTANT]
> Скрипты в этой статье применяются только к mMs, которые используют [менеджер ресурсов Azure.](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview)

## <a name="repair-process-overview"></a>Обзор процесса ремонта

Теперь вы можете использовать команды по ремонту Azure VM для изменения диска ОС на VM, и вам больше не нужно удалять и воссоздавать VM.

Выполните следующие действия, чтобы устранить проблему VM:

1. Запуск Azure Cloud Shell
2. Выполнить добавление/обновление расширения az.
3. Выполнить az vm ремонт создать.
4. Выполнить az vm ремонт перспективе.
5. Выполнить az vm восстановление ремонта.

Для получения дополнительной документации и инструкций, [см az vm ремонт](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Пример процесса ремонта

> [!NOTE]
> * Для выполнения сценария требуется исходящие подключения из VM (порт 443).
> * Только один скрипт может работать одновременно.
> * Запуск сценария не может быть отменен.
> * Максимальное время выполнения скрипта составляет 90 минут, после чего он будет тайм-аут.

1. Запуск Azure Cloud Shell

   Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Он включает в себя общие инструменты Azure, предустановленные и настроенные для использования с вашей учетной записью.

   Чтобы открыть облачную оболочку, выберите **попытайтесь** из верхнего правого угла блока кода. Вы также можете открыть Cloud Shell в [https://shell.azure.com](https://shell.azure.com)отдельной вкладке браузера, посетив .

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

4. Выполните `az vm repair run`. Эта команда будет работать указанный сценарий ремонта на прикрепленном диске через ремонт VM.

   ```azurecli-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

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

* При возникновении проблем с подключением к виртуальной машине ознакомьтесь со статьей [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Для проблем с доступом к приложениям, работающим на VM, см. [проблемы подключения приложений Troubleshoot на виртуальных машинах в Azure.](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection)
* Дополнительные сведения об использовании Resource Manager вы найдете в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
