---
title: Восстановление виртуальной машины Windows с помощью команд восстановления виртуальной машины Azure | Документация Майкрософт
description: В этой статье подробно описано, как с помощью команд восстановления ВИРТУАЛЬНОЙ машины Azure подключить диск к другой виртуальной машине Windows для устранения ошибок, а затем перестроить исходную виртуальную машину.
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
ms.openlocfilehash: 6bda8cb831e84a56c889ed40109954551a34c113
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73796175"
---
# <a name="repair-a-windows-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Восстановление виртуальной машины Windows с помощью команд восстановления виртуальной машины Azure

Если виртуальная машина Windows в Azure встречает ошибку загрузки или диска, может потребоваться выполнить устранение проблем на самом диске. Например, такая ситуация может возникнуть из-за сбоя обновления приложения, который мешает успешно загрузить виртуальную машину. В этой статье подробно описано, как с помощью команд восстановления ВИРТУАЛЬНОЙ машины Azure подключить диск к другой виртуальной машине Windows для устранения ошибок, а затем перестроить исходную виртуальную машину.

> [!IMPORTANT]
> Скрипты в этой статье применимы только к виртуальным машинам, использующим [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Обзор процесса исправления

Теперь вы можете использовать команды восстановления ВМ Azure, чтобы изменить диск операционной системы для виртуальной машины, и вам больше не нужно удалять и повторно создавать виртуальные машины.

Чтобы устранить неполадки с виртуальной машиной, выполните следующие действия.

1. Запуск Azure Cloud Shell
2. Выполните команду AZ расширение Добавить/обновить.
3. Выполните команду AZ VM Repair Create.
4. Выполните команду AZ VM Repair Run.
5. Выполните команду AZ VM Repair Restore.

Дополнительную документацию и инструкции см. в разделе [AZ VM Repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Пример процесса восстановления

> [!NOTE]
> * Для выполнения скрипта требуется исходящее подключение из виртуальной машины (порт 443).
> * В каждый момент времени может выполняться только один скрипт.
> * Выполнение скрипта не может быть отменено.
> * Максимальное время выполнения скрипта составляет 90 минут, после чего оно истекает время ожидания.

1. Запуск Azure Cloud Shell

   Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Он включает общие средства Azure, предварительно установленные и настроенные для использования с вашей учетной записью.

   Чтобы открыть Cloud Shell, выберите **проверить** в правом верхнем углу блока кода. Можно также открыть Cloud Shell на отдельной вкладке браузера, посетив [https://shell.azure.com](https://shell.azure.com).

   Выберите **Копировать** , чтобы скопировать блоки кода, вставьте код в Cloud Shell и нажмите клавишу **Ввод** , чтобы запустить его.

   Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду ``az --version``. Если вам нужно установить или обновить Azure CLI, см. раздел [install Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Если вы используете `az vm repair` команды в первый раз, добавьте расширение CLI для восстановления виртуальной машины.

   ```azurepowershell-interactive
   az extension add -n vm-repair
   ```

   Если вы ранее использовали `az vm repair` команды, примените все обновления к расширению восстановления виртуальной машины.

   ```azurepowershell-interactive
   az extension update -n vm-repair
   ```

3. Запустите `az vm repair create`. Эта команда создает копию диска ОС для нефункциональной виртуальной машины, создает виртуальную машину для восстановления и подключает диск.

   ```azurepowershell-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Запустите `az vm repair run`. Эта команда запустит указанный сценарий восстановления на подключенном диске с помощью виртуальной машины восстановления.

   ```azurepowershell-interactive
   az vm repair run  –g MyResourceGroup –n MyVM -–run-on-repair --run-id 2 --verbose
   ```

5. Запустите `az vm repair restore`. Эта команда переставит исправленный диск ОС на исходный диск ОС виртуальной машины.

   ```azurepowershell-interactive
   az vm repair restore -g MyResourceGroup -n MyVM --verbose
   ```

## <a name="verify-and-enable-boot-diagnostics"></a>Проверка и включение диагностики загрузки

В следующем примере на виртуальной машине ``myVMDeployed`` в группе ресурсов ``myResourceGroup`` включается расширение диагностики:

Azure CLI

```azurepowershell-interactive
az vm boot-diagnostics enable --name myVMDeployed --resource-group myResourceGroup --storage https://mystor.blob.core.windows.net/
```

## <a name="next-steps"></a>Дальнейшие действия

* При возникновении проблем с подключением к виртуальной машине ознакомьтесь со статьей [Устранение неполадок с подключением к удаленному рабочему столу на виртуальной машине Azure под управлением Windows](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-rdp-connection).
* Сведения о проблемах доступа к приложениям, выполняемым на виртуальной машине, см. [в статье Устранение неполадок с подключением к приложениям на виртуальных машинах в Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Дополнительные сведения об использовании Resource Manager вы найдете в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
