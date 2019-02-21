---
title: Краткое руководство. Создание виртуальной сети с помощью Azure CLI
titlesuffix: Azure Virtual Network
description: Из этого краткого руководства вы узнаете, как создать виртуальную сеть с помощью Azure CLI. Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет.
services: virtual-network
documentationcenter: virtual-network
author: jimdial
Customer intent: I want to create a virtual network so that virtual machines can communicate with privately with each other and with the internet.
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: quickstart
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 12/12/2018
ms.author: jdial
ms.openlocfilehash: affbfdebd91d341a4bc32b4a6bc9827919a48817
ms.sourcegitcommit: 9aa9552c4ae8635e97bdec78fccbb989b1587548
ms.translationtype: HT
ms.contentlocale: ru-RU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56427807"
---
# <a name="quickstart-create-a-virtual-network-using-the-azure-cli"></a>Краткое руководство. Создание виртуальной сети с помощью интерфейса командной строки Azure

Виртуальная сеть позволяет ресурсам Azure, таким как виртуальные машины, обмениваться данными в частном порядке и взаимодействовать через Интернет. Из этого краткого руководства вы узнаете, как создать виртуальную сеть. Создав виртуальную сеть, разверните в ней две виртуальные машины. Затем вы подключитесь к виртуальным машинам из Интернета и установите частную связь через новую виртуальную сеть.

Если у вас еще нет подписки Azure, [создайте бесплатную учетную запись](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Если вы решили установить и использовать Azure CLI локально, для выполнения инструкций из этого руководства вам потребуется использовать Azure CLI 2.0.28 или более поздней версии. Выполните команду `az --version`, чтобы узнать установленную версию. Сведения об установке или обновлении Azure CLI см. в [этой статье](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group-and-a-virtual-network"></a>Создание группы ресурсов и виртуальной сети

Перед созданием виртуальной сети необходимо создать группу ресурсов, которая будет содержать эту виртуальную сеть. Создайте группу ресурсов с помощью команды [az group create](/cli/azure/group). В этом примере создается группа ресурсов с именем *myResourceGroup* в расположении *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Создайте виртуальную сеть с помощью команды [az network vnet create](/cli/azure/network/vnet). В этом примере создается виртуальная сеть по умолчанию *myVirtualNetwork* с подсетью *default*:

```azurecli-interactive
az network vnet create \
  --name myVirtualNetwork \
  --resource-group myResourceGroup \
  --subnet-name default
```

## <a name="create-virtual-machines"></a>Создание виртуальных машин

Создайте две виртуальные машины в виртуальной сети.

### <a name="create-the-first-vm"></a>Создание первой виртуальной машины

Создайте виртуальную машину с помощью команды [az vm create](/cli/azure/vm). Команда также создает ключи SSH, если они не существуют в расположении ключей по умолчанию. Чтобы использовать определенный набор ключей, используйте параметр `--ssh-key-value`. Параметр `--no-wait` позволяет создать виртуальную машину в фоновом режиме, чтобы можно было перейти к следующему шагу. В этом примере создается виртуальная машина с именем *myVm1*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm1 \
  --image UbuntuLTS \
  --generate-ssh-keys \
  --no-wait
```

### <a name="create-the-second-vm"></a>Создание второй виртуальной машины

Так как вы использовали параметр `--no-wait` на предыдущем шаге, можно создать вторую виртуальную машину с именем *myVm2*.

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm2 \
  --image UbuntuLTS \
  --generate-ssh-keys
```

### <a name="azure-cli-output-message"></a>Выходное сообщение Azure CLI

Создание виртуальных машин может занять несколько минут. После того как Azure создаст виртуальные машины, Azure CLI вернет выходные данные следующего вида:

```azurecli
{
  "fqdns": "",
  "id": "/subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Compute/virtualMachines/myVm2",
  "location": "eastus",
  "macAddress": "00-0D-3A-23-9A-49",
  "powerState": "VM running",
  "privateIpAddress": "10.0.0.5",
  "publicIpAddress": "40.68.254.142",
  "resourceGroup": "myResourceGroup"
  "zones": ""
}
```

Запишите значение **publicIpAddress**. Этот адрес используется на следующем шаге, чтобы подключиться к виртуальной машине из Интернета.

## <a name="connect-to-a-vm-from-the-internet"></a>Подключение к виртуальной машине из Интернета

В этой команде замените `<publicIpAddress>` общедоступным IP-адресом виртуальной машины *myVm2*:

```bash
ssh <publicIpAddress>
```

## <a name="communicate-between-vms"></a>Взаимодействие между виртуальными машинами

Чтобы проверить частную связь между виртуальными машинами *myVm2* и *myVm1*, введите команду:

```bash
ping myVm1 -c 4
```

Вы получите четыре ответа с адреса *10.0.0.4*.

Завершите сеанс SSH с виртуальной машиной *myVm2*.

## <a name="clean-up-resources"></a>Очистка ресурсов

Чтобы удалить ненужную группу ресурсов и все содержащиеся в ней ресурсы, выполните команду [az group delete](/cli/azure/group):

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Дополнительная информация

Следуя инструкциям в этом кратком руководстве, вы создали виртуальную сеть по умолчанию и две виртуальные машины. Затем вы подключились к одной виртуальной машине из Интернета и установили частную связь между двумя виртуальными машинами. Дополнительные сведения о параметрах виртуальной сети см. в статье об [управлении виртуальной сетью](manage-virtual-network.md).

Azure не накладывает ограничения на частную связь между виртуальными машинами. По умолчанию она разрешает только входящие подключения к удаленному рабочему столу виртуальных машин Windows из Интернета. Дополнительные сведения о разрешении или ограничении различных типов сетевого взаимодействия с виртуальными машинами см. в статье [Руководство. Фильтрация сетевого трафика с помощью групп безопасности сети, используя портал Azure](tutorial-filter-network-traffic.md).
