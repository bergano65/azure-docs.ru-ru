---
title: Создайте узел Bastion с помощью Azure CLI Лазурный бастион
description: В этой статье узнайте, как создать и удалить хост Bastion
services: bastion
author: mialdrid
ms.service: bastion
ms.topic: conceptual
ms.date: 03/26/2020
ms.author: mialdrid
ms.openlocfilehash: f98c965ad3b776f3688a716ba28b5367a00c9119
ms.sourcegitcommit: bc738d2986f9d9601921baf9dded778853489b16
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80619225"
---
# <a name="create-an-azure-bastion-host-using-azure-cli"></a>Создание хоста Azure Bastion с помощью Azure CLI

В этой статье показано, как создать хост Azure Bastion с помощью Azure CLI. После предоставления службы Azure Bastion в виртуальной сети всем виртуальным сетям доступна бесшовная служба RDP/SSH. Бастион Azure развертывается на уровне виртуальной сети, а не подписки, учетной записи или виртуальной машины.

Дополнительно можно создать хост Azure Bastion с помощью [портала Azure](bastion-create-host-portal.md)или с помощью [Azure PowerShell.](bastion-create-host-powershell.md)

## <a name="before-you-begin"></a>Перед началом

Убедитесь в том, что у вас уже есть подписка Azure. Если у вас еще нет подписки на Azure, вы можете активировать льготы для [абонента MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details) или подписаться на [бесплатную учетную запись.](https://azure.microsoft.com/pricing/free-trial)

[!INCLUDE [cloudshell cli](../../includes/vpn-gateway-cloud-shell-cli.md)]

## <a name="create-a-bastion-host"></a><a name="createhost"></a>Создание узла-бастиона

Этот раздел поможет создать новый ресурс Azure Bastion с помощью Azure CLI.

1. Создайте виртуальную сеть и подсеть Azure Bastion. Необходимо создать подсеть Azure Bastion, используя значение названия **AzureBastionSubnet.** Это значение позволяет Azure знать, в какую подсеть развертывать ресурсы Bastion. Это отличается от подсети Gateway. Вы должны использовать подсеть, по крайней мере /27 или больше подсети (/27, /26, и так далее). Создайте **AzureBastionSubnet** без каких-либо таблиц маршрутов или делегаций. Если вы используете группы сетевой безопасности в **AzureBastionSubnet,** обратитесь к статье [«Работа с НС».](bastion-nsg.md)

   ```azurecli-interactive
   az network vnet create -g MyResourceGroup -n MyVnet --address-prefix 10.0.0.0/16 --subnet-name AzureBastionSubnet  --subnet-prefix 10.0.0.0/24
   ```

2. Создайте общедоступный IP-адрес для Azure Bastion. Публичным IP является публичный IP-адрес ресурса «Бастион», на котором будет доступна rdP/SSH (над портом 443). Общественный IP-адрес должен находиться в том же регионе, что и создаваемый ресурсом Bastion.

   ```azurecli-interactive
   az network public-ip create -g MyResourceGroup -n MyIp
   ```

3. Создайте новый ресурс Azure Bastion в AzureBastionSubnet вашей виртуальной сети. Создание и развертывание ресурса «Бастион» занимает около 5 минут.

   ```azurecli-interactive
   az network bastion create --name $name--public-ip-address $publicip--resource-group $RgName --vnet-name $VNetName --location $location
                           
   ```

## <a name="next-steps"></a>Следующие шаги

* Ознакомьте [часто задаваемые вопросы «Бастион»](bastion-faq.md) для получения дополнительной информации.

* Для использования групп сетевой безопасности с подсетью Azure Bastion [см.](bastion-nsg.md)
