---
title: Копирование виртуальной машины Linux с помощью Azure CLI | Документы Майкрософт
description: Сведения о том, как создать копию виртуальной машины Linux в Azure с помощью Azure CLI и Управляемых дисков.
services: virtual-machines-linux
documentationcenter: ''
author: cynthn
manager: jeconnoc
tags: azure-resource-manager
ms.assetid: 770569d2-23c1-4a5b-801e-cddcd1375164
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 10/17/2018
ms.author: cynthn
ms.openlocfilehash: abc8c09a51104c81b827afb7055531df98691714
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56731350"
---
# <a name="create-a-copy-of-a-linux-vm-by-using-azure-cli-and-managed-disks"></a>Создание копии виртуальной машины Linux в Azure с помощью Azure CLI и Управляемых дисков

В этой статье показано, как создать копию виртуальной машины Azure под управлением Linux, используя Azure CLI и модель развертывания с помощью Azure Resource Manager. 

Кроме того, можно [передать VHD и создать виртуальную машину на его основе](upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="prerequisites"></a>Технические условия

-   Установка [Azure CLI](/cli/azure/install-az-cli2).

-   Войдите в учетную запись Azure с помощью команды [az login](/cli/azure/reference-index#az-login).

-   Выберите виртуальную машину Azure, которая послужит источником копии.

## <a name="stop-the-source-vm"></a>Остановка исходной виртуальной машины

Отмените выделение исходной виртуальной машины, выполнив команду [az vm deallocate](/cli/azure/vm#az-vm-deallocate).
В следующем примере отменяется распределение виртуальной машины *myVM*, входящей в группу ресурсов *myResourceGroup*.

```azurecli
az vm deallocate \
    --resource-group myResourceGroup \
    --name myVM
```

## <a name="copy-the-source-vm"></a>Копирование исходной виртуальной машины

Чтобы скопировать виртуальную машину, необходимо создать копию ее виртуального жесткого диска. Этот процесс позволяет создать специализированный виртуальный жесткий диск (VHD) в качестве управляемого диска, содержащего такие же конфигурацию и параметры, как у исходной виртуальной машины.

Дополнительные сведения об Управляемых дисках Azure см. в [обзоре Управляемых дисков Azure](../windows/managed-disks-overview.md). 

1.  Получите список виртуальных машин и имен их дисков ОС, выполнив команду [az vm list](/cli/azure/vm#az-vm-list). В следующем примере создается список виртуальных машин, входящих в группу ресурсов *myResourceGroup*.
    
    ```azurecli
    az vm list -g myResourceGroup \
         --query '[].{Name:name,DiskName:storageProfile.osDisk.name}' \
         --output table
    ```

    Вы должны увидеть результат, аналогичный приведенному ниже.

    ```azurecli
    Name    DiskName
    ------  --------
    myVM    myDisk
    ```

1.  Скопируйте диск, создав управляемый диск и используя команду [az disk create](/cli/azure/disk#az-disk-create). В следующем примере создается диск *myCopiedDisk* на основе управляемого диска *myDisk*.

    ```azurecli
    az disk create --resource-group myResourceGroup \
         --name myCopiedDisk --source myDisk
    ``` 

1.  Убедитесь, что этот управляемый диск теперь входит в нужную группу ресурсов, выполнив команду [az disk list](/cli/azure/disk#az-disk-list). В следующем примере выводится список управляемых дисков, входящих в группу ресурсов *myResourceGroup*.

    ```azurecli
    az disk list --resource-group myResourceGroup --output table
    ```


## <a name="set-up-a-virtual-network"></a>Настройка виртуальной сети

Следующие необязательные шаги позволяют создать виртуальную сеть, подсеть, общедоступный IP-адрес и виртуальную сетевую карту.

Если копирование виртуальной машины выполняется для устранения неполадок или для создания дополнительных развертываний, вам может потребоваться создать виртуальную машину в новой виртуальной сети.

Если для копируемых виртуальных машин нужна отдельная инфраструктура виртуальный сети, выполните следующие шаги. Если вам не нужно создавать виртуальную сеть, перейдите к разделу [Создание виртуальной машины](#create-a-vm).

1.  Создайте виртуальную сеть, выполнив команду [az network vnet create](/cli/azure/network/vnet#az-network-vnet-create). В следующем примере создаются виртуальная сеть *myVnet* и подсеть *mySubnet*.

    ```azurecli
    az network vnet create --resource-group myResourceGroup \
        --location eastus --name myVnet \
        --address-prefix 192.168.0.0/16 \
        --subnet-name mySubnet \
        --subnet-prefix 192.168.1.0/24
    ```

1.  Создайте общедоступный IP-адрес, выполнив команду [az network public-ip create](/cli/azure/network/public-ip#az-network-public-ip-create). В следующем примере создается общедоступный IP-адрес *myPublicIP* с DNS-именем *mypublicdns*. (Так как DNS-имя должно быть уникальным, укажите уникальное имя.)

    ```azurecli
    az network public-ip create --resource-group myResourceGroup \
        --location eastus --name myPublicIP --dns-name mypublicdns \
        --allocation-method static --idle-timeout 4
    ```

1.  Создайте сетевую карту, выполнив команду [az network nic create](/cli/azure/network/nic#az-network-nic-create).
    В следующем примере создается сетевая карта *myNic*, подключенная к подсети *mySubnet*.

    ```azurecli
    az network nic create --resource-group myResourceGroup \
        --location eastus --name myNic \
        --vnet-name myVnet --subnet mySubnet \
        --public-ip-address myPublicIP
    ```

## <a name="create-a-vm"></a>Создание виртуальной машины

Создайте виртуальную машину, выполнив команду [az vm create](/cli/azure/vm#az-vm-create).

Укажите скопированный управляемый диск в качестве диска операционной системы (`--attach-os-disk`) следующим образом:

```azurecli
az vm create --resource-group myResourceGroup \
    --name myCopiedVM --nics myNic \
    --size Standard_DS1_v2 --os-type Linux \
    --attach-os-disk myCopiedDisk
```

## <a name="next-steps"></a>Дальнейшие действия

Чтобы узнать, как управлять виртуальной машиной с помощью Azure CLI, прочитайте статью [Команды Azure CLI в режиме Resource Manager](../azure-cli-arm-commands.md).
