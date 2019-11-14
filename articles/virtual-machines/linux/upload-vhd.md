---
title: Отправка или копирование пользовательской виртуальной машины Linux с Azure CLI
description: Отправка или копирование пользовательской виртуальной машины с использованием модели развертывания Resource Manager и Azure CLI
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.assetid: a8c7818f-eb65-409e-aa91-ce5ae975c564
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/10/2019
ms.author: cynthn
ms.openlocfilehash: 95486208f52b2faa2fbb3db5bf1ef968c330dab6
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 11/13/2019
ms.locfileid: "74034301"
---
# <a name="create-a-linux-vm-from-a-custom-disk-with-the-azure-cli"></a>Создание виртуальной машины Linux на основе пользовательского диска с помощью Azure CLI

<!-- rename to create-vm-specialized -->

В этой статье описывается, как отправить пользовательский виртуальный жесткий диск и скопировать существующий виртуальный жесткий диск в Azure. После этого созданный виртуальный жесткий диск используется для создания новых виртуальных машин Linux. Вы можете установить и настроить дистрибутив Linux в соответствии с требованиями, а затем использовать этот виртуальный жесткий диск для создания виртуальной машины Azure.

Чтобы создать несколько виртуальных машин на основе пользовательского диска, сначала создайте образ на основе виртуальной машины или виртуальный жесткий диск. Дополнительные сведения см. в статье [Создание пользовательского образа виртуальной машины Azure с помощью интерфейса командной строки](tutorial-custom-images.md).

Создать пользовательский диск можно двумя способами:
* Отправка VHD
* Копирование имеющейся виртуальной машины Azure.


## <a name="requirements"></a>Требования
Чтобы выполнить приведенные ниже действия, требуется следующее:

- Виртуальная машина Linux, которая была подготовлена для использования в Azure. Раздел [Подготовка виртуальной машины](#prepare-the-vm) этой статьи содержит сведения о том, как найти информацию для конкретного дистрибутива касательно установки агента Linux для Azure, необходимого для подключения к виртуальной машине по протоколу SSH.
- VHD-файл на основе имеющегося [рекомендуемого для Azure дистрибутива Linux](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) (или см. [сведения о нерекомендованных дистрибутивах](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)) на виртуальном диске в формате VHD. Для создания VHD-файлов существует несколько средств.
  - Установите и настройте [QEMU](https://en.wikibooks.org/wiki/QEMU/Installing_QEMU) или [KVM](https://www.linux-kvm.org/page/RunningKVM), используя VHD в качестве формата образа. При необходимости вы можете [преобразовать образ](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) с помощью `qemu-img convert`.
  - Кроме того, можно использовать Hyper-V [в Windows 10](https://msdn.microsoft.com/virtualization/hyperv_on_windows/quick_start/walkthrough_install) или [Windows Server 2012 и 2012 R2](https://technet.microsoft.com/library/hh846766.aspx).

> [!NOTE]
> Более новый формат VHDX не поддерживается в Azure. При создании виртуальной машины укажите формат VHD. При необходимости можно преобразовать диски VHDX в диски VHD с помощью команды [qemu-img convert](https://en.wikibooks.org/wiki/QEMU/Images#Converting_image_formats) или командлета PowerShell [Convert-VHD](https://technet.microsoft.com/library/hh848454.aspx). Azure не поддерживает отправку динамических дисков VHD, поэтому перед отправкой необходимо преобразовать такие диски в статические диски VHD. Для преобразования динамических дисков во время передачи в Azure можно использовать [служебные программы Azure VHD для GO](https://github.com/Microsoft/azure-vhd-utils-for-go).
> 
> 


- Обязательно установите последнюю версию [Azure CLI](/cli/azure/install-az-cli2) и войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az-login).

В приведенных ниже примерах замените в качестве примеров имен параметров собственные значения, такие как `myResourceGroup`, `mystorageaccount`и `mydisks`.

<a id="prepimage"> </a>

## <a name="prepare-the-vm"></a>Подготовка виртуальной машины

Azure поддерживает различные дистрибутивы Linux (см. раздел [Рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)). В следующих статьях описывается подготовка различных дистрибутивов Linux, которые поддерживаются в Azure:

* [Подготовка виртуальной машины на основе CentOS для Azure](create-upload-centos.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуального жесткого диска Debian для Azure](debian-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Oracle Linux](oracle-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуальной машины на основе Red Hat для Azure](redhat-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Подготовка виртуальной машины SLES или openSUSE для Azure](suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Ubuntu](create-upload-ubuntu.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Информация о нерекомендованных дистрибутивах](create-upload-generic.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

Другие общие советы по подготовке образов Linux для Azure см. в разделе [Общие замечания по установке Linux](create-upload-generic.md#general-linux-installation-notes).

> [!NOTE]
> [Соглашение об уровне обслуживания платформы Azure](https://azure.microsoft.com/support/legal/sla/virtual-machines/) применяется к виртуальным машинам, работающим под управлением Linux, только в том случае, если используется один из рекомендуемых дистрибутивов из раздела "Поддерживаемые версии" статьи [Linux в Azure — рекомендованные дистрибутивы](endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) с заданными параметрами конфигурации.
> 
> 

## <a name="option-1-upload-a-vhd"></a>Вариант 1. Передача VHD

Теперь виртуальный жесткий диск можно передать прямо в управляемый диск. Инструкции см. в статье [Отправка виртуального жесткого диска в Azure с помощью Azure CLI](disks-upload-vhd-to-managed-disk-cli.md).

## <a name="option-2-copy-an-existing-vm"></a>Вариант 2. Копирование имеющейся виртуальной машины

Вы также можете создать настраиваемую виртуальную машину в Azure, а затем скопировать диск операционной системы и подключить его к новой виртуальной машине для создания другой копии. Это нормально для тестирования, однако если вы хотите использовать имеющуюся виртуальную машину Azure в качестве модели для нескольких новых виртуальных машин, вместе этого вам необходимо будет создать *образ*. Дополнительные сведения о создании образа на основе имеющейся виртуальной машины Azure см. в статье [Создание пользовательского образа виртуальной машины Azure с помощью интерфейса командной строки](tutorial-custom-images.md).

Если вы хотите скопировать существующую виртуальную машину в другой регион, вы можете использовать azcopy, чтобы [создать копию диска в другом регионе](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk). 

В противном случае следует сделать снимок виртуальной машины, а затем создать новый виртуальный жесткий диск операционной системы из моментального снимка.

### <a name="create-a-snapshot"></a>Создание моментального снимка

В этом примере создается моментальный снимок виртуальной машины с именем *myVM* в группе ресурсов *myResourceGroup*, а также моментальный снимок с именем *osDiskSnapshot*.

```azure-cli
osDiskId=$(az vm show -g myResourceGroup -n myVM --query "storageProfile.osDisk.managedDisk.id" -o tsv)
az snapshot create \
    -g myResourceGroup \
    --source "$osDiskId" \
    --name osDiskSnapshot
```
###  <a name="create-the-managed-disk"></a>Создание управляемого диска

Создайте управляемый диск из моментального снимка.

Получите идентификатор моментального снимка. В этом примере используется моментальный снимок с именем *osDiskSnapshot*, который находится в группе ресурсов *myResourceGroup*.

```azure-cli
snapshotId=$(az snapshot show --name osDiskSnapshot --resource-group myResourceGroup --query [id] -o tsv)
```

Создайте управляемый диск. В этом примере мы создадим управляемый диск с именем *myManagedDisk* на основе моментального снимка размером в 128 ГБ, который хранится в хранилище уровня "Стандартный".

```azure-cli
az disk create \
    --resource-group myResourceGroup \
    --name myManagedDisk \
    --sku Standard_LRS \
    --size-gb 128 \
    --source $snapshotId
```

## <a name="create-the-vm"></a>Создание виртуальной машины

Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm#az-vm-create), и подключите (--attach-os-disk) управляемый диск как диск операционной системы. В следующем примере создается виртуальная машина с именем *myNewVM* на основе управляемого диска, созданного из отправленного ранее виртуального жесткого диска:

```azurecli
az vm create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNewVM \
    --os-type linux \
    --attach-os-disk myManagedDisk
```

Вы должны иметь возможность подключиться к виртуальной машине по протоколу SSH, используя учетные данные исходной виртуальной машины. 

## <a name="next-steps"></a>Дополнительная информация
После подготовки и передачи пользовательского виртуального диска ознакомьтесь с дополнительными сведениями об [использовании Resource Manager и шаблонов](../../azure-resource-manager/resource-group-overview.md). Возможно, вам также потребуется [добавить диск данных](add-disk.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) для новых виртуальных машин. Если на виртуальных машинах запущены приложения, к которым необходим доступ, [откройте порты и конечные точки](nsg-quickstart.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
