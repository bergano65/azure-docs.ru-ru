---
title: Работа с VM и NSG в Azure Bastion
description: В этой статье описывается, как включить доступ NSG с Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 04/20/2020
ms.author: charwen
ms.openlocfilehash: 0188f9bc1c7c0e8d7fed9f590d078085b175614f
ms.sourcegitcommit: ffc6e4f37233a82fcb14deca0c47f67a7d79ce5c
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81732193"
---
# <a name="working-with-nsg-access-and-azure-bastion"></a>Работа с доступом NSG и Azure Bastion

При работе с Azure Bastion можно использовать группы сетевой безопасности (НСГ). Для получения дополнительной информации [см.](../virtual-network/security-overview.md) 

![Architecture](./media/bastion-nsg/nsg-architecture.png)

На этой схеме:

* Хозяин Bastion развертывается в виртуальной сети.
* Пользователь подключается к порталу Azure с помощью любого браузера HTML5.
* Пользователь переходит на виртуальную машину Azure в RDP/SSH.
* Подключение Интеграция - Сеанс RDP/SSH с одним нажатием кнопки внутри браузера
* На виртуальной машине Azure не требуется общедоступный IP-адрес.

## <a name="network-security-groups"></a><a name="nsg"></a>Группы безопасности сети

В этом разделе отображается сетевой трафик между пользователем и Azure Bastion, а также целевой виртуальный трафик в виртуальной сети:

### <a name="azurebastionsubnet"></a><a name="apply"></a>AzureUreBastionСубнет

Azure Bastion развернут специально для ***AzureBastionSubnet***.

* **Входный трафик:**

   * **Вход трафик из общественного интернета:** Azure Bastion создаст общедоступный IP-адрес, который нуждается в включенном в открытый IP-адрес е-ip для входа трафика. Порт 3389/22 НЕ требуется открывать на AzureBastionSubnet.
   * **Вход ный трафик с плоскости управления Azure Bastion:** Для управления подключением самолета включите порт 443 из тега **службы GatewayManager.** Это позволяет плоскости управления, то есть диспетчеру шлюза, иметь возможность говорить с Azure Bastion.

* **Движение на вход:**

   * **Egress трафик для целевой VMs:** Azure Bastion достигнет целевого ВМ через частный IP. НСК необходимо разрешить трафик на другие целевые подсети VM для портов 3389 и 22.
   * **Движение на вход в другие общедоступные конечные точки azure:** Azure Bastion должен быть в состоянии подключиться к различным общедоступным конечным точкам в Azure (например, для хранения журналов диагностики и журналов учета). По этой причине Azure Bastion необходимо перейти на 443 в тег сервиса **AzureCloud.**

### <a name="target-vm-subnet"></a>Целевая подсеть VM
Это подсеть, которая содержит целевую виртуальную машину, к которой вы хотите rdP/SSH.

   * **Входный трафик из Лазурного бастиона:** Azure Bastion достигнет цели VM через частный IP. Порты RDP/SSH (порты 3389/22 соответственно) должны быть открыты на целевой стороне VM над частным IP. В качестве наилучшей практики в этом правиле можно добавить диапазон IP-адресов Azure Bastion Subnet, чтобы только Bastion мог открывать эти порты на целевых ВМ в подсети VM.


## <a name="next-steps"></a>Следующие шаги

Для получения дополнительной информации о [FAQ](bastion-faq.md)Бастионе Azure см.
