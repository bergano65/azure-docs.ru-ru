---
title: Создание узла бастиона с помощью Azure CLI | Azure бастиона
description: В этой статье вы узнаете, как создать и удалить узел бастиона.
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: how-to
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: e7f80bb7f9be2e01aa24090d7305b1a5d882da04
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85255521"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Создание узла бастиона Azure с помощью Azure CLI

В этой статье показано, как создать узел бастиона для Azure с помощью Azure CLI. Когда вы подготавливаете службу Azure бастиона в виртуальной сети, работа по протоколу RDP и SSH доступна для всех виртуальных машин в той же виртуальной сети. Бастион Azure развертывается на уровне виртуальной сети, а не подписки, учетной записи или виртуальной машины.

При необходимости можно создать узел бастиона для Azure с помощью [портал Azure](bastion-create-host-portal.md)или [Azure PowerShell](bastion-create-host-powershell.md).

## <a name="before-you-begin"></a>Перед началом

Убедитесь в том, что у вас уже есть подписка Azure. Если у вас еще нет подписки Azure, вы можете активировать преимущества для [подписчиков MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или зарегистрироваться для использования [бесплатной учетной записи](https://azure.microsoft.com/pricing/free-trial).

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Создание узла-бастиона

Этот раздел поможет вам создать новый ресурс Azure бастиона с помощью Azure CLI.

1. Создайте виртуальную сеть и подсеть бастиона для Azure. Необходимо создать подсеть бастиона для Azure, используя значение Name **азуребастионсубнет**. По этому значению Azure сможет определить, в какую подсеть следует развертывать ресурсы типа "Бастион". Она отличается от подсети шлюза. Необходимо использовать подсеть как минимум/27 или более крупную подсеть (/27,/26 и т. д.). Создайте **азуребастионсубнет** без таблиц или делегирований маршрутов. Если вы используете группы безопасности сети в **азуребастионсубнет**, см. статью [Работа с группы безопасности сети](bastion-nsg.md) .

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Создайте общедоступный IP-адрес для Azure бастиона. Общедоступные IP-адреса — это общедоступный IP-адрес ресурса бастиона, к которому будут обращаться протоколы RDP/SSH (через порт 443). Общедоступный IP-адрес должен находиться в том же регионе, что и создаваемый ресурс типа "Бастион".

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp --sku Standard
   ```

3. Создайте новый ресурс Azure бастиона в Азуребастионсубнет виртуальной сети. Создание и развертывание ресурса бастиона занимает около 5 минут.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Дальнейшие шаги

* Дополнительные сведения см. в статье [вопросы и ответы по бастиона](bastion-faq.md) .

* Сведения об использовании групп безопасности сети с подсетью Бастиона Azure см. в статье [Работа с группами безопасности сети](bastion-nsg.md).
