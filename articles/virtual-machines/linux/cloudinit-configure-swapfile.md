---
title: Используйте облачно-ит для настройки своп-раздела на Linux VM
description: Как использовать облачно-ит для настройки своп-раздела в Linux VM во время создания с Azure CLI
author: rickstercdn
manager: gwallace
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 1247652e536042ee249054d86aed3c3f8e7aa7bf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78969202"
---
# <a name="use-cloud-init-to-configure-a-swap-partition-on-a-linux-vm"></a>Используйте облачно-ит для настройки своп-раздела на Linux VM
В этой статье показано, как использовать [облачно-инат](https://cloudinit.readthedocs.io) для настройки раздела своп на различных дистрибутивах Linux. Своп-раздел традиционно был настроен агентом Linux (WALA), на основе которого требовался один дистрибутив.  В этом документе будет изложен процесс построения раздела свопа по требованию во время подготовки с использованием облачного инита.  Дополнительные сведения о встроенной поддержке cloud-init в Azure и поддерживаемых дистрибутивах Linux см. в [обзоре cloud-init](using-cloud-init.md).

## <a name="create-swap-partition-for-ubuntu-based-images"></a>Создание раздела своп для изображений на основе Ubuntu
По умолчанию на Azure изображения галереи Ubuntu не создают своп-разделов. Для включения конфигурации раздела своп во время подготовки VM с помощью облачно-инат - пожалуйста, смотрите [документ AzureSwapPartitions](https://wiki.ubuntu.com/AzureSwapPartitions) на вики Ubuntu.

## <a name="create-swap-partition-for-red-hat-and-centos-based-images"></a>Создание своп-раздела для изображений на основе Red Hat и CentOS

Создайте файл в текущей оболочке под названием *cloud_init_swappart.txt* и вставьте следующую конфигурацию. Для этого примера создайте файл в Cloud Shell (не на локальном компьютере). Вы можете использовать любой редактор. Введите `sensible-editor cloud_init_swappart.txt`, чтобы создать файл и просмотреть список доступных редакторов. Выберите первый пункт, чтобы использовать редактор **nano**. Убедитесь, что весь файл cloud-init скопирован правильно, особенно первая строка.  

```yaml
#cloud-config
disk_setup:
  ephemeral0:
    table_type: gpt
    layout: [66, [33,82]]
    overwrite: true
fs_setup:
  - device: ephemeral0.1
    filesystem: ext4
  - device: ephemeral0.2
    filesystem: swap
mounts:
  - ["ephemeral0.1", "/mnt"]
  - ["ephemeral0.2", "none", "swap", "sw", "0", "0"]
```

Прежде чем развернуть этот образ, необходимо создать группу ресурсов с помощью команды [az group create](/cli/azure/group). Группа ресурсов Azure является логическим контейнером, в котором происходит развертывание ресурсов Azure и управление ими. В следующем примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Теперь создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm) и укажите файл cloud-init с помощью `--custom-data cloud_init_swappart.txt`, как показано ниже.

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud_init_swappart.txt \
  --generate-ssh-keys 
```

## <a name="verify-swap-partition-was-created"></a>Создана проверка своп-раздела
Подключитесь по протоколу SSH к общедоступному IP-адресу виртуальной машины, который указан в выходных данных предыдущей команды. Введите свой общедоступный IP-адрес в качестве значения для **publicIpAddress**, как показано здесь:

```bash
ssh <publicIpAddress>
```

Как только вы имеете SSH'ed в vm, проверите если раздел своп был создан

```bash
swapon -s
```

Выходные данные этой команды должны выглядеть следующим образом:

```text
Filename                Type        Size    Used    Priority
/dev/sdb2  partition   2494440 0   -1
```

> [!NOTE] 
> Если у вас есть существующее изображение Azure с настройкой перегородки своп и вы хотите изменить конфигурацию раздела своп для новых изображений, следует удалить существующую перегородку. Дополнительные сведения см. в документе "Customize Images to provision by cloud-init" (Настройка образов для подготовки с помощью cloud-init).

## <a name="next-steps"></a>Дальнейшие действия
Дополнительные примеры изменения конфигурации с помощью cloud-init см. в следующих статьях:
 
- [Добавление пользователя Linux к виртуальной машине](cloudinit-add-user.md)
- [Запуск диспетчера пакетов для обновления существующих пакетов при первой загрузке](cloudinit-update-vm.md)
- [Изменение имени локального узла виртуальной машины](cloudinit-update-vm-hostname.md) 
- [Установка пакета приложения, обновление файлов конфигурации и внедрение ключей](tutorial-automate-vm-deployment.md)
