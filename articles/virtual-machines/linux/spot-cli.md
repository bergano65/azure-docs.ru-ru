---
title: Развертывание виртуальных машин точки Azure с помощью интерфейса командной строки
description: Узнайте, как с помощью интерфейса командной строки развертывать виртуальные машины в машинном обучении Azure для экономии затрат.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/26/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 9a06dea64ffb348477308979fa07c0e2ce822b0c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91825404"
---
# <a name="deploy-spot-vms-using-the-azure-cli"></a>Развертывание плашечных виртуальных машин с помощью Azure CLI

[Виртуальные машины Azure для машинного](spot-vms.md) размещения позволяют использовать преимущества неиспользуемой емкости с значительной экономией затрат. В любой момент времени, когда эта емкость становится нужна, инфраструктура Azure вытесняет точечные виртуальные машины. Это означает, что точечные виртуальные машины прекрасно подходят для рабочих нагрузок, для которых допустимы прерывания, например для заданий пакетной обработки, сред разработки или тестирования, больших вычислительных рабочих нагрузок и других.

Цена на точечные виртуальные машины может изменяться в зависимости от региона и ценовой категории. Дополнительные сведения см. на страницах с информацией о ценах на виртуальные машины [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) и [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/windows/). 

Вы можете задать максимальную цену, которую вы хотите платить, в час, для виртуальной машины. Максимальная цена на плашечную виртуальную машину можно установить в долларах США (USD), используя до 5 десятичных разрядов. Например, значение `0.98765` определяет максимальную цену 0,98765 долларов США в час. Если вы укажете для максимальной цены значение `-1`, виртуальная машине не будет вытесняться по критерию цены. Цена на виртуальную машину будет соответствовать текущей цене или цене для стандартной виртуальной машины, которая когда-либо будет меньше, если есть доступная емкость и квота. Дополнительные сведения о настройке максимальной цены см. в разделе [точки на виртуальных машинах — цены](../spot-vms.md#pricing).

Процесс создания виртуальной машины с использованием точки, использующей Azure CLI, аналогичен описанному в [статье Краткое руководство](./quick-create-cli.md). Просто добавьте параметр "--Priority плашка", установите для параметра значение " `--eviction-policy` освобождение" (по умолчанию) или `Delete` и укажите максимальную цену или `-1` . 


## <a name="install-azure-cli"></a>Установка Azure CLI

Чтобы создать плашечные виртуальные машины, необходимо запустить Azure CLI версии 2.0.74 или более поздней. Чтобы узнать версию, выполните команду **az --version**. Если вам необходимо выполнить установку или обновление, см. статью [Установка Azure CLI](/cli/azure/install-azure-cli). 

Затем войдите в Azure с помощью команды [az login](/cli/azure/reference-index#az-login).

```azurecli
az login
```

## <a name="create-a-spot-vm"></a>Создание плашечной виртуальной машины

В этом примере показано, как развернуть плашечную виртуальную машину Linux, которая не будет исключена на основе цены. Политика вытеснения устанавливается таким образом, чтобы освободить виртуальную машину, чтобы ее можно было перезапустить позднее. Если вы хотите удалить виртуальную машину и базовый диск при удалении виртуальной машины, задайте для параметра значение `--eviction-policy` `Delete` .

```azurecli
az group create -n mySpotGroup -l eastus
az vm create \
    --resource-group mySpotGroup \
    --name myVM \
    --image UbuntuLTS \
    --admin-username azureuser \
    --generate-ssh-keys \
    --priority Spot \
    --max-price -1 \
    --eviction-policy Deallocate
```



После создания виртуальной машины можно выполнить запрос, чтобы увидеть максимальную стоимость выставления счетов для всех виртуальных машин в группе ресурсов.

```azurecli
az vm list \
   -g mySpotGroup \
   --query '[].{Name:name, MaxPrice:billingProfile.maxPrice}' \
   --output table
```

## <a name="simulate-an-eviction"></a>Имитация вытеснения

Вы можете [имитировать вытеснение](/rest/api/compute/virtualmachines/simulateeviction) для ПЛАШЕЧНОЙ виртуальной машины, чтобы проверить, насколько хорошо ваше приложение будет ответил внезапного вытеснения. 

Замените следующие сведения: 

- `subscriptionId`
- `resourceGroupName`
- `vmName`


```http
POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Compute/virtualMachines/{vmName}/simulateEviction?api-version=2020-06-01
```

**Следующие шаги**

Можно также создать плашечную виртуальную машину с помощью [Azure PowerShell](../windows/spot-powershell.md), [портала](../spot-portal.md)или [шаблона](spot-template.md).

Запросите актуальные сведения о ценах с помощью [API розничных цен Azure](/rest/api/cost-management/retail-prices/azure-retail-prices) для получения сведений о ценах. Объект `meterName` и `skuName` будет содержать `Spot` .

Если возникает ошибка, см. раздел [коды ошибок](../error-codes-spot.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
