---
title: Работа с VM и NSG в Azure Bastion
description: В этой статье описывается, как включить доступ NSG с Azure Bastion
services: bastion
author: charwen
ms.service: bastion
ms.topic: conceptual
ms.date: 02/03/2020
ms.author: charwen
ms.openlocfilehash: 15abee4688a2f6aefa2b08ad2b8eee6622d56be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: ru-RU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77087273"
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

## <a name="network-security-groups"></a><a name="nsg"></a>Группы сетевой безопасности

В этом разделе отображается сетевой трафик между пользователем и Azure Bastion, а также целевой виртуальный трафик в виртуальной сети:

### <a name="azurebastionsubnet"></a>AzureUreBastionСубнет

Azure Bastion развернут специально для AzureBastionSubnet.

* **Входный трафик:**

   * **Вход трафик из общественного интернета:** Azure Bastion создаст общедоступный IP-адрес, который нуждается в включенном в открытый IP-адрес е-ip для входа трафика. Порт 3389/22 НЕ требуется открывать на AzureBastionSubnet.
   * **Вход ный трафик с плоскости управления Azure Bastion:** Для управления подключением самолета включите порт 443 из тега **службы GatewayManager.** Это позволяет плоскости управления, то есть диспетчеру шлюза, иметь возможность говорить с Azure Bastion.

* **Движение на вход:**

   * **Egress трафик для целевой VMs:** Azure Bastion достигнет целевого ВМ через частный IP. НСК необходимо разрешить трафик на другие целевые подсети VM для портов 3389 и 22.
   * **Движение на вход в другие общедоступные конечные точки azure:** Azure Bastion должен быть в состоянии подключиться к различным общедоступным конечным точкам в Azure (например, для хранения журналов диагностики и журналов учета). По этой причине Azure Bastion необходимо перейти на 443 в тег сервиса **AzureCloud.**

* **Целевая подсеть VM:** Это подсеть, которая содержит целевую виртуальную машину, к которой вы хотите rdP/SSH.

   * **Входный трафик из Лазурного бастиона:** Azure Bastion достигнет цели VM через частный IP. Порты RDP/SSH (порты 3389/22 соответственно) должны быть открыты на целевой стороне VM над частным IP. В качестве наилучшей практики в этом правиле можно добавить диапазон IP-адресов Azure Bastion Subnet, чтобы только Bastion мог открывать эти порты на целевых ВМ в подсети VM.

## <a name="apply-nsgs-to-azurebastionsubnet"></a><a name="apply"></a>Применение НСГ в AzureUreBastionSubnet

Если вы создаете и применяете NSG в ***AzureBastionSubnet,*** убедитесь, что вы добавили следующие правила в свой NSG. Если вы не добавите эти правила, создание/обновление NSG не удастся:

* **Управление подключением самолета:** Входящий на 443 от GatewayManager
* **Диагностика лесозаготовок и другие:** Выгоняемый на 443 в AzureCloud. Региональные теги в этом теге обслуживания пока не поддерживаются.
* **Целевой VM:** Выход для 3389 и 22 в VirtualNetwork

Пример правила NSG доступен для справки в этом [шаблоне quickstart.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-azure-bastion-nsg)

## <a name="next-steps"></a>Дальнейшие действия

Для получения дополнительной информации о [FAQ](bastion-faq.md)Бастионе Azure см.
