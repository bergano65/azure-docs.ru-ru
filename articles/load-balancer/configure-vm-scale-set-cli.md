---
title: Настройка виртуального набора масштабов машин с существующим балансом загрузки Azure - Azure CLI
description: Узнайте, как настроить виртуальный набор масштабов машин с существующим балансом загрузки Azure.
author: asudbring
ms.author: allensu
ms.service: load-balancer
ms.topic: article
ms.date: 03/25/2020
ms.openlocfilehash: a7f44a21dd404c556d6f3d8444fa70583cd71c57
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80349737"
---
# <a name="configure-a-virtual-machine-scale-set-with-an-existing-azure-load-balancer-using-the-azure-cli"></a>Настроили виртуальный набор масштабов машины с существующим балансом загрузки Azure с помощью Azure CLI

В этой статье вы узнаете, как настроить виртуальный набор масштабов машины с существующим балансом загрузки Azure. 

## <a name="prerequisites"></a>Предварительные требования

- Подписка Azure.
- Существующий стандартный балансер загрузки sku в подписке, где будет развернут виртуальный набор масштабов машины.
- Виртуальная сеть Azure для набора виртуальной машины.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Если вы решили использовать CLI локально, эта статья требует, чтобы у вас была версия версии Azure CLI 2.0.28 или более поздняя установка. Чтобы узнать версию, выполните команду `az --version`. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI 2.0]( /cli/azure/install-azure-cli).

## <a name="sign-in-to-azure-cli"></a>Вход в Azure CLI

Впишитесь в Azure.

```azurecli-interactive
az login
```

## <a name="deploy-a-virtual-machine-scale-set-with-existing-load-balancer"></a>Развертывание виртуального набора масштабов машины с существующим балансером нагрузки

Замените значения в скобках именами ресурсов в конфигурации.

```azurecli-interactive
az vmss create \
    --resource-group <resource-group> \
    --name <vmss-name>\
    --image <your-image> \
    --admin-username <admin-username> \
    --generate-ssh-keys  \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name <virtual-network-name> \
    --subnet <subnet-name> \
    --lb <load-balancer-name> \
    --backend-pool-name <backend-pool-name>
```

Ниже приведен пример развертывает виртуальную шкалу машины, установленную с:

- Виртуальный набор масштабов машины под названием **myVMSS**
- Балансизатор загрузки Azure назвал **myLoadBalancer**
- Нагрузка балансера бэкэнд бассейн под названием **myBackendPool**
- Виртуальная сеть Azure под названием **myVnet**
- Поднет под названием **mySubnet**
- Ресурсная группа под названием **myResourceGroup**
- Изображение Ubuntu Server для набора виртуальной машины

```azurecli-interactive
az vmss create \
    --resource-group myResourceGroup \
    --name myVMSS \
    --image Canonical:UbuntuServer:18.04-LTS:latest \
    --admin-username adminuser \
    --generate-ssh-keys \
    --upgrade-policy-mode Automatic \
    --instance-count 3 \
    --vnet-name myVnet\
    --subnet mySubnet \
    --lb myLoadBalancer \
    --backend-pool-name myBackendPool
```
> [!NOTE]
> После создания набора масштабов порт бэкэнда не может быть изменен для правила балансировки нагрузки, используемого зондом работоспособности балансоилира нагрузки. Чтобы изменить порт, можно удалить зонд работоспособности, обновив набор виртуальной машины Azure, обновить порт, а затем настроить зонд работоспособности снова.

## <a name="next-steps"></a>Дальнейшие действия

В этой статье развернут виртуальный набор масштабов машин с существующим балансом загрузки Azure.  Чтобы узнать больше о виртуальных наборах масштабов машин и балансерере нагрузки, см.:

- [Что такое Azure Load Balancer?](load-balancer-overview.md)
- [Что такое масштабируемый набор виртуальных машин?](../virtual-machine-scale-sets/overview.md)
                                