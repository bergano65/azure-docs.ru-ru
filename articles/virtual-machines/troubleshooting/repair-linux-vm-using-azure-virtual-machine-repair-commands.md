---
title: Восстановление виртуальной машины Linux с помощью команд восстановления виртуальной машины Azure | Документация Майкрософт
description: В этой статье подробно описано, как с помощью команд исправления виртуальной машины Azure подключить диск к другой виртуальной машине Linux для устранения ошибок, а затем перестроить исходную виртуальную машину.
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
ms.openlocfilehash: da40deb4df55a63f5fecc380500a507b374ca63d
ms.sourcegitcommit: 958f086136f10903c44c92463845b9f3a6a5275f
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 05/20/2020
ms.locfileid: "83711148"
---
# <a name="repair-a-linux-vm-by-using-the-azure-virtual-machine-repair-commands"></a>Восстановление виртуальной машины Linux с помощью команд восстановления виртуальной машины Azure

Если возникает проблема с загрузкой или диском на виртуальной машине Linux в Azure, возможно, вам нужно устранить неполадки, связанные с самим диском. Например, такая ситуация может возникнуть из-за сбоя обновления приложения, который мешает успешно загрузить виртуальную машину. В этой статье подробно описано, как с помощью команд исправления виртуальной машины Azure подключить диск к другой виртуальной машине Linux для устранения ошибок, а затем перестроить исходную виртуальную машину.

> [!IMPORTANT]
> Сценарии в этой статье применяются только к виртуальным машинам, которые используют [Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).

## <a name="repair-process-overview"></a>Обзор процесса исправления

Теперь вы можете использовать команды исправления виртуальной машины Azure, чтобы изменить диск операционной системы для виртуальной машины, и вам больше не нужно удалять и повторно создавать виртуальные машины.

Чтобы устранить проблему с виртуальной машиной, выполните действия ниже.

1. Запуск Azure Cloud Shell
2. Выполните команду az extension add/update.
3. Выполните команду az vm repair create.
4. Выполните действия для устранения проблемы.
5. Выполните команду az vm repair restore.

Дополнительную документацию и инструкции см. в описании [az vm repair](https://docs.microsoft.com/cli/azure/ext/vm-repair/vm/repair).

## <a name="repair-process-example"></a>Пример процесса исправления

1. Запуск Azure Cloud Shell

   Azure Cloud Shell — это бесплатная интерактивная оболочка, с помощью которой можно выполнять действия, описанные в этой статье. Она включает предварительно установленные общие инструменты Azure и настроена для использования с вашей учетной записью.

   Чтобы открыть Cloud Shell, выберите **Попробовать** в правом верхнем углу блока кода. Кроме того, Cloud Shell можно открыть в отдельной вкладке браузера. Для этого перейдите на страницу [https://shell.azure.com](https://shell.azure.com).

   Нажмите кнопку **Копировать**, чтобы скопировать блоки кода. Вставьте код в Cloud Shell и нажмите клавишу **ВВОД**, чтобы выполнить его.

   Если вы решили установить и использовать CLI локально, для выполнения инструкций из этого руководства вам потребуется Azure CLI 2.0.30 или более поздней версии. Чтобы узнать версию, выполните команду ``az --version``. Если вам необходимо установить или обновить Azure CLI, см. статью [Установка Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

2. Если вы используете команды `az vm repair` в первый раз, добавьте расширение CLI vm-repair.

   ```azurecli-interactive
   az extension add -n vm-repair
   ```

   Если вы ранее использовали команды `az vm repair`, примените все обновления к расширению vm-repair.

   ```azurecli-interactive
   az extension update -n vm-repair
   ```

3. Выполните `az vm repair create`. Эта команда создает копию диска ОС для неработающей виртуальной машины, создает виртуальную машину для исправления в новой группе ресурсов и подключает копию диска ОС.  Виртуальная машина для исправления будет иметь тот же размер и регион, что и неработающая виртуальная машина.

   ```azurecli-interactive
   az vm repair create -g MyResourceGroup -n myVM --repair-username username --repair-password password!234 --verbose
   ```

4. Выполните все необходимые действия по устранению проблем на созданной виртуальной машине восстановления, а затем перейдите к шагу 5.

5. Выполните `az vm repair restore`. Эта команда поменяет исходный диск ОС виртуальной машины на исправленный.

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
* Для решения проблем с доступом к приложениям, выполняющимся на виртуальной машине, см. статью [Устранение проблем с подключением к приложениям на виртуальных машинах в Azure](https://docs.microsoft.com/azure/virtual-machines/troubleshooting/troubleshoot-app-connection).
* Дополнительные сведения об использовании Resource Manager вы найдете в статье [Общие сведения об Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview).
